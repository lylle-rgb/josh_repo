# Soul Improvements — Evening Scan, June 22, 2026

**Researcher:** AlphaClaw Fleet Agent  
**Instance:** josh_repo (Heather Schwartz — personal assistant)  
**Based on:** F33–F36 from 2026-06-22-evening-findings.md

---

## Priority Changes

### 1. TOOLS.md — Fix Version Conflict (HIGH PRIORITY)

**File:** `workspace/TOOLS.md`  
**Problem:** Still says "current safe target: 2026.6.6" and carries the 2026.6.8 hold notice. MEMORY.md says 2026.6.9. On a fresh session, if Heather reads TOOLS.md before MEMORY.md (or reads it for reference during an upgrade session), she could stop at 2026.6.6.

**Exact changes:**

In the `Platform` section, update the line:
```
- **Current safe target:** **2026.6.6** (npm `latest` stable channel as of June 19, 2026)
```
To:
```
- **Current safe target:** **2026.6.9-stable** (npm `latest` stable channel as of June 21, 2026)
```

Replace the hold warning block:
```
> ⚠️ **HOLD: Do NOT upgrade to 2026.6.8**
> v2026.6.8 has critical regressions in Discord image tools (#94266), memory-search (#94316),
> cron isolation, sub-agent tools, and misleading fallbacks. ClawStat.us verdict: "Wait for next release."
> npm `latest` still points to 2026.6.6 — 2026.6.8 was NOT promoted to stable.
> Wait for **2026.6.9-stable** before upgrading beyond 2026.6.6.

- **Staged upgrade path:** 2026.3.22 → 2026.5.27 → 2026.6.2 → 2026.6.5 → 2026.6.6 → **[STOP — wait for 2026.6.9-stable]**
```

With:
```
> ✅ **UPGRADE WINDOW OPEN: Target is 2026.6.9-stable** (released June 21, 2026)
> Skip 2026.6.8 entirely — critical regressions. Jump from 2026.6.6 → 2026.6.9 directly.
> Before running upgrade: `npm show openclaw@latest version` must return `2026.6.9`

- **Staged upgrade path:** 2026.3.22 → 2026.5.27 → 2026.6.2 → 2026.6.5 → 2026.6.6 → **2026.6.9** ✅
```

Also update the Discord streaming note:
```
- **Streaming:** Disabled (enable post-upgrade to 2026.6.9-stable)
```
Already correct — no change needed.

Update the auto-thread titles note:
```
- **Auto-thread titles:** Available after upgrade to 2026.6.9-stable
```
Already correct — no change needed.

**Risk:** LOW — file edit only, no runtime impact. Removes active confusion risk.

---

### 2. SOUL.md — Update Version Reference (LOW)

**File:** `workspace/SOUL.md`  
**Problem:** The error recovery section references a specific version number that may confuse Heather about her current state:

> "On OpenClaw 2026.6.6+: the gateway self-recovers from provider refresh failures — silent restarts are expected, not a crisis"

This is technically accurate (the feature shipped in 2026.6.6 and Heather is running 2026.3.22), but Heather might misread it as "I'm already on 2026.6.6+" if she's not tracking versions carefully.

**Recommended change:**

Replace:
```
- On OpenClaw 2026.6.6+: the gateway self-recovers from provider refresh failures — silent restarts are expected, not a crisis
```
With:
```
- On OpenClaw 2026.6.9+ (post-upgrade): the gateway self-recovers from provider refresh failures — silent restarts are expected, not a crisis. Until then, note restarts in memory.
```

**Risk:** LOW. Stylistic clarification only.

---

### 3. HEARTBEAT.md — Add Cron-Not-Deployed Note (MEDIUM)

**File:** `workspace/HEARTBEAT.md`  
**Problem:** HEARTBEAT.md describes what Heather should do on heartbeat triggers but does not acknowledge that the heartbeat cron is not actually deployed to the VPS. If Heather reads this file, she may wait for heartbeat messages that never come.

**Add to the top of HEARTBEAT.md** (under the last-updated line):

```
> ⚠️ **CRON NOT DEPLOYED (as of June 22, 2026):** The heartbeat cron is NOT running on the VPS.
> heartbeat-state.json has been all-null since June 17 (7 days). Heather does not receive
> scheduled heartbeat triggers until Josh adds the cron to openclaw.json and upgrades to 2026.6.9.
> Until then: run checks manually when Josh messages. Remind Josh once per main session that
> proactive monitoring is not running on schedule.
```

**Risk:** LOW. Documentation only, no runtime impact.

---

### 4. MEMORY.md — Add F33–F36 and Update Standing Alerts

**File:** `workspace/MEMORY.md`  
**Problem:** MEMORY.md has not been updated since June 21. Today's findings add material Heather should know.

**Append to "Lessons Learned" section:**
```
- TOOLS.md and MEMORY.md can drift out of sync on version targets — always read MEMORY.md for the authoritative upgrade target, not TOOLS.md
- 2026.6.10-beta.1 shipped June 21 — do NOT upgrade to beta. Stable target is still 2026.6.9.
- Bundle heartbeat cron deployment with the upgrade session — 2026.6.9 has cron reliability fixes that make it safer
```

**Update in "Platform Version Status" section:**
Change "2026.6.9-stable released TODAY (June 21)" to "2026.6.9-stable released June 21, 2026 — upgrade window has been open since then"

**Update in "Status as of June 21, 2026" section header** to "Status as of June 22, 2026" and update day counts:
- Google Workspace: Day 92 (was Day 91)
- Heartbeat null: Day 7 (was 5 consecutive days)
- iMessage paused: Day 57 (was 56)

**Risk:** LOW. Memory update only.

---

## Behavioral Recommendations (No File Changes — Heather Action Items)

### On Next Session with Josh
1. Remind Josh that the BRAVE_API_KEY is not set — web search is disabled. He can add it without upgrading via AlphaClaw UI → Envars tab.
2. Remind Josh it's been **7 days** with zero proactive heartbeat checks (cron not deployed).
3. Confirm: has Josh started the upgrade yet? If not, offer to walk him through it step by step.

### Upgrade Session Checklist (When Josh Is Ready)
Heather should walk Josh through this in order, not all at once:
1. `npm show openclaw@latest version` → confirm returns `2026.6.9`
2. Upgrade through staged path (one version at a time, test Discord + memory after each)
3. After reaching 2026.6.9: update openclaw.json with:
   - `userTimezone: "America/Los_Angeles"`
   - Dreaming config (if desired)
   - Compaction model alias fix
   - Heartbeat cron entry
   - Fallback model reorder (Haiku 4.5 as first fallback, Gemini as second)
4. Verify Discord messaging works before ending session
5. Update TOOLS.md to reflect new current version (2026.6.9 → active)

---

## What Has NOT Drifted (No Action Needed)

- **SOUL.md personality core:** Accurate. Josh's preferences (no emoji, no filler, directness) are correctly documented and marked STRICT.
- **USER.md:** Accurate. Josh's identity, businesses, timezone correct.
- **AGENTS.md:** Accurate. Heartbeat guidance, group chat rules, memory maintenance all correct.
- **IDENTITY.md:** Fine. Heather's name, creature type, vibe all correct.
- **Model config:** Primary (gemini-3-flash-preview) and Fallback 1 (gemini-3.5-flash) are current. Fallback 2 needs updating to `claude-haiku-4-5` post-upgrade (documented in MEMORY.md).
