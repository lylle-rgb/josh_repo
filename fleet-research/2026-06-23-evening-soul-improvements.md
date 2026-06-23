# Soul Improvements — Evening Scan, June 23, 2026

**Researcher:** AlphaClaw Fleet Agent
**Instance:** josh_repo (Heather Schwartz — personal assistant)
**Based on:** F38–F41 from 2026-06-23-evening-findings.md

---

## Changes Applied This Scan

### ✅ HEARTBEAT.md — Cron-Not-Deployed Warning (F38, Applied)

Added a visible warning block to `workspace/HEARTBEAT.md` acknowledging that the heartbeat cron is not currently deployed to the VPS. Prevents Heather from passively waiting for triggers that never arrive on a fresh session.

### ✅ MEMORY.md — Day Count Update (F41, Applied)

Updated MEMORY.md last-updated date to June 23, 2026. Day counts corrected:
- Google Workspace OAuth: Day 93
- Heartbeat null state: Day 8
- iMessage paused: Day 58

Added lessons learned: TOOLS.md/MEMORY.md drift risk, beta track awareness, cron-not-deployed awareness.

---

## New Recommendation (Post-Upgrade — Not Yet Applied)

### Recommendation 14 — Post-Upgrade: Discord Components V2 Behavioral Guidance

**Priority:** LOW — apply after Josh upgrades to 2026.6.9
**Why:** 2026.6.9 brings Discord Components V2. Heather gains native buttons, select menus, and modals. This enables interactive action confirmations — cleaner than text questions, directly serves Josh's "ask before acting externally" preference in SOUL.md.

**14a — Add to `workspace/SOUL.md` `## Boundaries` section post-upgrade:**
```
**Interactive confirmations (post-2026.6.9):** For external actions (sending emails, creating
calendar events, posting content), prefer a Discord button prompt over a text question. Use
a Confirm / Cancel button pair so Josh can respond with one tap. Only use buttons when an
action can actually be cancelled — don't add confirmation friction to time-sensitive sends.
```

**14b — Add to `workspace/AGENTS.md` `## Tools` section post-upgrade:**
```
**Discord Interactive Components (2026.6.9+):**
- Buttons for action confirmations (send email, create event, post content)
- Select menus for option selection (which calendar? which contact?)
- Modals for multi-field input (draft new event details)
- Use sparingly — one clear prompt beats a cascade of menus
```

**Risk:** LOW — post-upgrade behavioral guidance. No immediate action.

---

## Priority Order (Updated June 23)

| # | Action | File | Priority | Status |
|---|--------|------|----------|--------|
| 1 | Upgrade to 2026.6.9 (staged, skip 2026.6.8) | VPS shell | HIGH | ⏳ Window open Day 3 |
| 2 | Add `userTimezone` to openclaw.json (do FIRST) | openclaw.json | HIGH | ⏳ Pending |
| 3 | Enable Dreaming (verify key path first, Finding 36) | openclaw.json | HIGH | ⏳ Pending |
| 4 | Add compaction/memoryFlush block | openclaw.json | HIGH | ⏳ Pending |
| 5 | Add heartbeat cron job | openclaw.json | HIGH | ⏳ Pending |
| 6 | Connect Google Workspace OAuth | AlphaClaw UI | CRITICAL | ⏳ Day 93 |
| 7 | Post-upgrade: Components V2 guidance (Rec 14a/14b) | SOUL.md + AGENTS.md | LOW | After upgrade |
| 8 | Post-upgrade: fallback chain reorder (Rec 13b / F31) | openclaw.json | MEDIUM | After upgrade |
| 9 | Post-upgrade: enable Discord streaming (Rec 13b) | openclaw.json | LOW | After upgrade |
| 10 | Post-upgrade: update SOUL.md error recovery (Rec 13a/13e) | workspace/SOUL.md | LOW | After upgrade |
| 11 | Post-upgrade: update TOOLS.md version block (Rec 13b) | workspace/TOOLS.md | LOW | After upgrade |
| 12 | Post-upgrade: update MEMORY.md model config (Rec 13c) | workspace/MEMORY.md | LOW | After upgrade |
| 13 | Post-upgrade: add streaming note to AGENTS.md (Rec 13d) | workspace/AGENTS.md | LOW | After upgrade |
| 14 | Tighten Discord allowFrom (Finding 20) | openclaw.json | MEDIUM-HIGH | ⏳ Pending |

---

## What Has NOT Drifted (No Action Needed)

- **SOUL.md personality core:** Accurate. Josh's preferences (no emoji, no filler, directness) correctly marked STRICT.
- **USER.md:** Accurate. Josh's profile, businesses, timezone current.
- **AGENTS.md:** Accurate. Heartbeat guidance, group chat rules, memory maintenance all correct.
- **IDENTITY.md:** Fine. Heather's name, creature type, vibe current.
- **TOOLS.md:** Accurate. Version conflict resolved June 22 (F37). AlphaClaw UI links, Discord config, model config all current.
