# Fleet Research — Josh / Heather Schwartz — Morning Scan (Run 2)

**Scan Date:** 2026-05-15 (Morning Run 2 — Day 28)  
**Agent:** AlphaClaw Apex Fleet Research Agent  
**Instance:** Josh — Discord bot Heather Schwartz (personal assistant: iMessage, email, calendar, contacts)  
**Previous:** `findings-2026-05-15-morning.md` (Day 28 Morning Run 1). All prior findings remain unresolved.

This document captures items surfaced in a second research pass on the same morning, with fresh search queries covering file transfer, heartbeat optimization, watchdog alerting, and platform-specific tooling for Josh's use case.

---

## New Platform Intelligence (Run 2 Research)

### File Transfer Plugin (v2026.5.3) — Four New Agent Tools

v2026.5.3 (part of the 13-release gap since Josh's 2026.3.22) ships a **file transfer plugin** with four dedicated agent tools:

| Tool | Function |
|---|---|
| `file_fetch` | Fetch a file from a remote paired node |
| `dir_list` | List contents of a directory on paired node |
| `dir_fetch` | Recursively fetch a directory from paired node |
| `file_write` | Write a file to paired node |

**16 MB per-round-trip ceiling.** Binary operations supported.

**Why this matters for Heather:**
- `file_fetch` enables retrieving email attachments from paired storage without manual intervention
- `dir_list` can audit the workspace directory structure (resolves the chronic "TOOLS.md is empty" problem — Heather could self-inventory her environment)
- `file_write` enables saving calendar exports, email digests, and memory files back to a paired node
- This is the infrastructure prerequisite for any file-based automation Heather performs

**Blocked by:** Updating to 2026.5.3+. No additional config required — plugin loads automatically post-update if allowed.

**Risk level:** LOW — available post-update, no config change.

---

### OpenRouter Response Caching (v2026.5.4) — Cost Reduction on Fallback Calls

v2026.5.4 adds **opt-in response caching** via `X-OpenRouter-Cache` headers for requests routed through OpenRouter.

**Josh's OpenRouter fallbacks:**
```json
"fallbacks": [
  "openrouter/google/gemini-2.5-flash",
  "openrouter/anthropic/claude-3.5-haiku"  // ⚠️ retired
]
```

When Heather's primary (Gemini 3 Flash Preview) is unavailable or rate-limited, she falls back to OpenRouter. Repeated queries with similar contexts (email drafts for the same thread, calendar lookups for the same event window) would cache at OpenRouter, reducing latency and cost on fallback calls.

**After updating, add to `agents.defaults`:**
```json
"openrouter": {
  "cacheControl": true
}
```

**Risk level:** LOW — opt-in, only affects OpenRouter-routed fallback calls.

---

### Heartbeat lightContext + isolatedSession — Token Cost Reduction

Community best practices from OpenClaw automation docs surface two heartbeat flags not yet used by either instance:

```json
"heartbeat": {
  "lightContext": true,
  "isolatedSession": true
}
```

**What they do:**
- `lightContext: true` — strips conversation history from the heartbeat context window; the heartbeat only gets the HEARTBEAT.md checklist and current session state. Dramatically reduces token burn for routine checks.
- `isolatedSession: true` — runs the heartbeat turn in an isolated session, preventing heartbeat context from polluting the main conversation history.

**Why this matters for Heather:** Heather has an empty HEARTBEAT.md and no configured heartbeat schedule. When heartbeat is activated (required pre-condition for proactive email/calendar monitoring), these flags should be set from Day 1 to avoid context bloat on every 30-minute check cycle.

**Config (add to heartbeat/cron config when activating):**
```json
"cron": {
  "heartbeat": {
    "lightContext": true,
    "isolatedSession": true,
    "schedule": "*/30 * * * *"
  }
}
```

**Risk level:** LOW — reduces cost, no capability loss.

---

### AlphaClaw Watchdog Crash Notifications

AlphaClaw's watchdog system (platform-level, not in `openclaw.json`) includes:
- Crash-loop detection + automatic repair
- Auto-restart after failure
- **Discord and Telegram notifications for crash events**

**For Josh:** If Heather crashes or enters a crash loop (relevant during the pending update), the watchdog can send a notification to a configured Discord channel or Telegram handle — alerting Josh immediately rather than having Heather silently dark.

**Prerequisite:** AlphaClaw Apex dashboard watchdog notification config. Not a change to `openclaw.json` — this is a fleet-operator setting. Verify with the AlphaClaw dashboard whether Discord crash notifications are enabled for this instance.

**Why now:** The pending update (2026.3.22 → 2026.5.7) is the highest-crash-risk operation Josh will do. Having watchdog notifications enabled before the update means Josh gets immediate Discord ping if Heather doesn't come back up cleanly.

**Risk level:** LOW — platform configuration, zero risk to agent behavior.

---

### X/Twitter Skills — Potential for Josh as Founder/CEO

Josh is Founder & CEO @blisslifestyleofficial and Partner @obenhifi. OpenClaw supports X (Twitter) integration via the X Search skill and posting integrations.

**X Search skill:** Allows Heather to search X content with natural language — useful for brand monitoring ("what are people saying about Bliss?"), competitor tracking, and founder networking context.

**X posting:** Available via the OpenTweet bridge (~$0/month via bridge vs $100/month direct API). Heather could draft tweets for Josh's review, schedule brand content, or monitor @ mentions for Bliss/Oben HiFi.

**Important security note:** ClawHub security team has flagged ~20% of all marketplace skills for security issues (as of Feb 2026). Any X skill installed from ClawHub should be verified before use.

**Relevance to Josh:** Medium-term capability gap — not a Day 28 priority but worth adding to TOOLS.md as a future skill to evaluate once the baseline (memory, heartbeat, update) is established.

**Risk level:** INFO — opportunity, not a current gap.

---

## New Findings — Josh Instance (Day 28 Morning Run 2)

### Finding 48. File Transfer Plugin (v2026.5.3) — Unlocks File-Based Automation
**Risk: LOW | Days pending: NEW**

Four new agent tools post-update: `file_fetch`, `dir_list`, `dir_fetch`, `file_write`. Enables attachment handling, workspace self-audit, and file-based outputs for email/calendar workflows.

**Config:** No change required — plugin activates post-update.
**Blocked by:** Update to 2026.5.3+ (part of the pending 2026.5.7 update).

---

### Finding 49. OpenRouter Response Caching (v2026.5.4) — Reduce Fallback Cost
**Risk: LOW | Days pending: NEW**

X-OpenRouter-Cache headers enable response caching on OpenRouter-routed fallback calls. Add `"cacheControl": true` to OpenRouter config post-update.

**Exact config:**
```json
"openrouter": {
  "cacheControl": true
}
```

**Blocked by:** Update to 2026.5.4+.

---

### Finding 50. Heartbeat lightContext + isolatedSession — Token Efficiency
**Risk: LOW | Days pending: NEW**

Best practice flags for heartbeat runs: `lightContext: true` and `isolatedSession: true` reduce token burn and prevent heartbeat context from bleeding into main conversation history.

**Apply when activating HEARTBEAT.md.** Zero-cost config addition.

---

### Finding 51. AlphaClaw Watchdog — Verify Discord Crash Notifications Before Update
**Risk: LOW | Days pending: NEW**

AlphaClaw watchdog supports Discord crash notifications. Verify via AlphaClaw Apex dashboard that crash alerts are enabled for this instance before performing the pending update. Highest-value during update window when crash risk is elevated.

---

### Finding 52. X Search Skill — Future Capability for Brand Monitoring
**Risk: INFO | Days pending: NEW**

X Search skill allows natural-language X searches. Relevant for Josh's brands (Bliss, Oben HiFi). Future addition after baseline (memory/heartbeat/update) is in place. Any ClawHub skill must be security-verified before install.

---

## Updated Finding Priority Table — Day 28 All Runs

| Finding | Severity | Days Pending | Status |
|---|---|---|---|
| Config wipe bug — backup before update | HIGH | NEW (Run 1) | ⬜ Before update |
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
| AGENTS.md not customized | MEDIUM | 28 | ⬜ Pending |
| Gemini 3 tool-call fixes — version-gated | MEDIUM | 1 | ⬜ Post-update |
| Gemini 3.1 Flash-Lite as fallback | LOW | NEW (Run 1) | ⬜ Post-update |
| Retired claude-3.5-haiku fallback | LOW | 11 | ⬜ Pending |
| Discord streaming off | LOW | 28 | ⬜ Pending |
| IDENTITY.md avatar blank | LOW | 4 | ⬜ Pending |
| ElevenLabs v3 TTS — no voice preference | LOW | 1 | ⬜ Post-update |
| Auto-reply authorization hooks | LOW | 1 | ⬜ Post-update |
| threadBindings — multi-agent Discord | MEDIUM | 4 | ⬜ Post-update |
| Retry-aware cron (exact config ready) | MEDIUM | NEW (Run 1) | ⬜ Ready today |
| workspace/reports/ missing | LOW | 2 | ⬜ Pending |
| memory-lancedb-pro upgrade path | LOW | 2 | ⬜ Post-memory-core |
| **File transfer plugin (v2026.5.3)** | LOW | **NEW (Run 2)** | ⬜ Post-update |
| **OpenRouter response caching** | LOW | **NEW (Run 2)** | ⬜ Post-update |
| **Heartbeat lightContext + isolatedSession** | LOW | **NEW (Run 2)** | ⬜ On heartbeat activation |
| **Watchdog crash notifications** | LOW | **NEW (Run 2)** | ⬜ Before update |
| **X Search skill (future)** | INFO | **NEW (Run 2)** | ⬜ Post-baseline |
| v2026.5.10 stable — monitor | OPPORTUNITY | monitoring | ⬜ Expected this week |
| /context map — token visibility | OPPORTUNITY | 4 | ⬜ Post-5.10 |
| A2A 20-turn sub-agent delegation | OPPORTUNITY | 4 | ⬜ Post-5.10 |
| Per-agent tool overrides (Discord) | OPPORTUNITY | 4 | ⬜ Post-5.10 |

---
*Generated by AlphaClaw Apex Fleet Research Agent — Morning Scan Run 2 — 2026-05-15 (Day 28)*
