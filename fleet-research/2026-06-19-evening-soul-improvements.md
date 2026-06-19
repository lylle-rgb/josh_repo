# Soul & Workspace Improvements — Josh (Heather Schwartz) | 2026-06-19 Evening Scan

**Scan type:** Recursive improvement recommendations
**Date:** 2026-06-19 (evening)
**Instance:** Josh Meyers — Heather Schwartz (personal assistant)
**Prior scan:** 2026-06-18 evening

---

## Summary

Three consecutive days of null heartbeat-state.json and an incorrect upgrade recommendation in yesterday's scan are the two drivers of tonight's recommendations. Both are fixable with targeted language additions to SOUL.md and AGENTS.md. None of the recommendations below change Heather's personality or core behavior — they close specific execution gaps.

---

## Recommendation 1 — Add Mandatory State-Write Rule to SOUL.md

**Target:** `workspace/SOUL.md`
**Priority:** HIGH
**Risk:** LOW — additive only

**Problem:** Three consecutive scans confirm heartbeat-state.json has all null timestamps. The AGENTS.md guidance says to track checks in heartbeat-state.json, but does not make the write a hard requirement with consequences language. Heather returns HEARTBEAT_OK without evidence of what ran.

**Suggested addition to SOUL.md** under the existing "When Things Break" section or as a new block:

```markdown
## Non-Negotiables (Zero Tolerance)

**State writes after heartbeats:**
Every heartbeat poll must end with an updated heartbeat-state.json — even if all checks
silently no-op'd. Write the file. Then return HEARTBEAT_OK. Returning HEARTBEAT_OK without
updating the state file is a silent failure mode that makes it impossible to know whether
Heather is working or ghost-running.

**No mental notes:**
If you checked something: timestamp it. If you learned something: file it. If you made a
decision: write it down. Session memory ends at restart. File memory does not.
```

**Why SOUL.md and not just AGENTS.md:** SOUL.md is re-read at every session start. It is the highest-authority document. Operational rules that keep being missed belong here.

---

## Recommendation 2 — Add Platform Version Awareness to SOUL.md

**Target:** `workspace/SOUL.md`
**Priority:** HIGH
**Risk:** LOW — additive only

**Problem:** The June 18 scan incorrectly called 2026.6.8 "confirmed stable" based on the release notes alone, without checking the community regression tracker. If Heather ever self-assesses the platform version, she carries the same risk.

**Suggested addition to SOUL.md** (under "When Things Break" — platform section):

```markdown
**Before acting on any upgrade instruction:**
- Read the most recent file in `fleet-research/` for current upgrade target and regression warnings
- Check `TOOLS.md` → Platform section for the safe upgrade target
- Do NOT act on version numbers from memory — the target changes
- When in doubt: staying on an older stable version is always safer than upgrading into regressions
```

---

## Recommendation 3 — Add Upgrade Hold Warning to HEARTBEAT.md

**Target:** `workspace/HEARTBEAT.md`
**Priority:** HIGH (time-sensitive — active regression)
**Risk:** LOW — visibility note

**Problem:** HEARTBEAT.md is Heather's operational checklist. If Josh runs a heartbeat and asks about the platform, Heather should know immediately that 2026.6.8 is on hold.

**Suggested addition at the very top of HEARTBEAT.md** (after the header/date line):

```markdown
> ⚠️ **UPGRADE HOLD (as of June 19, 2026):** Do NOT upgrade to OpenClaw 2026.6.8.
> Critical regressions: Discord image tools (#94266), memory-search (#94316), cron jobs.
> Current safe target: **2026.6.6**. Wait for 2026.6.9-stable. See TOOLS.md for full path.
```

---

## Recommendation 4 — Strengthen Heartbeat State-Write Language in AGENTS.md

**Target:** `workspace/AGENTS.md`
**Priority:** MEDIUM
**Risk:** LOW — additive only

**Problem:** The heartbeat section says to track state in heartbeat-state.json but frames it as a "tip" rather than a hard requirement. Strengthening the language may close the gap.

**Suggested addition in AGENTS.md** under the "💓 Heartbeats" section, before the "Things to check" list:

```markdown
### The State-Write Contract

After EVERY heartbeat poll — even one where all checks silently skip:
1. Open `memory/heartbeat-state.json`
2. Update the Unix-ms timestamp for each check you ran (or attempted)
3. Write the file to disk
4. THEN return HEARTBEAT_OK

Breaking this contract makes heartbeat state meaningless. It is not optional.
```

---

## Recommendation 5 — Add "iMessage Status Check Is Always On" Note to HEARTBEAT.md

**Target:** `workspace/HEARTBEAT.md`
**Priority:** MEDIUM
**Risk:** LOW

**Problem:** HEARTBEAT.md says to "skip silently" when Google Workspace is not connected. The risk is that Heather skips ALL checks silently, including the iMessage status check which does NOT require Google. This may be why the state file is never written — Heather conflates "Google not connected" with "nothing to check."

**Suggested clarification in HEARTBEAT.md** under the "Daily: iMessage Status Check" section:

```markdown
> **This check does NOT require Google Workspace.** It reads `memory/inbox-state.json` locally.
> Run it at every morning heartbeat regardless of Google connection status.
> After running: update `heartbeat-state.json` → `imessage` timestamp before returning.
```

---

## What Was Applied Tonight vs. Recommended

### Applied Tonight (GitHub-direct)
- `workspace/TOOLS.md` — corrected upgrade target (2026.6.8 → hold/2026.6.9)
- `workspace/MEMORY.md` — corrected platform target + regression note + date
- `fleet-research/2026-06-19-evening-findings.md` — this scan's findings
- `fleet-research/2026-06-19-evening-soul-improvements.md` — this document

### Recommended (Josh should review and apply)
The recommendations above (SOUL.md additions, AGENTS.md strengthening, HEARTBEAT.md warning) modify operational behavior. Fleet agents apply them conservatively — soul files in particular should be reviewed by Josh before update, as they define who Heather is.

**Highest-priority action for Josh:** Confirm heartbeat checks are running. Ask Heather in Discord: "Did you check iMessage this morning?" Her answer will tell you whether the execution gap is the state-write, the check itself, or the heartbeat trigger.

---

## What's Working Well (No Changes Needed)

- **SOUL.md personality:** Sharp, correct, Josh-specific. No drift detected.
- **AGENTS.md overrides:** NO emoji reactions rule prominently placed and repeated. Correct.
- **USER.md:** Accurate and current.
- **IDENTITY.md:** Correct (Heather, Sharp/Helpful/Resourceful).
- **Model fallback chain:** Correctly configured post-June 16 (gemini-2.5-flash replaced with gemini-3.5-flash).
- **Discord config:** Guild ID correct, no @mention required, streaming appropriately off for current version.
- **HEARTBEAT.md schedule:** Well-designed, respects quiet hours, batches checks properly.
