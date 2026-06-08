# Soul Improvements — Heather Schwartz
**Instance:** Josh — personal assistant
**Date:** 2026-06-08 (evening scan)
**Based on:** Codebase analysis + OpenClaw 2026.6.x research

_Full detail in `fleet-research/soul-improvements.md` (current). This is the dated archive copy._

---

## Priority Order

| # | Action | File | Priority |
|---|--------|------|----------|
| 1 | Create MEMORY.md | workspace/memory/MEMORY.md | CRITICAL |
| 2 | Configure HEARTBEAT.md with email/calendar checks | workspace/HEARTBEAT.md | HIGH |
| 3 | Add Josh-specific rules to SOUL.md (no emoji reactions, Bliss/Oben HiFi context, LA timezone) | workspace/SOUL.md | HIGH |
| 4 | Add memory discipline doctrine to SOUL.md | workspace/SOUL.md | HIGH |
| 5 | Update TOOLS.md with actual setup (Google OAuth URL, Discord guild, iMessage status) | workspace/TOOLS.md | MEDIUM |
| 6 | Add error recovery posture to SOUL.md | workspace/SOUL.md | MEDIUM |
| 7 | Add personal assistant identity to SOUL.md Vibe section | workspace/SOUL.md | MEDIUM |
| 8 | Delete BOOTSTRAP.md | workspace/BOOTSTRAP.md | LOW |
| 9 | Fix inbox-state.json duplicate key | workspace/memory/inbox-state.json | LOW |
| 10 | Update OpenClaw (2026.3.22 → 2026.6.2) on VPS | VPS shell | HIGH |

---

## Key Recommendations

### 1. Josh-Specific Rules for SOUL.md
- No emoji reactions (STRICT, Josh explicitly requested)
- Professional communications (luxury brand context)
- LA time awareness (PST/PDT)
- Discord as primary channel

### 2. Memory Discipline
- Create MEMORY.md immediately
- Write it down or it's gone — mental notes die on restart
- MEMORY.md = identity across time

### 3. Heartbeat Schedule
- ~9:00 AM PST: email + calendar
- ~1:00 PM PST: urgent items
- ~6:00 PM PST: daily summary
- Silent 11 PM–8 AM

### 4. Error Recovery
- Be honest about failures
- Retry once, then diagnose
- OAuth expires → direct to AlphaClaw General tab
- Gateway down → wait 60s, watchdog will recover

### 5. Skill Workshop (after 2026.6.2 update)
- Morning brief format
- Google contact lookup
- Weekly summary generation

### 6. dreaming Memory System
- Light: daily note scan
- Deep: weekly files → MEMORY.md distillation
- REM: full MEMORY.md review and pruning
