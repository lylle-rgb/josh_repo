# Fleet Research — Josh / Heather Schwartz — Morning Scan

**Scan Date:** 2026-05-15 (Morning — Day 28)  
**Agent:** AlphaClaw Apex Fleet Research Agent  
**Instance:** Josh — Discord bot Heather Schwartz (personal assistant: iMessage, email, calendar, contacts)  
**Previous findings:** `findings-2026-05-14-evening.md` (Day 27 Evening). All prior findings remain unresolved.

---

## Platform News (New Since Day 27 Evening Scan)

### v2026.5.7 Still Current Stable — v2026.5.10 Expected This Week

No new stable release overnight. Beta series is at v2026.5.12-beta.3. v2026.5.10 stable is still expected before end of this week. Josh's version gap holds at **13 releases** (2026.3.22 → 2026.5.7). When 5.10 ships, the gap will be 14+ releases.

---

### CRITICAL UPDATE WARNING: Config-Wipe Bug in Certain Version Ranges

GitHub issue #65105 documents a confirmed bug: **updating OpenClaw through versions 2026.4.9 → 4.10 → 4.11 silently destroys the entire `channels.discord` config block and `agents.list` array** in `openclaw.json`. Josh's current version (2026.3.22) means an update to 2026.5.7 could pass through this version range depending on how the updater packages incremental migrations.

**Before updating**, take a complete backup of `openclaw.json`. The Discord channel config, guild settings, and `requireMention: false` will need to be verified intact post-update. If Heather goes silent after an update, this config wipe is the first thing to check.

**Risk level:** HIGH — pre-update backup is a zero-cost action that prevents a complete Discord integration failure post-update.

---

### Soft Restart (`--soft`) — Preserves Discord Channel Bindings Post-Update

OpenClaw's soft restart capability (now default) tears down the active agent session, preserves channel bindings + auth profiles + workspace state, restarts the agent's provider, and resumes on the next inbound message. This means routine restarts (including those triggered by updates) should not lose Discord channel bindings or guild configuration — **assuming the config-wipe bug above does not trigger during the update**.

For Josh: the `"requireMention": false` guild setting (which prevents Heather from requiring `@Heather` mentions) is in the channels block that the config-wipe bug can destroy. The soft restart itself is safe; the update migration path is the risk.

---

### Gemini 3 Flash Preview — Performance Specs Confirmed

Morning research confirms exact performance specs for Josh's primary model (`google/gemini-3-flash-preview`):

| Metric | Gemini 3 Flash Preview | Gemini 2.5 Flash (old default) |
|---|---|---|
| Context window | **1M tokens** | 128K tokens |
| Output limit | **66K tokens** | 8K tokens |
| Output speed | **380 tokens/s** | ~150 tokens/s |
| Speed vs 2.5 Flash | **2.5x faster** | baseline |
| SWE-bench Verified | **78%** | ~45% |
| Reasoning levels | Configurable (thinking budget) | None |
| Multi-modal | Text, images, audio, video, PDFs | Text + images |

**What this means for Heather:** Josh's personal assistant is running on a model that has 8x the context window of the previous generation. Long email threads, calendar history, and iMessage conversation archives can all fit in a single context. The 380 tok/s output speed means faster replies. The 78% SWE-bench score means better tool-use reliability (calendar reads, email drafts, contact lookups).

**Caveat from research:** At full 1M context, the model can lose track of system instructions. Once Heather's memory corpus grows (post memory-core activation), keeping workspace files concise becomes more important.

**Status:** Josh is already benefiting from Gemini 3 Flash capabilities. The remaining gap is that v2026.5.7's Gemini 3 tool-call thought-signature replay fix is not yet applied (Finding 41, Day 27 Evening).

---

### Gemini 3.1 Flash-Lite Preview — Newer, Lighter Model Available

Morning research surfaces `google/gemini-3.1-flash-lite-preview` — a lighter variant of Gemini 3 that has shipped since `gemini-3-flash-preview`. It offers a **2.5x speed boost** over Gemini 3 Flash Preview at lower cost, with some capability tradeoff on deep reasoning tasks.

**Relevance to Josh:** For Heather's daily-driver tasks (email drafts, calendar checks, contact lookups, iMessage monitoring), the lite model may be sufficient and faster/cheaper. For complex multi-step planning tasks, the full Flash is better. Worth evaluating as a fallback option:

