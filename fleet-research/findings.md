# Fleet Research Findings — Josh / Heather Schwartz

**Scan date:** 2026-06-16 (evening) · Previous scan: 2026-06-15 morning
**Researcher:** AlphaClaw Fleet Agent
**Instance:** josh_repo (Heather Schwartz — personal assistant)
**Current version:** 2026.3.22
**Latest stable:** 2026.6.6 (June 13, 2026)
**Latest beta:** 2026.6.7-beta.1

> ⛔ FINAL WARNING — TOMORROW: gemini-2.5-flash deprecates June 17. One line in openclaw.json. 30 seconds.
> See Finding 16 for the new stable (2026.6.6). All prior findings still unresolved (Day 86).

---

## Finding 1 — Version Outdated (86 Days Behind)

**Risk: HIGH**

Heather is running OpenClaw `2026.3.22`. Current stable is now `2026.6.6` (confirmed npm `latest` June 13). Beta is `2026.6.7-beta.1`. That's an 86-day gap spanning 10+ releases.

**Key fixes in the missed window directly relevant to Heather:**
- **Gateway restart wedge** (2026.6.6): Failed provider refresh could lock the gateway until manual restart — now self-recovers. Explains Heather's intermittent unresponsiveness.
- **Native hook relay leak** (2026.6.6): Abandoned connections accumulated indefinitely on long-running agents — now bounded. Always-on agents like Heather were accumulating dead connections.
- **iMessage recovery** (2026.6.5): Private-API failures and send timeouts now explain themselves; split-send coalescing honors balloon metadata.
- **Parallel web search bundled** (2026.6.5): Web search is a first-class built-in; no separate setup required.
- **MCP tool result coercion** (2026.6.5): Non-text/image MCP blocks no longer poison session history with Anthropic 400 errors.
- **Meeting Notes** (2026.5.26): Real-time Discord voice call transcription — missed entirely at current version.

**Action:**
```bash
openclaw update
```
Or via the AlphaClaw Watchdog tab: `https://5.78.142.81.sslip.io#watchdog`

Recommended staged upgrade: `2026.3.22 → 2026.5.27 → 2026.6.2 → 2026.6.5 → 2026.6.6`

---

## Finding 2 — Google Workspace Not Connected (Critical Gap)

**Risk: CRITICAL**

No Google accounts are configured. Heather's entire value proposition is managing Josh's iMessage, email, and calendar. Without Google Workspace, she cannot access Gmail, Google Calendar, or Google Contacts.

Analysis of `workspace/memory/inbox-state.json` confirms email and iMessage have been offline for **86+ days** (iMessage paused ~April 27, email last checked ~April 30).

**Action:**
1. Go to AlphaClaw UI: `https://5.78.142.81.sslip.io#general`
2. Under Google Workspace, provide OAuth client credentials from Google Cloud Console
3. Authorize: Gmail, Google Calendar, Google Contacts (minimum); Drive and Tasks recommended
4. Steps in `workspace/memory/onboarding-google.md`

**Alternative path if OAuth is blocked:** See Finding 14 (Nylas CLI).

---

## Finding 3 — Concurrent Web Search Bug (Gemini-3-Flash)

**Risk: MEDIUM**

