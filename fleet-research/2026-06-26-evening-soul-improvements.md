# Soul Improvements — June 26, 2026 Evening Scan
## Heather Schwartz — Josh Personal Assistant

**Based on:** Evening findings F56-F61, Day 97 Google OAuth milestone, Day 12 heartbeat null, Day 17 Noah scope broken

---

## Applied This Scan

### ✅ MEMORY.md Day Count Update (Rec 22)

All day counts advanced from June 25 morning to June 26 evening:
- Google Workspace OAuth: Day 96 → **Day 97** (Day 100 in 3 days)
- iMessage paused: Day 61 → **Day 62**
- Heartbeat null: Day 11 → **Day 12**
- Noah scope broken: Day 16 → **Day 17**
- 2026.6.10 stable window: Day 2 → **Day 3**
- Status section date updated to June 26 evening

Applied to `workspace/MEMORY.md` in this commit.

---

## New Recommendations This Scan

### Rec 22 — MEMORY.md: Day Count Advancement (Applied Above)

**Priority: LOW — Applied in this commit**

See applied section above. All day counts updated; status section date moved to June 26 evening.

---

### Rec 23 — SOUL.md: Google OAuth Day 100 Urgency Trigger

**Priority: MEDIUM — Apply via Browse tab**

**Why:** Day 100 of Google Workspace disconnection arrives June 29 (3 days). Heather should know to mention this milestone at the next main session with a concrete frame — not a vague reminder but a milestone call-out. The current SOUL.md error recovery section covers what to do if tools fail, but doesn't guide Heather on how to escalate persistently unresolved configuration gaps to Josh with appropriate urgency.

**Add to `workspace/SOUL.md` under `## When Things Break` (after the Google Workspace tools section):**
```
**For configuration gaps that have been open 90+ days:**
- Name the duration explicitly when mentioning it to Josh — "Day 97" lands harder than "a while"
- On Day 100 and each 10-day milestone after, surface it proactively even if Josh hasn't asked
- Pair the gap with its fix: "You can connect Google Workspace in 5 minutes at the AlphaClaw General tab"
- Do not just log it to memory and move on — Josh may not be checking fleet-research files
```

**Risk:** LOW. Behavioral guidance only — no functional change.

---

### Rec 24 — MEMORY.md: Noah Scope Confirmation Note

**Priority: LOW — Applied in this commit**

**Why:** The Noah scope issue (F49/F55/F60) has been documented as a broken repo name (`noah--repo` → 404). This scan confirmed via GitHub search that the actual repos are `lylle-rgb/Noahrepo2` and `lylle-rgb/Noah-workspace`. This fact should be in MEMORY.md so Heather has it for context when Josh discusses fleet operations, even though Heather doesn't directly manage fleet scope.

**Applied to MEMORY.md Operational Context in this commit:** Added note that Noah's actual repo identifiers are Noahrepo2 and Noah-workspace (confirmed June 26), session scope fix pending fleet admin.

**Risk:** LOW. Memory context only.

---

### Rec 25 — TOOLS.md: RAFT CLI Wake Bridge as Cron Alternative

**Priority: LOW — Deferred to 2026.6.11 stable**

**Why:** 2026.6.11-beta.1 introduces RAFT, a CLI wake bridge that lets external processes trigger an OpenClaw agent session (F61). For Josh's setup, this is a meaningful addition: if the internal heartbeat cron continues to be unreliable post-upgrade, RAFT provides an external trigger path that doesn't depend on OpenClaw's internal scheduler.

**Defer action — add to `workspace/TOOLS.md` under `## Platform` when 2026.6.11 is stable:**
```
## RAFT CLI Wake Bridge (2026.6.11+)
- **What:** External plugin that lets a CLI process remotely wake an OpenClaw agent session
- **Use case for Heather:** If heartbeat cron is unreliable, RAFT can act as an external fallback trigger
- **Also useful:** `openclaw agent --message-file` runs agent tasks from a file without an interactive session
  — powers batch email processing, bulk memory updates, weekly calendar briefings
- **Install:** `openclaw skill install @openclaw/raft` (after upgrading to 2026.6.11)
```

**Risk:** LOW. Deferred. No change needed today.

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
| 19 | MEMORY.md: Day 100 Google OAuth milestone note | ✅ Applied June 25 |
| 20 | SOUL.md: per-DM model override awareness | 🔬 Deferred to 2026.6.11 stable |
| 21 | MEMORY.md: Gemini deprecation cadence lesson | ✅ Applied June 25 |
| **22** | **MEMORY.md: Day count advancement (97/62/12/17/Day3)** | **✅ Applied June 26 (this commit)** |
| **23** | **SOUL.md: Google OAuth Day 100 urgency trigger** | **🆕 Added June 26 — apply Browse tab** |
| **24** | **MEMORY.md: Noah scope confirmed (Noahrepo2 + Noah-workspace)** | **✅ Applied June 26 (this commit)** |
| **25** | **TOOLS.md: RAFT wake bridge + --message-file documentation** | **🆕 Added June 26 — deferred to 2026.6.11** |

---

## Priority Actions Summary (June 26)

**Do NOW — AlphaClaw UI only (no VPS needed):**
1. **[CRITICAL]** Connect Google Workspace OAuth → https://5.78.142.81.sslip.io#general — **Day 97, Day 100 in 3 days**
2. **[MEDIUM-HIGH]** Migrate primary model → `google/gemini-3.5-flash` (Browse tab → openclaw.json — do BEFORE upgrade)
3. **[MEDIUM-HIGH]** Set BRAVE_API_KEY in Envars tab (enables web search, no upgrade needed)
4. **[MEDIUM]** Apply Rec 23: Day 100 urgency trigger in SOUL.md (Browse tab)

**Bundle in VPS upgrade session (SSH required):**
5. **[HIGH]** Staged upgrade: 2026.3.22 → 2026.5.27 → 2026.6.2 → 2026.6.5 → 2026.6.6 → **2026.6.10**
6. **[HIGH]** Add to openclaw.json before upgrading: `userTimezone`, `dreaming`, `compaction`, heartbeat `cron` block

**After upgrade (2026.6.10):**
7. Apply Rec 13 (post-upgrade workspace file updates)
8. Apply Rec 14 (Discord Components V2 guidance in AGENTS.md)
9. Apply Rec 17 (monthly model health check in HEARTBEAT.md)
10. Apply Rec 18 (SOUL.md silent model failure awareness)

**Deferred (2026.6.11 stable):**
11. Apply Rec 20 (SOUL.md: per-DM model override awareness)
12. Apply Rec 25 (TOOLS.md: RAFT wake bridge documentation)

**Fleet admin (not Heather):**
13. **[FLEET OPS]** Fix session scope: replace `noah--repo` (404) with `Noahrepo2` → then run full Noah audit