```json
"fallbacks": [
  "openrouter/google/gemini-3.1-flash-lite-preview",
  "openrouter/google/gemini-2.5-flash",
  "openrouter/anthropic/claude-haiku-4-5"
]
```

This replaces the stale `claude-3.5-haiku` fallback (Finding 10, Day 17 — still unresolved) and adds the new lite model as a faster, cheaper first fallback.

**Risk level:** LOW — this is a model config change that can be reverted immediately if Heather's performance degrades.

---

### Cron Retry — Exact Backoff Numbers (Config-Ready)

The prior evening scans referenced retry-aware cron without the exact configuration. Morning research confirms the precise values from OpenClaw's cron documentation:

**How it works:** After consecutive cron errors, OpenClaw applies exponential backoff:
- Attempt 1: immediate
- After 1st failure: 30 seconds
- After 2nd failure: 1 minute
- After 3rd failure: 5 minutes
- After 4th failure: 15 minutes
- After 5th+ failure: 60 minutes

Backoff resets automatically after the next successful run. After initial + 2 `LiveSessionModelSwitchError` retries, cron aborts rather than looping indefinitely.

**Config for Josh's `openclaw.json` (add to root level):**
```json
"cron": {
  "retry": {
    "maxAttempts": 3,
    "backoffMs": [30000, 60000, 300000, 900000, 3600000],
    "retryOn": ["rate_limit", "overloaded", "network", "server_error"]
  }
}
```

**Blocked by:** Heather has no active cron/heartbeat configured. This config is ready to add but delivers no value until HEARTBEAT.md is populated and cron tasks are wired up. Still correct to add proactively so it's in place when heartbeat is activated.

**Risk level:** MEDIUM — becomes critical once any automated task is running.

---

### Discord Issue #77254 — Outbound Delivery Loses Adapter After Plugin Registry Reload

A recently documented bug: Discord outbound message delivery loses its adapter after a plugin registry reload (triggered by skill installs or plugin updates). Symptoms: Heather stops responding to Discord messages or sends messages to the wrong channel after a plugin change.

**Mitigation:** After any skill install or plugin change, verify Discord delivery with a test message before assuming changes are live. The soft restart (`--soft`) resolves this if it occurs.

**Risk level:** LOW — relevant only during/after plugin changes, not day-to-day operation.

---

## New Findings — Josh Instance (Day 28 Morning)

### Finding 45. Config-Wipe Bug — Backup openclaw.json Before Any Update
**Risk: HIGH | Days pending: NEW**

GitHub issue #65105 confirms a bug that silently wipes the `channels.discord` block and `agents.list` from `openclaw.json` when updating through certain version ranges. Josh's update path (2026.3.22 → 2026.5.7) may pass through affected versions.

**Pre-update action (zero cost):**
1. Download current `openclaw.json` from the AlphaClaw Apex dashboard or the GitHub repo
2. Save a timestamped backup: `openclaw-backup-2026-05-15.json`
3. Post-update: verify Discord channel config and `requireMention: false` are intact
4. If Heather goes silent after update: restore from backup and re-apply new settings manually

This is the most important pre-condition for the update that has been pending 28 days.

**Risk level:** HIGH — without backup, a config wipe means complete Discord reconfiguration from scratch.

---

### Finding 46. Gemini 3.1 Flash-Lite Preview — Faster/Cheaper Fallback Option
**Risk: LOW | Days pending: NEW**

`google/gemini-3.1-flash-lite-preview` is now available as a 2.5x faster, lower-cost model than current `gemini-3-flash-preview`. For Heather's daily-driver workloads (email, calendar, contacts, iMessage), the lite model is likely sufficient. For complex multi-step tasks, primary model stays as full Flash.

**Recommended fallback update (post-update, replaces retired haiku):**
```json
"fallbacks": [
  "openrouter/google/gemini-3.1-flash-lite-preview",
  "openrouter/google/gemini-2.5-flash",
  "openrouter/anthropic/claude-haiku-4-5"
]
```

**Risk level:** LOW — improve fallback chain quality and fix retired model in one change.

---

### Finding 47. Cron Retry Backoff — Ready to Add Today
**Risk: MEDIUM | Days pending: NEW (config detail)**

Exact cron retry backoff values confirmed: 30s, 1m, 5m, 15m, 60m. Config snippet ready (see above). Delivers no value today (no active cron) but should be added proactively so heartbeat tasks are protected from Day 1.

