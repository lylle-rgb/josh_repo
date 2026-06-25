# Soul Improvements — June 25, 2026 Evening Scan
## Heather Schwartz — Josh Personal Assistant

**Based on:** Evening findings F50-F52, Day 96 milestones, HEARTBEAT.md fix applied this commit

---

## Applied This Scan

### ✅ HEARTBEAT.md Version Reference Fix (F51)

The cron deployment warning in HEARTBEAT.md referenced the now-broken 2026.6.9 as the upgrade target.
Updated to reference 2026.6.10-stable (released June 24). Day count updated (8 days → 11 days). Applied in this commit.

---

## New Recommendations This Scan

### Rec 19 — MEMORY.md: Note Day 100 Google OAuth Milestone

**Priority: LOW — Applied in this commit**

**Why:** Google Workspace OAuth has been disconnected for 96 days. In 4 days (June 29), it will be Day 100 — email and calendar inaccessible for over 3 months. Heather should mention this milestone to Josh proactively at the next main session. The day count framing makes the urgency concrete rather than abstract.

**Applied to MEMORY.md in this commit:** Added Day 100 milestone warning under Known Configuration Issues and Status sections.

**Risk:** LOW. Memory note only.

---

### Rec 20 — SOUL.md: Add Per-DM Model Override Awareness (Deferred — 2026.6.11)

**Priority: LOW — Defer until 2026.6.11 is stable**

**Why:** 2026.6.11-beta.1 (F50) introduces per-DM model overrides. Once stable, Heather can be configured with a lighter model for casual Josh DMs and the primary for complex tasks. SOUL.md should document how to handle this so Heather knows it's intentional when a DM session feels "lighter", not a degradation.

**Deferred action — add to `workspace/SOUL.md` under `## Tools` when 2026.6.11 is stable:**
```
**Per-DM model overrides (2026.6.11+):** Some DM conversations may be configured to use a lighter
model. If this applies, treat it as intentional — not degraded. For tasks that clearly exceed a
lightweight model's capability (complex calendar management, multi-step research), note the
limitation to Josh rather than silently producing lower-quality results.
```

**Risk:** LOW. Deferred. No change needed today.

---

### Rec 21 — MEMORY.md: Gemini Deprecation Cadence as Lesson Learned

**Priority: LOW — Applied in this commit**

**Why:** The June 25 Gemini shutdown wave arrived exactly on schedule (F52), confirming that Google's deprecation cadence is reliable and predictable. This should be captured as a lesson: preview model sunsets aren't "maybe someday" — they happen on the announced date. Heather should treat any preview model as having a finite, measurable life.

**Applied to MEMORY.md Lessons Learned in this commit:** Added note on Gemini shutdown cadence and the importance of proactive migration to GA stable models.

**Risk:** LOW. Memory addition only.

---

## Prior Recommendations Status

| Rec | Description | Status |
|-----|-------------|--------|
| 1–12 | Emoji, memory, identity, heartbeat, behavioral, gateway, model checks | ✅ All resolved June 16–17 |
| 13 | Post-2026.6.10 upgrade: SOUL.md / TOOLS.md / MEMORY.md updates | ⏳ Pending upgrade |
| 14 | Post-upgrade: Discord Components V2 guidance | ⏳ Pending upgrade |
| 15 | HEARTBEAT.md cron-not-deployed warning | ✅ Resolved June 23 |
| 16 | MEMORY.md day count staleness | ✅ Resolved June 23 |
| 17 | Monthly model health check in HEARTBEAT.md | ⏳ Apply anytime (Browse tab) |
| 18 | SOUL.md: silent model failure awareness | ⏳ Apply anytime (Browse tab) |
| **19** | **MEMORY.md: Day 100 Google OAuth milestone note** | **✅ Applied June 25 (this commit)** |
| **20** | **SOUL.md: per-DM model override awareness** | **🆕 Added June 25 — deferred to 2026.6.11** |
| **21** | **MEMORY.md: Gemini deprecation cadence lesson** | **✅ Applied June 25 (this commit)** |

---

## Priority Actions Summary (June 25)

**Do NOW — AlphaClaw UI only (no VPS needed):**
1. **[CRITICAL]** Connect Google Workspace OAuth → https://5.78.142.81.sslip.io#general (Day 96 — Day 100 in 4 days)
2. **[MEDIUM-HIGH]** Migrate primary model → `google/gemini-3.5-flash` (Browse tab → openclaw.json, no upgrade needed)
3. **[MEDIUM-HIGH]** Set BRAVE_API_KEY in Envars tab (Finding 30 — enables web search now)
4. **[LOW]** Apply Rec 17: monthly model check block in HEARTBEAT.md (Browse tab)
5. **[LOW]** Apply Rec 18: SOUL.md silent model failure awareness (Browse tab)

**Bundle in VPS upgrade session:**
6. **[HIGH]** Add userTimezone, dreaming, compaction, heartbeat cron to openclaw.json
7. **[HIGH]** Run staged upgrade: 2026.3.22 → 2026.5.27 → 2026.6.2 → 2026.6.5 → 2026.6.6 → 2026.6.10

**After upgrade (2026.6.10):**
8. Apply Rec 13 (post-upgrade workspace file updates)
9. Apply Rec 14 (Discord Components V2 behavioral guidance)

**Deferred (2026.6.11 stable):**
10. Apply Rec 20 (per-DM model override awareness in SOUL.md)
