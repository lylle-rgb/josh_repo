# Soul Improvements — Josh (Heather Schwartz) | 2026-06-17 Evening

**Instance:** Heather Schwartz (Josh — personal assistant)  
**Scan date:** 2026-06-17 (evening)  
**Based on findings:** `2026-06-17-evening-findings.md`

---

## Status: All GitHub-Accessible Improvements Applied

All soul/workspace improvements reachable via GitHub have now been applied across the June 16–17 scan pair:

| Date | What Was Applied |
|------|----------------|
| June 16 morning | Gemini fallback fixed in openclaw.json; MEMORY.md seeded; HEARTBEAT.md populated with monitoring schedule |
| June 17 evening | SOUL.md personalized with Josh context + hard rules + error recovery; AGENTS.md emoji contradiction resolved + weekly self-check added; TOOLS.md populated with environment specifics; heartbeat-state.json created; stale gemini-2.5-flash removed from openclaw.json models dict |

---

## What Was Applied Tonight

### SOUL.md — Josh Executive Context (Implements June 13 Rec 3 + June 16 Recs 10–11)

**Added "Who I'm Serving" section:**
- Josh Meyers — Bliss CEO, Oben HiFi Partner, LA/PST
- Defines what Josh actually needs: proactive inbox/calendar awareness, directness, discretion

**Added "Josh's Hard Rules" section:**
- NO emoji reactions — explicitly states this overrides AGENTS.md defaults
- No performative filler
- Concise by default

**Added "When Things Break" section (Implements June 15 Rec 6, June 16 Recs 10–11):**
- Tool/integration failure playbook
- Gateway restart procedure: write to memory first, re-read startup files after
- Echoed Discord messages: don't reply twice, self-heals on 2026.6.6+
- Google Workspace unavailable: note at morning heartbeat once, don't repeat-alarm

### AGENTS.md — Emoji Contradiction Resolved (Implements June 15 Rec 8)

**Added "Josh's Override Rules" block** — appears near the top, before the emoji section, so Heather reads the override before the default behavior.

**Suspended the "😊 React Like a Human!" section** with explicit ⛔ notice. The section is preserved for readability but marked as suspended for this instance. This resolves the multi-month contradiction between AGENTS.md (use reactions) and USER.md (strict no reactions).

### AGENTS.md — Weekly Self-Check Step (Implements June 16 Rec 12)

Added step 5 to Session Startup: optional weekly audit of `openclaw.json` for deprecated model endpoints. Builds a habit so Heather can catch a future gemini-style deprecation herself rather than waiting for the fleet scan.

### TOOLS.md — Environment Documentation

Replaced the boilerplate template with actual environment specifics. Heather now has:
- AlphaClaw UI tab reference (all 5 tabs with URLs and purposes)
- Discord configuration (guild ID, reaction prohibition)
- Google Workspace status (API key configured, OAuth not connected, how to fix)
- iMessage paused status with do-not-edit warning
- Full platform version + staged upgrade path
- Current model config with upgrade timeline

### heartbeat-state.json — Initial State Created

Created `memory/heartbeat-state.json` with null timestamps for email, calendar, imessage, memory_maintenance, and contacts. This enables HEARTBEAT.md's rate-limiting logic: Heather can now track when she last checked each service and avoid repeat checks within the 30-minute quiet window.

---

## What Heather Looks Like Now (After All Fixes)

| Feature | Status |
|---------|-------|
| Long-term memory (MEMORY.md) | ✅ Seeded June 16 |
| Proactive heartbeat schedule (HEARTBEAT.md) | ✅ Populated June 16 |
| Heartbeat state tracking (heartbeat-state.json) | ✅ Created June 17 |
| Josh identity rules (SOUL.md) | ✅ Personalized June 17 |
| Emoji contradiction resolved (AGENTS.md) | ✅ Fixed June 17 |
| Weekly fallback self-check (AGENTS.md) | ✅ Added June 17 |
| Environment documentation (TOOLS.md) | ✅ Populated June 17 |
| Working fallback model chain | ✅ Fixed June 16 |
| Google Workspace | ⛔ Blocked on Josh action |
| Platform upgrade to 2026.6.6 | ⛔ Blocked on Josh action |
| iMessage monitoring | ⛔ Paused — auto-fixes after platform upgrade |
| BOOTSTRAP.md deletion | ⛔ Needs manual GitHub UI delete |

**Everything reachable from GitHub is done. The two high-impact blockers (Google OAuth, platform upgrade) require Josh's direct action.**

---

## Remaining Open Recommendations (Post-GitHub-Fixes)

These are deferred to future scans — they require platform upgrade or VPS access first:

1. **Enable Discord streaming** — `channels.discord.streaming: "progress"` in openclaw.json (requires upgrade first; streaming mode was broken pre-2026.6.5)

2. **Upgrade fallback 2 to claude-haiku-4-5** — change `openrouter/anthropic/claude-3.5-haiku` → `openrouter/anthropic/claude-haiku-4-5` after 2026.6.8-stable ships (expected late June). Haiku 4.5 is faster and more capable at similar cost.

3. **Add isolated cron sessions for time-critical tasks** — post-upgrade, consider separate cron sessions for morning digest and calendar alerts rather than relying solely on heartbeat. Cron gives exact timing; heartbeat drifts.

4. **ClawHub Skill Workshop** — available in 2026.6.1+. Provides a proposal-review queue before agent-created skills reach production. Worth enabling post-upgrade to give Josh visibility into any skills Heather proposes.

---

## Noah

Session scope mismatch persists. No Noah soul improvements can be made until the fleet scan is pointed at the correct repo (`Noahrepo2` or `Noah-workspace` — both confirmed to exist on GitHub). Escalate to fleet operator.
