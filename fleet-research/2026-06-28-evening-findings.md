# Fleet Research: Josh (Heather) — Evening Scan
**Date:** 2026-06-28 | **Scan type:** Evening | **Agent:** AlphaClaw Fleet Research

## Summary
Day 99 of Google Workspace downtime — **Day 100 is TOMORROW (June 29)**. Heather should escalate to Josh with concrete fix steps in the next main session. Three previously unresearched features surfaced: Active Memory plugin, Dreaming consolidation, and OPENCLAW_TIMEZONE — all highly relevant post-upgrade. 2026.6.11 may have graduated to stable; verify before treating as upgrade target. Noah scope broken Day 19.

---

## Findings

### F1 — CRITICAL: Google Workspace OAuth Day 99 — Day 100 is TOMORROW (June 29)
**Risk:** CRITICAL
- Email and calendar have been inaccessible since ~March 21, 2026 — **Day 99 as of June 28 evening**
- **Day 100 arrives TOMORROW (June 29)** — per SOUL.md escalation rule (applied this scan), surface this with full concreteness at every session
- Fix is ~5 minutes in a browser: AlphaClaw UI → General tab → Google Workspace OAuth → https://5.78.142.81.sslip.io#general
- No VPS, no upgrade required — can be done right now
- **Action for Heather:** Frame as: "Today is Day 99. Tomorrow is Day 100 — email and calendar have been disconnected for 100 days. The fix is 5 minutes in the browser at https://5.78.142.81.sslip.io#general"
- This is the most important action item for Josh today

### F2 — HIGH: Upgrade Window Still Green — 2026.6.10-stable Day 5
**Risk:** HIGH (staying on 2026.3.22 accumulates version debt and known issues)
- 2026.6.10-stable: Day 5 as of June 28. No new regressions reported. Green light unchanged.
- Current version: 2026.3.22 — approximately 3 months behind stable
- Staged upgrade path unchanged: **2026.3.22 → 2026.5.27 → 2026.6.2 → 2026.6.5 → 2026.6.6 → 2026.6.10**
- Skip 2026.6.8 and 2026.6.9 (confirmed critical regressions)
- Verify before running: `npm show openclaw@latest version` should return `2026.6.10`

### F3 — NEW: Active Memory Plugin + Dreaming Available Post-Upgrade (Not Previously Documented)
**Risk:** MEDIUM (capability gap — both features opt-in and must be explicitly enabled after upgrade)
- **Active Memory Plugin (introduced 2026.4.10):** Adds a dedicated memory sub-agent that runs before each reply, automatically pulling relevant preferences, history, and prior context into the active session
  - Result: Heather proactively recalls relevant facts without needing Josh to say "remember this"
  - Reduces manual memory maintenance burden at heartbeats
  - Enabled via openclaw.json plugin config after upgrade
- **Dreaming (introduced 2026.4.9):** Three-phase background memory consolidation inspired by human sleep cycles
  - Light Sleep: ingests and stages short-term signals
  - REM Sleep: reflects and extracts patterns
  - Deep Sleep: promotes qualified items to MEMORY.md automatically
  - Opt-in, disabled by default — enable via openclaw.json after upgrade
  - Complements the current heartbeat-driven manual MEMORY.md maintenance pattern
- **Both features require upgrading past 2026.3.22** — available once reaching 2026.4.10+ en route to 2026.6.10
- **Action:** Add enabling Active Memory + Dreaming to the post-upgrade checklist for Josh's session at 2026.6.10
- With Dreaming enabled, Heather can autonomously consolidate daily memory files → MEMORY.md without heartbeat intervention

### F4 — NEW: OPENCLAW_TIMEZONE Not Set (Not Previously Documented)
**Risk:** MEDIUM (heartbeat cron will fire on UTC clock — Josh is PST/PDT; off by 7–8 hours)
- Cron expressions in OpenClaw default to UTC unless `OPENCLAW_TIMEZONE` is set
- Josh is in Los Angeles (America/Los_Angeles — UTC-7 in PDT, UTC-8 in PST)
- HEARTBEAT.md schedules heartbeats at 9:00 AM, 1:00 PM, 6:00 PM PST — **these will fire at wrong times without timezone config**
- **Set now** via AlphaClaw UI → Envars tab → add `OPENCLAW_TIMEZONE=America/Los_Angeles`
- No upgrade required — can be configured immediately. Must be set before heartbeat cron is deployed.
- Without this: morning check-ins fire at 5 PM, evening summaries fire at 2 AM