**Risk level:** MEDIUM — not urgent today, but zero-cost to add now.

---

## Persistent High-Priority Items — Day 28 Morning Summary

**Version gap: 2026.3.22 → 2026.5.7 = 13 releases, 85 days.**  
**28 consecutive days with zero implementations.**  
**workspace/memory: still empty (2 files, no daily logs, no MEMORY.md, no heartbeat-state.json).**  
**TOOLS.md: confirmed empty template — 28 days.**  
**HEARTBEAT.md: 168 bytes — effectively empty.**

| Finding | Severity | Days Pending | Status |
|---|---|---|---|
| Config wipe bug — backup before updating | HIGH | NEW | ⬜ Do before update |
| OpenClaw 13 releases outdated | HIGH | 28 | ⬜ Pending |
| memory-core plugin missing entirely | HIGH | 4 | ⬜ Pending |
| workspace/memory empty — no daily logs | HIGH | 28 | ⬜ Pending |
| iMessage monitoring dark (~April 26) | HIGH | 20 | ⬜ Pending |
| HEARTBEAT.md effectively empty (168 bytes) | HIGH | 28 | ⬜ Pending |
| Pre-compaction flush not configured | MEDIUM | 4 | ⬜ Pending |
| MEMORY.md never created | MEDIUM | 28 | ⬜ Pending |
| SOUL.md never evolved | MEDIUM | 28 | ⬜ Pending |
| No-emoji rule not in SOUL.md | MEDIUM | 28 | ⬜ Pending |
| TOOLS.md empty template (28 days) | MEDIUM | 28 | ⬜ Pending |
| inbox-state.json malformed + stale | MEDIUM | 28 | ⬜ Pending |
| AGENTS.md not customized (matches Noah's) | MEDIUM | 28 | ⬜ Pending |
| Gemini 3 tool-call fixes — version-gated | MEDIUM | 1 | ⬜ Post-update |
| Gemini 3.1 Flash-Lite as fallback | LOW | NEW | ⬜ Post-update |
| Retired claude-3.5-haiku fallback | LOW | 11 | ⬜ Pending |
| Discord streaming off | LOW | 28 | ⬜ Pending |
| IDENTITY.md avatar blank | LOW | 4 | ⬜ Pending |
| ElevenLabs v3 TTS — no voice preference | LOW | 1 | ⬜ Post-update |
| Auto-reply authorization hooks | LOW | 1 | ⬜ Post-update |
| threadBindings — multi-agent Discord | MEDIUM | 4 | ⬜ Post-update |
| Retry-aware cron (exact config ready) | MEDIUM | NEW | ⬜ Ready to add today |
| workspace/reports/ missing | LOW | 2 | ⬜ Pending |
| memory-lancedb-pro upgrade path | LOW | 2 | ⬜ Post-memory-core |
| v2026.5.10 stable — monitor | OPPORTUNITY | monitoring | ⬜ Expected this week |
| /context map — token visibility | OPPORTUNITY | 4 | ⬜ Post-5.10 |
| A2A 20-turn sub-agent delegation | OPPORTUNITY | 4 | ⬜ Post-5.10 |
| Per-agent tool overrides (Discord) | OPPORTUNITY | 4 | ⬜ Post-5.10 |

**Revised implementation order (incorporating Day 28 pre-update safety step):**
1. **TODAY — Zero config:** Send memory bootstrap message to Heather in Discord
2. **TODAY — Zero config:** Send TOOLS.md populate message
3. **Before update:** Backup `openclaw.json` (Finding 45)
4. Update OpenClaw to 2026.5.7 — unlock Gemini 3 fixes, ElevenLabs v3, auth hooks
5. Verify Discord channel config intact post-update (check for config-wipe bug)
6. Add memory-core to `plugins.allow` + `plugins.entries` + compaction config
7. Fix fallback chain: replace retired haiku, add gemini-3.1-flash-lite (Finding 46)
8. Add cron retry config (Finding 47)
9. Populate HEARTBEAT.md with email/calendar/iMessage check schedule
10. Tell Heather to create `workspace/reports/` directory
11. Enable threadBindings, monitor for 2026.5.10 → memory-lancedb-pro evaluation

---
*Generated by AlphaClaw Apex Fleet Research Agent — Morning Scan — 2026-05-15 (Day 28)*
