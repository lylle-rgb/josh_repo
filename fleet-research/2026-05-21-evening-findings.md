# Fleet Research — Josh (Heather) | 2026-05-21 Evening Scan

**Scan type:** Full (web research + codebase analysis + platform news)
**Date:** 2026-05-21
**Instance:** Josh Meyers — Heather Schwartz (personal assistant)
**Repo:** lylle-rgb/josh_repo

---

## Platform Status

| Item | Current | Latest | Gap |
|------|---------|--------|-----|
| OpenClaw | 2026.3.22 | 2026.5.18 | **~2 months behind** |
| AlphaClaw | Unknown | 0.9.16 | Check deployment |
| Primary model | google/gemini-3-flash-preview | — | — |

---

## Findings

### FINDING-JOSH-29 | Platform Version Critical Gap
**Severity:** HIGH
**Status:** NEW

openclaw.json `lastTouchedVersion` is `2026.3.22`, last touched 2026-03-24. Current stable OpenClaw is `2026.5.18`. The instance is nearly **two months behind** on platform updates.

**Why it matters:**
- 2026.5.x includes Discord final-message delivery fixes, streaming progress drafts (`streaming.mode: "progress"`), multi-turn voice fixes, and a Python debugging skill
- Missing stability and reliability patches from two full release trains
- AlphaClaw 0.9.16 adds file tree improvements and critical config restoration on fresh boot (0.9.15)
- Minimum Node.js requirement raised to 22.19 — verify VPS is compliant

**Exact changes to apply:**
On the VPS: `openclaw upgrade` then verify with `openclaw --version`. Also upgrade AlphaClaw to 0.9.16.

**Risk level:** MEDIUM (test Discord connectivity after upgrade)

---

### FINDING-JOSH-30 | MEMORY.md Never Created
**Severity:** CRITICAL
**Status:** PERSISTENT (documented since Day 15, now Day 32+)

`workspace/MEMORY.md` does not exist. AGENTS.md explicitly instructs the agent to read and maintain this file as long-term memory — but it has never been created. The agent cannot fulfill its own continuity protocol.

**Why it matters:**
- Agent wakes each session with no persistent context beyond the generic soul files
- Daily logs (`memory/YYYY-MM-DD.md`) also don't exist — the memory system is completely absent
- 60+ days of operational context about Josh, his preferences, and known issues is permanently lost
- Community data: "forgets context between sessions" is the #2 most-cited OpenClaw complaint

**Exact changes to apply:**
See soul-improvements.md Recommendation 1 for exact file content to create.

**Risk level:** LOW

---

### FINDING-JOSH-31 | HEARTBEAT.md Empty — No Proactive Monitoring
**Severity:** HIGH
**Status:** PERSISTENT (documented since Day 15, now Day 32+)

`workspace/HEARTBEAT.md` contains only template comments. AGENTS.md instructs the agent to check email, calendar, mentions, and weather during heartbeats — but no tasks are defined. Every heartbeat fires and returns `HEARTBEAT_OK`.

**Why it matters:**
- Heather is not proactively checking Josh's Gmail or calendar
- Josh is getting zero proactive value between manual messages
- iMessage monitoring has been paused 25+ days with no automated status checks
- Heartbeat compute is wasted on no-ops

**Exact changes to apply:**
See soul-improvements.md Recommendation 2 for full HEARTBEAT.md replacement content.

**Risk level:** LOW

---

### FINDING-JOSH-32 | Bootstrap TOOLS.md Stale — False Google Auth State
**Severity:** MEDIUM
**Status:** PERSISTENT

`workspace/hooks/bootstrap/TOOLS.md` is injected at every session start. It currently reads "No Google accounts are currently configured" — which is incorrect. Google (google:default, api_key mode) has been onboarded for 60+ days.

**Why it matters:**
- Every session starts with poisoned context about available tools
- Agent may hesitate to use Gmail or Calendar tools it actually has
- This has been the startup context for 60+ days of sessions

**Exact changes to apply:**
See soul-improvements.md Recommendation 4 for corrected bootstrap TOOLS.md content.

**Risk level:** LOW

---

### FINDING-JOSH-33 | iMessage Monitoring Paused ~25 Days
**Severity:** MEDIUM
**Status:** PERSISTENT

`workspace/memory/inbox-state.json` contains `"imessage_monitoring_paused": true` with a malformed duplicate key. iMessage monitoring has been paused since approximately April 26, 2026.

**Why it matters:**
- iMessage monitoring is a core personal assistant use case
- 25+ days of unmonitored messages
- The malformed JSON (duplicate key) may cause parse errors in agents that read it
- No automated check exists to detect or report this state

**Exact changes to apply:**
1. Investigate root cause of the pause (likely permission or bridge connectivity issue)
2. Repair inbox-state.json to remove duplicate key
3. Re-enable iMessage monitoring once root cause resolved
4. Add iMessage status check to HEARTBEAT.md (included in Recommendation 2)

**Risk level:** MEDIUM (requires investigating iMessage bridge configuration)

---

### FINDING-JOSH-34 | Emoji Contradiction — USER.md vs AGENTS.md
**Severity:** LOW
**Status:** NEW

`workspace/USER.md` contains: *"STRICT: DO NOT SEND EMOJI REACTIONS TO MESSAGES."*
`workspace/AGENTS.md` contains a full section: *"😊 React Like a Human! — use emoji reactions naturally: 👍 ❤️ 😂..."*

These instructions directly contradict each other. Which wins depends on which the agent happens to emphasize in a given session.