A known OpenClaw issue (#30675) affects `google/gemini-3-flash-preview`: when a subagent fires multiple parallel `web_search` calls in one turn, subsequent calls fail silently with `missing_gemini_api_key`. Research tasks may return incomplete answers without surfacing an error.

**Action:**
Update to 2026.6.6 first (Finding 1). If it persists, add to `openclaw.json` under `agents.defaults`:
```json
"webSearch": {
  "maxConcurrentCalls": 1
}
```

---

## Finding 4 — No Memory Protection Before Compaction

**Risk: HIGH**

`openclaw.json` has no compaction settings. Without `memoryFlush`, OpenClaw does not trigger a memory-write turn before compaction. When Heather's session hits the context limit, everything from that session is silently lost.

**Action:**
Add to `openclaw.json` under `agents.defaults`:
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

## Finding 5 — TOOLS.md Is a Blank Template

**Risk: LOW**

`workspace/TOOLS.md` contains only the default placeholder text — no actual device names, SSH aliases, or environment notes. Heather must ask or guess about Josh's setup every session.

**Action:** Populate `workspace/TOOLS.md` with Josh's devices, channels, and frequently mentioned contacts. Full template in `2026-06-13-evening-soul-improvements.md`.

---

## Finding 6 — Discord Streaming Disabled

**Risk: LOW**

`openclaw.json` has `"streaming": "off"`. See Finding 13 for the recommended fix.

---

## Finding 7 — Dreaming (Memory Consolidation) Not Enabled

**Risk: HIGH**

OpenClaw's "Dreaming" feature runs nightly background memory consolidation. It is disabled by default. `workspace/MEMORY.md` does not exist — 86 days without long-term memory.

**Action:**

Step 1 — Create `workspace/MEMORY.md`. Full template in `2026-06-13-evening-soul-improvements.md`.

Step 2 — Enable Dreaming in `openclaw.json` under `agents.defaults`:
```json
"dreaming": {
  "enabled": true,
  "schedule": "0 3 * * *",
  "maxPromotion": 10,
  "minScore": 0.7
}
```

---

## Finding 8 — HEARTBEAT.md Not Populated

**Risk: HIGH**

`workspace/HEARTBEAT.md` contains only template placeholder text. Heather returns `HEARTBEAT_OK` on every heartbeat poll without doing any proactive work. Email urgency checks, calendar alerts, weather — all completely dormant. 86 days.

**Action:** Replace `workspace/HEARTBEAT.md` with the active monitoring template in `2026-06-13-evening-soul-improvements.md`.

---

## Finding 9 — Platform Monitoring (Updated June 16)

**Risk: INFO**

`2026.6.6` is confirmed npm `latest` as of June 13. Beta at `2026.6.7-beta.1`. Upgrade target is now `2026.6.6` (was `2026.6.5`).

---

## Finding 10 — AGENTS.md Emoji Rule Contradicts USER.md Strict Preference

**Risk: HIGH**

- `workspace/USER.md` states: **"STRICT: DO NOT SEND EMOJI REACTIONS TO MESSAGES."**
- `workspace/AGENTS.md` section "React Like a Human!" actively encourages emoji reactions with examples.

Heather is almost certainly violating Josh's hard preference on every session.

**Action:**
1. Add Josh-Specific Hard Rules section to top of `workspace/SOUL.md` (see `2026-06-13-evening-soul-improvements.md`)
2. Replace "React Like a Human!" section in `workspace/AGENTS.md` (full replacement text in `2026-06-15-evening-soul-improvements.md` Rec 8)

---

## Finding 11 — No gog-cli Skill in Josh's Repo

**Risk: MEDIUM-HIGH**

No `skills/` directory exists. There is no structured Gmail/Calendar/Contacts interface defined for Heather.

**Action:** Once Google is connected, install gog-cli skill. If OAuth is blocked, see Finding 14 (Nylas CLI).

---

## Finding 12 — Prolonged Stagnation (86 Days, No Workspace Files Applied)

**Risk: ESCALATION**

Zero of the GitHub-only recommendations from May–June scans have been applied. These require no VPS access:

- Fix gemini-2.5-flash fallback → gemini-3.5-flash (30 sec, ⛔ TOMORROW deadline)
- Create `workspace/MEMORY.md` (5 min)
- Populate `workspace/HEARTBEAT.md` (5 min)
- Add Josh hard rules to `workspace/SOUL.md` (5 min)
- Delete `workspace/BOOTSTRAP.md` (30 sec)
- Fix emoji contradiction in `workspace/AGENTS.md` (2 min)

Total GitHub-only effort: ~20 minutes.

---

## Finding 13 — Discord Streaming: Use "progress" Mode

**Risk: LOW**

`"progress"` mode, available since v2026.5.3, is better than `"on"` for Heather's use case. It batches tool-use turns and produces cleaner responses when tools fire mid-response.

**Updated action:**
```json
"channels": {
  "discord": {
    "streaming": "progress"
  }
}
```

**Dependency:** OpenClaw ≥ 2026.5.3, included in the 2026.6.6 upgrade.

---

## Finding 14 — Nylas CLI: Alternative Email/Calendar Integration Path

**Risk: MEDIUM**

If GCP OAuth setup is the blocker for Finding 2, **Nylas CLI** provides an alternative:
- 72+ commands across Gmail, Outlook, Exchange, Yahoo, iCloud, IMAP
- Single authentication flow — no Google Cloud Console project required
- `openclaw skill install nylas-cli`

**Note:** Nylas is middleware — email transits a third-party API.

---

## Finding 15 — NVIDIA SkillSpector Skill Security (Post-Upgrade Passive Benefit)

**Risk: LOW (passive)**

OpenClaw 2026.6.1 shipped Skill Workshop with NVIDIA SkillSpector integration — skills scanned for prompt injection and data exfiltration risks before reaching production. Relevant given Heather's access to Josh's personal calendar, contacts, and business communications.

**Action:** No immediate action. Activates automatically on upgrade to 2026.6.6.

---

## ⭐ Finding 16 — OpenClaw 2026.6.6 Now Stable (NEW — June 16 Evening)

**Risk: INFO / Updated upgrade target**

`2026.6.6` confirmed npm `latest` since June 13. Upgrade target advances from `2026.6.5` to `2026.6.6`. Key changes relevant to Heather:

- **Gateway wedge fixed:** Gateway now self-recovers from restart failures after provider refresh. No more manual restart needed after auth issues.
- **Native hook relay bounded:** Abandoned connections can no longer accumulate indefinitely. Directly benefits always-on agents.
- **Plugin convergence repair exposed:** Orphaned plugin state is self-healed at boot.
- **Explicit session intent guard:** Prevents accidental new chat sessions from opening.
- **Restored chat queue drain:** Messages queued during session switches are now reliably delivered.

**Updated staged upgrade path:** `2026.3.22 → 2026.5.27 → 2026.6.2 → 2026.6.5 → 2026.6.6`

---

## ⭐ Finding 17 — Commit Audit: Zero Workspace Changes Since Deployment (NEW — June 16 Evening)

**Risk: ESCALATION**

A review of git history confirms: no commit to a workspace file (SOUL.md, MEMORY.md, HEARTBEAT.md, AGENTS.md, TOOLS.md, openclaw.json) has occurred since the March 2026 initial setup. Every commit since then has been a fleet-research document. This means Heather's behavioral configuration is identical to Day 1 — 86 days later.

The five GitHub-only actions (Finding 12) remain the fastest path to meaningful improvement.

---

## Summary Table

| Finding | Priority | Effort | Impact | Status |
|---|---|---|---|---|
| ⛔ gemini-2.5-flash deadline TOMORROW | **CRITICAL** | 30 sec | Fallback chain | ⛔ LAST CHANCE |
| 2. Connect Google Workspace | CRITICAL | Medium | Unlocks email/calendar | ⏳ Day 86 |
| 7. Create MEMORY.md + enable Dreaming | HIGH | Low | Long-term memory | ⏳ Day 86 |
| 10. Fix emoji contradiction | HIGH | Low | Stops violating Josh's rule | ⏳ Day 86 |
| 1. Upgrade to 2026.6.6 | HIGH | Low | Gateway fix + relay leak + all 2026.6.x | ⏳ Day 86 |
| 4. Add compaction/memoryFlush | HIGH | Low | Memory safe on compaction | ⏳ Day 86 |
| 8. Populate HEARTBEAT.md | HIGH | 5 min | 86 days zero proactive monitoring | ⏳ Day 86 |
| 11. No gog-cli skill | MEDIUM-HIGH | Medium | Email toolchain gap | ⏳ Day 86 |
| 3. Concurrent search bug | MEDIUM | Low (update first) | Research reliability | ⏳ Unresolved |
| 14. Nylas CLI alternative path | MEDIUM | Low | Alternative to blocked OAuth | 🆕 New 06-15 |
| 17. Commit audit — zero workspace changes | ESCALATION | — | 86-day stagnation documented | 🆕 New 06-16 |
| 16. 2026.6.6 now stable | INFO | None | Upgrade target updated | 🆕 New 06-16 |
| 13. Discord streaming → "progress" mode | LOW | Low | Cleaner responses | New 06-15 |
| 5. Populate TOOLS.md | LOW | Low | Fewer clarifying questions | ⏳ Day 86 |
| 15. NVIDIA SkillSpector post-upgrade | LOW | None | Passive skill security | New 06-15 |
| 9. Platform monitor: 2026.6.6 / beta 2026.6.7-beta.1 | INFO | None | Tracking | Updated 06-16 |

---

*Sources: [OpenClaw Releases](https://github.com/openclaw/openclaw/releases), [newreleases.io/openclaw/v2026.6.6](https://newreleases.io/project/github/openclaw/openclaw/release/v2026.6.6), [Releasebot OpenClaw June 2026](https://releasebot.io/updates/openclaw), [OpenClaw Memory docs](https://docs.openclaw.ai/concepts/memory), [AlphaClaw Releases](https://github.com/chrysb/alphaclaw/releases)*
