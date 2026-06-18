# Fleet Research Findings — Josh / Heather Schwartz

**Scan date:** 2026-06-18 (morning) · Previous scan: 2026-06-18 evening
**Researcher:** AlphaClaw Fleet Agent
**Instance:** josh_repo (Heather Schwartz — personal assistant)
**Current version:** 2026.3.22
**Latest stable:** 2026.6.8 (released June 16, 2026 — STABLE)
**Latest beta:** None newer — 2026.6.8 is the head of the release train as of June 18

> ✅ RESOLVED (June 17): workspace/SOUL.md — personalized with Josh's hard rules
> ✅ RESOLVED (June 17): workspace/AGENTS.md — personalized with emoji override at top
> ✅ RESOLVED (June 17): workspace/TOOLS.md — populated with AlphaClaw UI, Discord, iMessage, models
> ✅ RESOLVED (June 17): workspace/BOOTSTRAP.md — deleted (no longer burning context tokens)
> ✅ RESOLVED (June 17): memory/heartbeat-state.json — created
> ✅ RESOLVED (June 16): workspace/MEMORY.md — created and seeded
> ✅ RESOLVED (June 16): workspace/HEARTBEAT.md — populated with active monitoring schedule
> ✅ RESOLVED (June 16): gemini-2.5-flash → gemini-3.5-flash in openclaw.json
> ⛔ Still open: Google Workspace OAuth not connected — email/calendar inaccessible
> ⛔ Still open: OpenClaw 87+ days outdated (2026.3.22 vs 2026.6.8). Requires VPS upgrade.
> ⛔ Still open: Dreaming not enabled in openclaw.json (use corrected config — see Finding 24)
> ⛔ Still open: compaction/memoryFlush not configured in openclaw.json
> ⛔ Still open: Discord security open to all (groupPolicy: open)
> ⛔ Still open: Heartbeat cron never fired — all five service checks at null (see Finding 26)

---

## ⭐ Finding 26 — Heartbeat All Nulls: Every Service Check Blocked (NEW — June 18 Morning)

**Risk: MEDIUM (monitoring blind spot — Heather has zero health visibility)**

The heartbeat-state.json created June 17 shows ALL five service checks permanently at `null` — no check has ever completed:

```json
{
  "email": null,
  "calendar": null,
  "imessage": null,
  "memory_maintenance": null,
  "contacts": null
}
```

This is not a timing gap. All five are blocked by known root causes:

| Check | Status | Blocker |
|---|---|---|
| email | `null` | Google Workspace OAuth not connected (Finding 2) |
| calendar | `null` | Google Workspace OAuth not connected (Finding 2) |
| contacts | `null` | Google Workspace OAuth not connected (Finding 2) |
| imessage | `null` | iMessage paused since ~April 27 — 87+ days offline |
| memory_maintenance | `null` | **No blocker** — this should be running |

`memory_maintenance` (checks MEMORY.md size, daily note hygiene) does NOT require Google Workspace or iMessage. Its null state means Heather's heartbeat cron has **never been scheduled on the VPS** — the fleet agent created the file but the agent was never instructed to run a daily maintenance task.

**Action (VPS, after upgrade):**
Add to openclaw.json cron section:
```json
{
  "schedule": "0 9 * * *",
  "task": "heartbeat_memory_maintenance",
  "description": "Daily MEMORY.md size audit and stale daily-note cleanup"
}
```

This is the one check that can fire immediately after upgrade, even while Google OAuth and iMessage remain offline. Connect Google Workspace OAuth (Finding 2) to unlock the other four.

---

## ⭐ Finding 25 — ClawHavoc: Audit Installed Skills (NEW — June 18 Morning)

**Risk: LOW for Josh (no skills installed) / note for Noah audit**

In January 2026, security researchers uncovered **ClawHavoc** — a coordinated attack planting 341 malicious skills on ClawHub via typosquatting (e.g., `nylas-cIi` with capital I, `brave-seach` missing 'r'). The malware exfiltrated SSH keys, API tokens, and browser session cookies from infected agents.

**Josh / Heather:** The `workspace/skills` directory is empty — **no current risk**.

**Noah / Catalyst (last known):** `gog-cli` was installed in Noah's skills directory. This needs to be verified against the legitimate `gog-cli` ClawHub listing.