**Why it matters:**
- Josh gave explicit feedback — user preference must override generic templates
- Ambiguous instructions produce inconsistent behavior
- Risk of frustrating Josh when Heather reacts with emoji despite the strict rule

**Exact changes to apply:**
Add a USER PREFERENCE OVERRIDES section to `workspace/SOUL.md` (included in soul-improvements.md Recommendation 3 — appended section).

**Risk level:** LOW

---

### FINDING-JOSH-35 | New Platform Feature: Streaming Progress Drafts
**Severity:** INFO
**Status:** OPPORTUNITY (available in OpenClaw 2026.5.x)

OpenClaw 2026.5.x introduces `streaming.mode: "progress"` for Discord (and Telegram, Matrix, Slack, Teams). While a task runs, the bot posts incremental progress updates instead of going silent.

**Why it matters:**
- Currently Heather appears frozen during long tasks (email search, calendar lookup, web research)
- Progress drafts make the bot feel responsive and alive
- Available for free by changing one config value — no plugin install required

**Exact changes to apply:**
In `openclaw.json`, under `channels.discord`, change:
`"streaming": "off"` → `"streaming": "progress"`

**Risk level:** LOW

---

### FINDING-JOSH-36 | New Platform Feature: Mem0 Persistent Memory Plugin
**Severity:** INFO
**Status:** OPPORTUNITY

Mem0 (mem0.ai) is a memory plugin for OpenClaw that stores memories externally — outside the LLM context window. Auto-Recall injects only relevant memories per turn. Survives context compaction, restarts, and upgrades.

**Why it matters:**
- Native MEMORY.md can be pruned away during long context windows
- Mem0 is immune to compaction — memories persist across all conditions
- Particularly valuable for a personal assistant building long-term context about Josh's preferences, projects, and history
- Directly addresses the community's #2 complaint

**Exact changes to apply:**
1. Install: `openclaw skill install mem0`
2. Configure Mem0 API key in openclaw.json
3. Use alongside native MEMORY.md — complementary, not a replacement

**Risk level:** LOW

---

### FINDING-JOSH-37 | SOUL.md Never Personalized
**Severity:** MEDIUM
**Status:** PERSISTENT (documented since Day 15)

`workspace/SOUL.md` is nearly identical to the generic OpenClaw template. Josh has provided specific feedback (strict emoji rule, luxury lifestyle brand context, named the bot Heather) but none of this has been incorporated into the soul file.

**Why it matters:**
- SOUL.md is loaded every session — it shapes every interaction
- A generic soul produces generic behavior
- 60+ days of personalization signals are going unused
- Josh's business context (Bliss, Oben HiFi) is nowhere in the soul

**Exact changes to apply:**
See soul-improvements.md Recommendation 3 for the SOUL.md appendix to add.

**Risk level:** LOW

---

### FINDING-JOSH-38 | AlphaClaw Crash Notifications Not Configured
**Severity:** INFO
**Status:** OPPORTUNITY (available in AlphaClaw 0.9.x)

AlphaClaw 0.9.x includes a self-healing watchdog with crash detection, crash-loop recovery, and crash notifications via Discord, Telegram, and Slack.

**Why it matters:**
- Currently, a Heather crash is silent — Josh would only notice if the bot stops responding
- With crash notifications, the fleet self-reports outages to a Discord channel immediately
- Enables faster recovery without manual monitoring

**Exact changes to apply:**
Configure crash notification target in AlphaClaw settings: set notification channel to Josh's Discord DM or a private #bot-status channel.

**Risk level:** LOW

---

## Summary Table

| Finding | Severity | Status |
|---------|----------|--------|
| JOSH-29: Platform 2 months outdated | HIGH | NEW |
| JOSH-30: MEMORY.md never created | CRITICAL | PERSISTENT |
| JOSH-31: HEARTBEAT.md empty | HIGH | PERSISTENT |
| JOSH-32: Bootstrap TOOLS.md stale | MEDIUM | PERSISTENT |
| JOSH-33: iMessage monitoring paused | MEDIUM | PERSISTENT |
| JOSH-34: Emoji contradiction | LOW | NEW |
| JOSH-35: Streaming progress available | INFO | OPPORTUNITY |
| JOSH-36: Mem0 plugin available | INFO | OPPORTUNITY |
| JOSH-37: SOUL.md never personalized | MEDIUM | PERSISTENT |
| JOSH-38: Crash notifications available | INFO | OPPORTUNITY |

---

## Platform Research Notes (2026-05-21)

- **OpenClaw latest stable:** 2026.5.18 (May 2026 rollup of 2026.5.12–2026.5.17 beta train)
- **AlphaClaw latest:** 0.9.16 (May 15, 2026) — file tree lazy-loading, config restoration
- **Key AlphaClaw tip:** Set `OPENCLAW_STATE_DIR=/data/.openclaw` in all Docker deployments to prevent plugin data loss on container restart
- **Anti-drift:** AlphaClaw 0.9.x injects bootstrap prompts automatically to prevent persona drift over time
- **Voice improvements:** Discord + OpenAI realtime now maintain multi-turn context; ElevenLabs direct playback with prebuffering; `/vc join/leave/status` commands
- **Cron system:** Now supports isolated sessions (`--session isolated`), webhook delivery, timezone-aware `cron` expressions, exponential backoff retries
- **iMessage (context):** Poke ($15M seed from General Catalyst) validates strong iMessage AI market; Blooio offers an iPhone-native iMessage bot bridge for OpenClaw webhook integration