### F5 — NEW: 2026.6.11 Status — Possibly Stable (Verify Before Acting)
**Risk:** LOW — informational; DO NOT upgrade to 2026.6.11 without confirming stable status
- Previous scan (June 27 evening): "2026.6.11-beta.1 released June 24"
- Web research June 28 suggests 2026.6.11 may have progressed toward stable
- Key features if stable: per-DM model overrides, `openclaw agent --message-file` batch workflows, RAFT CLI wake bridge, richer Discord output, Slack relay mode, per-agent usage-cost reporting
- **Action:** After reaching 2026.6.10-stable, verify: `npm show openclaw@2026.6.11 version`; check ClawStat.us for status
- **DO NOT** skip 2026.6.10 — upgrade must land there before considering 2026.6.11

### F6 — NEW: Fast Talks Feature in 2026.6.10 (Not Previously Documented)
**Risk:** LOW — capability gap only; no action needed beyond awareness
- Fast Talks graduated from beta to stable in 2026.6.10
- Short conversational turns auto-enter fast mode; return to normal mode for longer work
- Relevant to Josh's conciseness preference — quick exchanges will be noticeably faster post-upgrade
- No configuration needed — automatic after upgrade to 2026.6.10

### F7 — MEDIUM: iMessage Paused Day 64, Heartbeat Cron Day 14
**Risk:** MEDIUM (status unchanged from June 27)
- iMessage: Day 64 since ~April 27, 2026. Auto-fix via upgrade to 2026.6.6 hop (SQLite migration).
- Heartbeat cron: **NOT DEPLOYED** — Day 14 since June 17. openclaw.json has no cron entries. Requires upgrade + OPENCLAW_TIMEZONE + cron config.
- Set OPENCLAW_TIMEZONE before deploying heartbeat cron (see F4)

### F8 — INFO: Noah Fleet Scope Broken — Day 19
**Status:** Fleet admin issue — no Noah coverage for 19 days
- Configured scope: `lylle-rgb/noah--repo` → 404 Not Found (access denied for this session)
- Actual repos confirmed: `lylle-rgb/Noahrepo2` and `lylle-rgb/Noah-workspace`
- Resolution: Fleet admin (lylle@lxrcap.com) updates session allowed repositories to include `lylle-rgb/Noahrepo2`

---

## Priority Action Table for Josh

| Priority | Action | Where | Effort | Impact |
|----------|--------|--------|--------|--------|
| 1 | Connect Google Workspace OAuth (Day 99 — Day 100 TOMORROW) | Browser → AlphaClaw General tab | ~5 min | Restores email + calendar immediately |
| 2 | Set `OPENCLAW_TIMEZONE=America/Los_Angeles` | AlphaClaw Envars tab | 1 min | Fixes cron schedule before heartbeat goes live |
| 3 | Upgrade OpenClaw to 2026.6.10 (staged) | VPS SSH → `openclaw update` | 1–2 hrs | Unlocks Active Memory, Dreaming, Fast Talks, heartbeat fix |
| 4 | Enable Active Memory + Dreaming after upgrade | openclaw.json via Browse tab | 10 min | Autonomous memory consolidation |
| 5 | Deploy heartbeat cron in openclaw.json | Browse tab → openclaw.json edit | 10 min | Restores proactive scheduled monitoring |
| 6 | Migrate model to gemini-3.5-flash | Browse tab → openclaw.json edit | 5 min | Eliminates preview deprecation risk |
| 7 | Set BRAVE_API_KEY in Envars tab | AlphaClaw Envars tab | 2 min | Enables web search for Heather |
| 8 | Fix fleet scope: noah--repo → Noahrepo2 | Fleet admin session config | 5 min | Restores Noah monitoring (Day 19) |

---

## What Changed Since Last Scan (June 27 Evening)
- **Day 100 is TOMORROW:** Google Workspace went from "2 days away" to "TOMORROW (June 29)" — maximum urgency
- **New:** Active Memory plugin (2026.4.10) and Dreaming (2026.4.9) documented — critical post-upgrade features previously unresearched
- **New:** OPENCLAW_TIMEZONE gap identified — must be set before heartbeat cron deployment
- **New:** Fast Talks (2026.6.10) documented — automatic speed improvement post-upgrade
- **New:** 2026.6.11 possible stable promotion flagged for verification
- **Updated:** All day counts incremented (Google Workspace: 98→99, iMessage: 63→64, heartbeat: 13→14, Noah scope: 18→19, 2026.6.10: Day 4→5)
- **Applied:** Day-100 escalation rule added to SOUL.md (was pending since June 27)
- **Applied:** Active Memory + Dreaming awareness added to SOUL.md Continuity section
- **Applied:** Emoji section cleanup applied to AGENTS.md
- **No change:** All other open issues persist (Noah scope, heartbeat cron, iMessage, model migration)

---

_Generated by AlphaClaw Fleet Research Agent — 2026-06-28 Evening_
_Scan scope: josh_repo workspace files, OpenClaw 2026.6.10/2026.6.11 changelogs, Active Memory/Dreaming docs, community reports, personal assistant improvements_