**Action (VPS, both instances):**
```bash
openclaw skill list
```
Cross-reference every installed skill name against the official ClawHub listing at `openclaw.ai/skills`. Watch for: numbers replacing letters (0 for O, I for l), missing characters, or unknown publishers. If uncertain, uninstall and reinstall from the verified source.

---

## ⭐ Finding 24 — Dreaming Config Correction: minScore Must Be 0.8, Not 0.7 (NEW — June 18 Morning)

**Risk: LOW (corrects Finding 22 before it is applied)**

The dreaming config in Finding 22 specified `"minScore": 0.7`. **This is too low.** Official OpenClaw docs, the DEV.to Dreaming Guide, and multiple independent sources confirm the correct promoted threshold:

- `minScore`: **0.8** minimum (at 0.7, weak/ephemeral memories get promoted prematurely, polluting MEMORY.md with noise)
- `minRecallCount`: **3** (memory must be recalled at least 3 times before promotion)
- `minUniqueQueries`: **3** (recalled across at least 3 distinct queries)

**New file awareness:** When Dreaming is enabled, two new file types are created automatically:
- **`DREAMS.md`** in workspace root — human-readable dream diary log (excluded from default recall to avoid noise)
- **`memory/dreaming/<phase>/YYYY-MM-DD.md`** — per-phase reports (Light Sleep / REM Sleep / Deep Sleep)

Do not load DREAMS.md into the main context window. It is informational only.

**Corrected dreaming config for openclaw.json** (replaces Finding 22 config):
```json
"dreaming": {
  "enabled": true,
  "schedule": "0 3 * * *",
  "maxPromotion": 10,
  "minScore": 0.8,
  "minRecallCount": 3,
  "minUniqueQueries": 3
}
```

Add under `agents.defaults` in openclaw.json. Adjust schedule to 3 AM VPS-local time (or 11:00 UTC if VPS is UTC, targeting 3 AM PST).

---

## ⭐ Finding 23 — AlphaClaw 0.9.17/0.9.18: Per-Agent Thinking, Opus 4.8, OpenAI Proxy, Remote MCP (NEW — June 18 Morning)

**Risk: LOW (new capabilities — available now on current AlphaClaw version)**

Two AlphaClaw releases (0.9.17 May 31, 0.9.18 June 1) added capabilities that are available now on the fleet's current AlphaClaw version:

### 0.9.17 — Per-Agent Thinking Level + Opus 4.8

**Per-agent `thinkingDefault`**: Set inference thinking level per-agent from the model card in the AlphaClaw UI — no openclaw.json edit needed. For Heather, this means fast/minimal thinking for conversational replies, deeper thinking for complex scheduling or research tasks. Configurable per agent instance.

**Claude Opus 4.8 in catalog**: Opus 4.8 is now properly mapped and selectable. Relevant for high-stakes agentic tasks. Could be used as an occasional override model rather than the daily driver (significantly higher cost than Sonnet/Haiku).

### 0.9.18 — OpenAI-Compatible Proxy + Remote MCP

**OpenAI-compatible API** (`/v1/chat/completions`, `/v1/embeddings`): Heather's AlphaClaw instance can now act as an OpenAI-format endpoint. This unlocks integrations that require OpenAI-format APIs — e.g., n8n, Zapier AI Actions, some home automation platforms — pointing directly at Heather without any middleware.

Enable in AlphaClaw Setup UI (toggle, persisted to Git-syncable config). Does NOT require openclaw.json changes.

**Remote MCP server support**: Set `REMOTE_MCP_URL` and `REMOTE_MCP_API_TOKEN` in AlphaClaw environment settings to auto-register a hosted MCP server. Opens the door to connecting:
- Managed Google Workspace MCP (alternative path to Finding 2 if OAuth remains blocked)
- Financial data MCP servers for Noah
- Any other hosted MCP without installing locally

**Actions:**
- Thinking level: AlphaClaw UI → each agent's model card → set `thinkingDefault`
- OpenAI proxy: AlphaClaw Setup UI → toggle on (optional)
- Remote MCP: AlphaClaw Setup UI → environment settings → set `REMOTE_MCP_URL` and `REMOTE_MCP_API_TOKEN` if/when a remote MCP service is used

---

## ⭐ Finding 19 — OpenClaw 2026.6.8 Now STABLE (June 17 Morning)

**Risk: INFO / Upgrade path now clear to 2026.6.8**

OpenClaw 2026.6.8 has graduated from beta to stable as of June 16–17, 2026. The upgrade path for Josh is:

```
2026.3.22 → 2026.5.27 → 2026.6.2 → 2026.6.5 → 2026.6.6 → 2026.6.8
```

**Key new fixes in 2026.6.8 relevant to Heather:**

- **iMessage reply-action handling** (PR #93137): Disabled reply-action handling for iMessage — fixes spurious reactions to iMessages.
- **iMessage split-send coalescing removed** (PR #93143): Client-side split-send coalescing gone — iMessage sends now simpler and more reliable.
- **iMessage NUL byte fix**: NUL bytes in sent-message echoes no longer corrupt the session.
- **Memory/state recovery**: Stronger recovery from context compaction events — state preserved across resets.
- **Safer model routing**: Provider catalog routing hardened; fewer silent fallback failures.
- **Cron SQLite storage**: Cron status now uses SQLite instead of legacy jobs.json — fixes status reporting reliability.
- **Usage footer enhanced**: `/usage` generates a full footer report after each AI reply with accurate limits.

**Haiku 4.5 upgrade now available post-2026.6.8:**
Change `openrouter/anthropic/claude-3.5-haiku` → `openrouter/anthropic/claude-haiku-4-5` in openclaw.json fallbacks.

**Action:**
```bash
openclaw update   # run on VPS at 5.78.142.81
```

---

## ⭐ Finding 20 — Discord Security: Open to All (June 17 Morning)

**Risk: MEDIUM-HIGH**

Heather's Discord configuration uses `groupPolicy: open` and `dmPolicy: open` with `allowFrom: ["*"]`. Anyone who can reach Josh's Discord server can DM Heather and get responses — including access to responses that draw on Josh's personal context.

**Action (requires VPS):**
In `openclaw.json` under `channels.discord`:
```json
"groupPolicy": "allowlist",
"dmPolicy": "allowlist",
"allowFrom": ["JOSH_DISCORD_USER_ID"],
"pairingEnabled": true
```
Replace `JOSH_DISCORD_USER_ID` with Josh's actual Discord user ID. Guild ID: `1484448262290276464`.

---

## ⭐ Finding 21 — MEMORY.md Size Monitoring (June 17 Morning)

**Risk: LOW (proactive hygiene)**

OpenClaw enforces a hard cap of **20,000 characters per individual file** loaded into context. MEMORY.md is currently 3,297 bytes — well within limits. As Heather learns more, this will grow. Best practice: keep under 15,000 characters; move old resolved items to dated daily notes.

---

## ⭐ Finding 22 — Dreaming Still Not Enabled (June 17 Morning — config corrected by Finding 24)

**Risk: HIGH**

Dreaming was first flagged June 12. MEMORY.md exists since June 16. Without Dreaming, MEMORY.md stays static between fleet agent scans and session context builds up in daily notes with no automated consolidation.

**Use the corrected config from Finding 24 (minScore: 0.8, not 0.7).**

---

## Previous Findings (June 16 Morning)

---

## Finding 1 — Version Outdated (87+ Days Behind)

**Risk: HIGH**

Heather is running OpenClaw `2026.3.22`. Current stable is `2026.6.8`. That's an 87+ day gap spanning 12+ releases.

**Key fixes in the missed window directly relevant to Heather:**
- **Gateway restart wedge** (2026.6.6): Failed provider refresh could lock the gateway until manual restart — now self-recovers.
- **Native hook relay leak** (2026.6.6): Abandoned connections accumulated indefinitely on long-running agents — now bounded.
- **iMessage recovery** (2026.6.5): Private-API failures and send timeouts now explain themselves.
- **iMessage reply-action + NUL byte fixes** (2026.6.8): See Finding 19.
- **Parallel web search built-in** (2026.6.5): No separate setup required.
- **MCP tool result coercion** (2026.6.5): Non-text/image MCP blocks no longer poison session history with Anthropic 400 errors.
- **Meeting Notes** (2026.5.26): Real-time Discord voice call transcription.
- **Cron SQLite fix** (2026.6.8): Cron status reporting now reliable.

**Action:**
```bash
openclaw update   # on VPS 5.78.142.81
```
Staged: `2026.3.22 → 2026.5.27 → 2026.6.2 → 2026.6.5 → 2026.6.6 → 2026.6.8`

---

## Finding 2 — Google Workspace Not Connected (Critical Gap)

**Risk: CRITICAL**

No Google accounts are connected via OAuth. Heather cannot access Gmail, Google Calendar, or Google Contacts. iMessage has been paused since ~April 27, 2026. Email and calendar have been offline for 87+ days.

**Action:**
1. Go to AlphaClaw UI: `https://5.78.142.81.sslip.io#general`
2. Under Google Workspace, provide OAuth client credentials from Google Cloud Console
3. Authorize Gmail, Google Calendar, Google Contacts (minimum)
4. Steps in `workspace/memory/onboarding-google.md`

**Alternative path:** See Finding 14 (Nylas CLI) if GCP OAuth is the blocker.
**New path (Finding 23):** Remote MCP (`REMOTE_MCP_URL`) via a managed Google Workspace MCP server — avoids needing GCP OAuth entirely.

---

## Finding 3 — Concurrent Web Search Bug (Gemini-3-Flash)

**Risk: MEDIUM**

Known OpenClaw issue (#30675): when a subagent fires multiple parallel `web_search` calls, subsequent calls fail silently. Resolved on upgrade to 2026.6.6+ (built-in parallel web search).

---

## Finding 4 — No Memory Protection Before Compaction

**Risk: HIGH**

`openclaw.json` has no compaction settings. Without `memoryFlush`, OpenClaw does not trigger a memory-write turn before compaction.

**Action (add to `openclaw.json` under `agents.defaults`):**
```json
"compaction": {
  "reserveTokensFloor": 40000,
  "memoryFlush": {
    "enabled": true,
    "softThresholdTokens": 4000
  }
},
"contextPruning": {
  "mode": "cache-ttl",
  "ttl": "6h"
}
```

---

## Finding 7 — Dreaming Not Enabled (escalated to Finding 22, config corrected by Finding 24)

See Finding 22 and Finding 24. Use corrected config (minScore: 0.8).

---

## Finding 10 — AGENTS.md Emoji Rule Contradiction

**Risk: ✅ RESOLVED — June 17, 2026**

---

## Finding 13 — Discord Streaming: Use "progress" Mode

**Risk: LOW**

`"progress"` mode batches tool-use turns and produces cleaner responses.

**Action (post-upgrade to 2026.5.3+):**
```json
"channels": {
  "discord": {
    "streaming": "progress"
  }
}
```

---

## Finding 14 — Nylas CLI: Alternative Email/Calendar Integration

**Risk: MEDIUM**

If GCP OAuth is blocked, Nylas CLI provides an alternative: `openclaw skill install nylas-cli`. Note: email transits a third-party API. Also see Finding 23 Remote MCP path.

---

## Finding 15 — NVIDIA SkillSpector (Post-Upgrade Passive Benefit)

**Risk: LOW**

Skill Workshop with NVIDIA SkillSpector integration activates automatically on upgrade to 2026.6.6+. Scans skills for prompt injection risks.

---

## Summary Table (Updated June 18 Morning)

| Finding | Priority | Effort | Impact | Status |
|---|---|---|---|---|
| ~~gemini-2.5-flash deadline~~ | ~~CRITICAL~~ | ~~30 sec~~ | ~~Fallback chain~~ | ✅ FIXED 2026-06-16 |
| ~~MEMORY.md missing~~ | ~~CRITICAL~~ | ~~Low~~ | ~~Long-term memory~~ | ✅ CREATED 2026-06-16 |
| ~~HEARTBEAT.md empty~~ | ~~HIGH~~ | ~~5 min~~ | ~~Proactive monitoring~~ | ✅ POPULATED 2026-06-16 |
| ~~SOUL.md not personalized~~ | ~~HIGH~~ | ~~5 min~~ | ~~Identity + hard rules~~ | ✅ DONE 2026-06-17 |
| ~~AGENTS.md emoji contradiction~~ | ~~HIGH~~ | ~~5 min~~ | ~~Stops violating Josh's rule~~ | ✅ DONE 2026-06-17 |
| ~~TOOLS.md template only~~ | ~~LOW~~ | ~~Low~~ | ~~Fewer clarifying questions~~ | ✅ DONE 2026-06-17 |
| ~~BOOTSTRAP.md stale~~ | ~~LOW~~ | ~~30 sec~~ | ~~Context token waste~~ | ✅ DELETED 2026-06-17 |
| ~~heartbeat-state.json missing~~ | ~~LOW~~ | ~~Low~~ | ~~State tracking~~ | ✅ CREATED 2026-06-17 |
| 2. Connect Google Workspace | CRITICAL | Medium | Unlocks email/calendar/heartbeat | ⏳ Day 88 |
| 1. Upgrade to 2026.6.8 | HIGH | Low | All iMessage + gateway fixes | ⏳ Day 88 |
| 22/24. Enable Dreaming (corrected config) | HIGH | Low | Nightly memory consolidation | ⏳ New 06-18 |
| 4. Add compaction/memoryFlush | HIGH | Low | Memory safe on compaction | ⏳ Day 88 |
| 26. Heartbeat cron never fired | MEDIUM | Low (post-upgrade) | memory_maintenance unblocked | 🆕 New 06-18 |
| 20. Discord security (open→allowlist) | MEDIUM-HIGH | Low | Protects Josh's personal context | ⏳ 06-17 |
| 3. Concurrent search bug | MEDIUM | Low (update first) | Research reliability | ⏳ Unresolved |
| 14. Nylas CLI / Remote MCP alternative | MEDIUM | Low | Unblocks email if OAuth blocked | 06-15 |
| 23. AlphaClaw 0.9.17/18 new features | INFO | None (UI only) | Thinking control, OpenAI proxy, Remote MCP | 🆕 New 06-18 |
| 19. 2026.6.8 stable + Haiku 4.5 | INFO | None (post-upgrade) | New upgrade target | 06-17 |
| 24. Dreaming config correction | LOW | Low (apply before enabling) | Correct memory promotion threshold | 🆕 New 06-18 |
| 25. ClawHavoc skill audit | LOW | 5 min (VPS) | Security hygiene | 🆕 New 06-18 |
| 13. Discord streaming → "progress" | LOW | Low | Cleaner responses | 06-15 |
| 15. NVIDIA SkillSpector post-upgrade | LOW | None | Passive skill security | 06-15 |
| 21. MEMORY.md size monitoring | LOW | Watch only | Proactive hygiene | 06-17 |

---

## Remaining Open Action List (June 18 Morning)

### Requires VPS access
1. **[CRITICAL]** Connect Google Workspace OAuth at `https://5.78.142.81.sslip.io#general` (also consider Remote MCP path from Finding 23)
2. **[HIGH]** Upgrade OpenClaw: `openclaw update` (staged: 2026.3.22 → 2026.5.27 → 2026.6.2 → 2026.6.5 → 2026.6.6 → 2026.6.8)
3. **[HIGH]** Add to `openclaw.json`: compaction/memoryFlush + contextPruning (see Finding 4)
4. **[HIGH]** Enable Dreaming in `openclaw.json` using **corrected config from Finding 24** (minScore: 0.8)
5. **[MEDIUM]** Add heartbeat memory_maintenance cron (see Finding 26)
6. **[MEDIUM-HIGH]** Tighten Discord allowFrom: replace `"*"` with Josh's Discord user ID (see Finding 20)
7. **[LOW]** Run `openclaw skill list` and audit installed skills (see Finding 25)
8. **[LOW]** Enable Discord streaming `"progress"` mode (see Finding 13)

### AlphaClaw UI (no VPS CLI needed)
9. **[LOW]** Set per-agent `thinkingDefault` from model card in AlphaClaw UI (Finding 23)
10. **[LOW]** Enable OpenAI-compatible proxy toggle if integrations need it (Finding 23)

### After upgrade to 2026.6.8
11. **[LOW]** Upgrade fallback 2 from `claude-3.5-haiku` → `claude-haiku-4-5` in `openclaw.json`

---

*Sources: [OpenClaw Releases](https://github.com/openclaw/openclaw/releases), [Releasebot OpenClaw](https://releasebot.io/updates/openclaw), [OpenClaw Dreaming Guide](https://dev.to/czmilo/openclaw-dreaming-guide-2026-background-memory-consolidation-for-ai-agents-585e), [OpenClaw Dreaming Docs](https://docs.openclaw.ai/concepts/dreaming), [AlphaClaw GitHub](https://github.com/chrysb/alphaclaw), [Mem0 Memory Management](https://mem0.ai/blog/openclaw-memory-management-live-data-compaction-and-best-practices), [VelvetShark Memory Guide](https://velvetshark.com/openclaw-memory-masterclass), [OpenClaw Memory Docs](https://docs.openclaw.ai/concepts/memory)*
