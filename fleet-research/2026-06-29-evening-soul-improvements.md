# Soul Improvements — Josh / Heather — Evening Scan
**Date:** 2026-06-29 | **Scan:** Evening | **Agent:** AlphaClaw Fleet Research

---

## 1. SOUL.md — Day Count Computation Rule

**Current state:** SOUL.md has the Day 100 milestone rule: "At Day 100 and every 10 days after, surface to Josh proactively." This is good. But Heather must know HOW to compute the current day count on any given session, since MEMORY.md only has the day count as of the last fleet scan.

**Gap:** A fresh Heather session on June 30 might read "Day 100 as of June 29" in MEMORY.md and report "Day 100" when it's actually Day 101. The count drifts between fleet scans.

**Recommended addition to SOUL.md under "If a configuration gap has been open for 90+ days":**
```
- **Compute the CURRENT day count, not the stale one.** MEMORY.md records day counts as-of its last update date.
  To get today's number: take the MEMORY.md count, add days since the "as of" date, report that.
  Example: "Day 100 as of June 29" + 2 days later = Day 102. Always say Day 102, not Day 100.
- **On Day 100 and every 10 days after:** Surface with: "Today is Day [N]. [Issue] has been disconnected
  for [N] days. The fix: [concrete steps, 5 minutes]." Be specific, be direct, do not soften.
```

**Why it matters:** Accurate day counts make the urgency land harder with Josh. "Day 102" is more specific and actionable than a stale "Day 100."

**Risk:** LOW — clarification only, no behavioral change

---

## 2. AGENTS.md — Fleet Research in Session Startup

**Current state:** AGENTS.md session startup reads SOUL.md → USER.md → memory files. Fleet-research findings are mentioned in Lessons Learned (MEMORY.md) but not in the startup sequence itself.

**Gap:** A fresh session planning an upgrade might check TOOLS.md or HEARTBEAT.md for the version target without reading fleet-research — and act on a stale version number. This exact failure mode is documented in Lessons Learned but isn't surfaced at startup.

**Recommended addition to AGENTS.md "Session Startup" section, after step 4:**
```
5. **Before any upgrade, config change, or model migration:** Read the most recent
   `fleet-research/YYYY-MM-DD-*-findings.md` file first.
   - This is the authoritative source for current upgrade targets, skip-list versions, and config warnings
   - TOOLS.md and HEARTBEAT.md version numbers can be stale — fleet-research is ground truth
   - Never act on a version number in TOOLS.md or HEARTBEAT.md without cross-referencing fleet-research first
```

**Why it matters:** Prevents acting on stale upgrade targets. A missed skip of 2026.6.9 could corrupt email config and memory store.

**Risk:** LOW — adds one conditional check before upgrade/config actions

---

## 3. HEARTBEAT.md — Version Reference Staleness Warning (DONE in this scan)

**Current state:** HEARTBEAT.md referenced specific version numbers that could go stale between fleet scans. This scan added a staleness warning to the top of HEARTBEAT.md.

**Status:** Applied in this commit. No further action needed.

---

## 4. SOUL.md — Monthly Model Health Check Rule

**Current state:** SOUL.md mentions proactive web search during heartbeats (Bliss brand, Oben HiFi news) but doesn't include a specific model deprecation check.

**Gap:** Gemini preview deprecations happen with minimal notice. The lesson from F43 (flagged CRITICAL by evening scan, confirmed safe by morning scan) shows fleet research can lag or be wrong. Heather should independently verify once a month.

**Recommended addition to SOUL.md under "If web search is enabled (BRAVE_API_KEY configured)":**
```
**Monthly model health check (first heartbeat of each month):**
Check https://ai.google.dev/gemini-api/docs/deprecations. If any model in openclaw.json appears
in the deprecation list, surface to Josh immediately with migration steps.
Do not wait for the announced shutdown date — migrate proactively.
Note the check date in memory/heartbeat-state.json under "model_health_check".
```

**Why it matters:** Provides Heather an independent check between fleet scans. Preview model retirements can happen with as little as 2-4 weeks notice.

**Risk:** LOW — additive behavior, only triggers when BRAVE_API_KEY is configured

---

## 5. TOOLS.md — Add Post-Upgrade Features Checklist

**Current state:** TOOLS.md lists post-upgrade features for 2026.6.10 (streaming, auto-thread titles) but is missing Task Flow, Skill Workshop, openclaw backup, and the openclaw migrate command.

**Recommended addition to TOOLS.md under "Platform" section, after upgrade path:**
```
### Post-Upgrade Actions (after reaching 2026.6.10-stable)
1. Run `openclaw backup create` BEFORE starting the upgrade (snapshots state)
2. Enable Active Memory plugin in openclaw.json (pre-reply context sub-agent)
3. Enable Dreaming in openclaw.json (background memory consolidation)
4. Enable streaming in Discord config (set `"streaming": "on"`)
5. Explore Skill Workshop for managing future skill upgrades via UI
6. Consider Task Flow for scheduling async background tasks (email summaries, research)

### openclaw migrate (available at 2026.3.22+)
- `openclaw migrate --plan` — preview what will change
- `openclaw migrate --dry-run` — simulate migration without applying
- Use before each hop in the staged upgrade path
```

**Why it matters:** Gives Heather a complete post-upgrade checklist so nothing is missed during the upgrade session with Josh.

**Risk:** LOW — documentation only

---

## 6. SOUL.md — Noah Fleet Scope Gap (Fleet Admin Note)

**Current state:** MEMORY.md documents the Noah scope issue. SOUL.md has no mention of fleet scope problems.

**Gap:** Heather has no awareness that another OpenClaw instance (Noah's Market Catalyst Agent) is in the same fleet but currently invisible to fleet research. If something cross-fleet matters (e.g., OpenClaw upgrade path advice), Heather has no context that Noah exists.

**Recommended addition to SOUL.md under "Continuity":**
```
**Fleet context:** You are one of two OpenClaw instances managed by AlphaClaw Apex.
- Josh (you — Heather): personal assistant, Discord bot
- Noah: Market Catalyst Agent — stock catalyst hunter, paper trading
You don't interact with Noah's instance directly. Fleet admin (lylle) manages both.
If fleet research ever covers Noah findings, note the cross-fleet context.
```

**Why it matters:** Gives Heather context that she's part of a fleet, not an isolated deployment. Useful if fleet admin decisions or OpenClaw upgrade paths affect both instances.

**Risk:** LOW — informational addition only
