# Soul Improvements: Josh (Heather) — Evening Scan
**Date:** 2026-06-28 | **Scan type:** Evening | **Agent:** AlphaClaw Fleet Research

## Overview
Day 100 arrives TOMORROW — the Day-100 escalation rule from June 27 (pending) is now urgent and has been
applied to SOUL.md in this scan. AGENTS.md emoji cleanup (LOW, pending since June 27) also applied.
Three new awareness items added to SOUL.md: Active Memory + Dreaming post-upgrade behavior, web search
proactive use. MEMORY.md and HEARTBEAT.md day counts updated. OPENCLAW_TIMEZONE lesson added to MEMORY.md.

---

## Improvement 1 — SOUL.md: Day-100 Escalation Rule (APPLIED THIS SCAN)
**File:** `workspace/SOUL.md`
**Status:** Applied
**Reason:** Day 100 arrives TOMORROW (June 29). This rule needs to live in SOUL.md so it fires in ALL
session types — Discord, group chats, and main sessions alike. MEMORY.md is main-session only; SOUL.md
is always loaded. Was flagged MEDIUM priority in June 27 scan; promoted to HIGH with Day 100 arriving
tomorrow.

**Applied addition** (after "If a tool or integration fails" block in "When Things Break" section):
```
**If a configuration gap has been open for 90+ days:**
- Name the duration explicitly: "Day 99" lands better than "it's been a while"
- At Day 100 and every 10 days after, surface to Josh proactively with the concrete fix steps — not just a mention
- Frame urgency relative to milestone: "Today is Day 99 — Day 100 is tomorrow" is actionable framing
- Don't wait to be asked — this is exactly when proactive outreach is warranted
```

---

## Improvement 2 — SOUL.md: Active Memory + Dreaming Continuity Note (APPLIED THIS SCAN)
**File:** `workspace/SOUL.md`
**Status:** Applied
**Reason:** After upgrading to 2026.6.10, two major memory features become available (Active Memory plugin,
Dreaming consolidation). SOUL.md's Continuity section should reference them so Heather knows to enable them
post-upgrade and understands how her memory model will evolve.

**Applied addition** (at end of "Continuity" section):
```
After upgrading to OpenClaw 2026.6.10+:
- Active Memory plugin: Enable in openclaw.json — pre-reply memory sub-agent that automatically recalls
  relevant context before each response
- Dreaming: Enable in openclaw.json — background consolidation that promotes strong signals from daily
  notes into MEMORY.md automatically (Light Sleep → REM → Deep Sleep)
- Together these form a "remember-consolidate-recall" loop that makes memory maintenance largely autonomous
```

---

## Improvement 3 — AGENTS.md: Clean Up Suspended Emoji Section (APPLIED THIS SCAN)
**File:** `workspace/AGENTS.md`
**Status:** Applied (carried forward from June 27 — LOW priority, now applied)
**Reason:** The "React Like a Human!" section was SUSPENDED but still contained ~250 words of instructions
explicitly marked as not applying to this instance. Every session read processed content meant to be ignored.
Replaced with a single-line suspension statement to eliminate cognitive noise.

**Before:** ~300-word block including full emoji reaction guidance (all marked "does not apply")
**After:** Single line: "⛔ SUSPENDED FOR THIS INSTANCE — Josh has stated NO emoji reactions, ever, on any platform."

---

## Improvement 4 — SOUL.md: Web Search Awareness (APPLIED THIS SCAN)
**File:** `workspace/SOUL.md`
**Status:** Applied
**Reason:** BRAVE_API_KEY is fifth priority for Josh and can be set now with no upgrade. Once set, Heather
should use web search proactively during heartbeats. Guidance belongs in SOUL.md (always loaded) not just
MEMORY.md (main session only).

**Applied addition** (in "Be resourceful before asking" section):
```
If web search is enabled (BRAVE_API_KEY configured): Use it proactively during heartbeats — check for
Bliss brand mentions, Oben HiFi news, relevant business contacts. Bring relevant news to Josh without
being asked. Focus on things he'd want to know but hasn't thought to check.
```

---

## Improvement 5 — HEARTBEAT.md: Day Count Updates (DONE IN THIS SCAN)
**File:** `workspace/HEARTBEAT.md`
**Status:** Done
- Google Workspace: Day 98 → **Day 99**
- "Day 100 arrives June 29 (2 days)" → **"Day 100 arrives TOMORROW (June 29)"**
- Cron undeployed: "13 days as of June 27" → **"14 days as of June 28"**
- Added OPENCLAW_TIMEZONE warning to the cron-not-deployed notice
- Updated scan date in header

---

## Improvement 6 — MEMORY.md: Day Counts + New Findings (DONE IN THIS SCAN)
**File:** `workspace/MEMORY.md`
**Status:** Done
- Last updated: June 27 → **June 28**
- Google Workspace: Day 98 → **Day 99**, "2 days" → **"TOMORROW"**
- iMessage: Day 63 → **Day 64**
- Noah scope: Day 18 → **Day 19**
- Heartbeat: 13+ days → **14+ days**
- 2026.6.10-stable: Day 4 → **Day 5**
- Added new sections: Active Memory + Dreaming, OPENCLAW_TIMEZONE, 2026.6.11 status check
- Added lessons: OPENCLAW_TIMEZONE must be set before cron deployment; Active Memory + Dreaming opt-in post-upgrade
- Updated Status section date and all day counts

---

## What's Healthy — Don't Change
- **SOUL.md core values:** Sharp, concise, resourceful, no filler. No drift detected. Correct for Josh.
- **Josh's hard rules:** No emoji, no filler, concise — consistently enforced across SOUL.md, AGENTS.md, TOOLS.md. No drift.
- **Error recovery procedures:** Write-to-file-before-giving-up pattern well-specified and correct.
- **Heartbeat rotation logic** (AGENTS.md): When to speak / stay silent guidance is excellent.
- **MEMORY.md lessons learned:** Growing well, cumulative knowledge strong. No stale or contradicted lessons.
- **Upgrade skip warnings** (MEMORY.md + TOOLS.md): 2026.6.8 and 2026.6.9 skip consistently documented.
- **Platform formatting notes** (no tables in Discord/WhatsApp, suppress embeds): Correct and practical.
- **TOOLS.md AlphaClaw UI quick reference:** Accurate and useful.

---

## Priority Summary

| Improvement | File | Priority | Status |
|-------------|------|----------|--------|
| 1. Day-100 escalation rule | SOUL.md | HIGH (Day 100 TOMORROW) | **Applied this scan** |
| 2. Active Memory + Dreaming awareness | SOUL.md | MEDIUM | **Applied this scan** |
| 3. Emoji section cleanup | AGENTS.md | LOW | **Applied this scan** |
| 4. Web search awareness | SOUL.md | LOW | **Applied this scan** |
| 5. HEARTBEAT.md day count updates | HEARTBEAT.md | HIGH | **Done this scan** |
| 6. MEMORY.md day counts + new findings | MEMORY.md | HIGH | **Done this scan** |

---

_Generated by AlphaClaw Fleet Research Agent — 2026-06-28 Evening_
