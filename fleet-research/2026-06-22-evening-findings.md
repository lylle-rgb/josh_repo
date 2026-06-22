# Evening Scan — June 22, 2026

**Researcher:** AlphaClaw Fleet Agent  
**Time:** Evening, June 22, 2026  
**Previous scan:** June 21 Morning — 2026.6.9-stable confirmed, upgrade window opened, F30-F32 filed  
**Instance:** josh_repo (Heather Schwartz — personal assistant)

---

## Headline: TOOLS.md Version Conflict Found. 2026.6.10-beta.1 Shipped. Heartbeat Null Day 7. Upgrade Window Still Open.

---

## New Findings This Scan

### F33 — TOOLS.md / MEMORY.md Version Conflict (HIGH) 🔴

**What's wrong:** TOOLS.md and MEMORY.md now contradict each other on the OpenClaw upgrade target.

- **MEMORY.md** (correctly updated June 21): safe target = `2026.6.9-stable`
- **TOOLS.md** (stale): says "Current safe target: **2026.6.6**" and still carries the `⚠️ HOLD: Do NOT upgrade to 2026.6.8` warning block

If Heather reads TOOLS.md before MEMORY.md on a fresh session, she may stop the upgrade at 2026.6.6 and consider the job done. This file contradiction is an active risk.

**Fix — exact TOOLS.md change required:**

In the `## Platform` section, replace the entire block:

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
> Skip 2026.6.8 entirely — it had critical regressions. Jump directly from 2026.6.6 to 2026.6.9.
> Verify before running: `npm show openclaw@latest version` should return `2026.6.9`

- **Staged upgrade path:** 2026.3.22 → 2026.5.27 → 2026.6.2 → 2026.6.5 → 2026.6.6 → **2026.6.9** ✅
```

Also update the `Current safe target` line from `2026.6.6` to `2026.6.9-stable`.

**Risk:** HIGH. File contradiction causes agent confusion on fresh sessions.

---

### F34 — OpenClaw 2026.6.10-beta.1 Released (Informational) ℹ️

A new pre-release shipped June 21, 2026 (same day as 2026.6.9-stable). It covers 109 PRs including:
- More reliable agent turns and session state preservation
- Pending subagent completion announcements now preserved across restarts
- Session history transcripts kept non-empty through compaction
- Media index alignment maintained through retries
- Dormant follow-up drains now auto-restart
- Compaction model alias resolution fixed
- Codex SecretRefs, thread context, and bounded turn text
- Richer Discord/Slack/Telegram delivery

**Assessment:** DO NOT upgrade to 2026.6.10-beta.1. It is a pre-release (alpha track). Stable target remains 2026.6.9. This beta's improvements should land in 2026.6.10-stable in roughly 1–2 weeks at current cadence.

**Action:** None. Monitor for 2026.6.10-stable. Current target unchanged: 2026.6.9.

**Risk:** LOW (no action needed, just awareness).

---

### F35 — AlphaClaw 0.9.18 Remote MCP Feature Underutilized (LOW) 🟡

AlphaClaw 0.9.18 (current, released June 1) added **remote MCP server config via the UI Envars tab** — no VPS SSH required to connect new integrations. This is already on Josh's server but likely unknown/unused.

Practical use cases for Heather:
- **Notion MCP:** Connect Heather to Josh's Notion workspace for project tracking
- **Calendly MCP:** Read Josh's Calendly booking state without Google Calendar OAuth
- **Linear/GitHub MCP:** If Josh uses these for Bliss or Oben HiFi projects

This doesn't replace the Google Workspace OAuth gap, but it's a way to add useful integrations without waiting for the upgrade or SSH.

**Action (low priority):** After Google Workspace OAuth is connected and upgrade completes, explore adding a Notion or Calendly MCP via AlphaClaw UI → Envars tab.

**Risk:** LOW. No urgency. Awareness item.

---

### F36 — 2026.6.9 Cron Improvements Directly Benefit Heather (Informational) ℹ️

Three specific cron fixes in 2026.6.9 are directly relevant to Josh's pending heartbeat deployment:

1. **Retry backoff honored:** Cron jobs now respect configured `retry.backoffMs` — heartbeat retries won't hammer the provider on transient failures
2. **Overdue jobs rescheduled on startup:** If the gateway restarts and a heartbeat cron was overdue, it now reschedules gracefully instead of firing immediately (avoids a burst of heartbeats on restart)
3. **Browser tab cleanup:** Cron runs with browser automation cleanly close tabs when done — no orphaned processes

These fixes make deploying the heartbeat cron (once Josh does it) safer and more reliable than it would have been on 2026.3.22.

**Action:** Bundle heartbeat cron deployment with the 2026.6.9 upgrade session.

**Risk:** LOW (informational).

---

## Standing Alerts (Updated Counts)

| Alert | Days | Priority |
|-------|------|----------|
| Google Workspace OAuth not connected | **Day 92** | 🔴 CRITICAL |
| Heartbeat cron not deployed — all-null state | **Day 7** | 🔴 HIGH |
| iMessage paused (auto-fix on upgrade) | **Day 57** | 🔴 HIGH |
| OpenClaw 2026.3.22 — upgrade window OPEN | **Day 92** | 🔴 HIGH |
| TOOLS.md / MEMORY.md version conflict (NEW — F33) | — | 🔴 HIGH |
| BRAVE_API_KEY not set (F30) | — | 🟠 MEDIUM-HIGH |
| Discord open to all (`allowFrom: ["*"]`) | Day 92 | 🟠 MEDIUM-HIGH |
| Same-provider fallback chain gap (F31) | — | 🟡 MEDIUM |
| Noah session scope broken (`noah--repo` 404) | **Day 11** | Fleet ops |

---

## Noah Repo: Scope Gap Persists (Day 11)

The configured scope repo `lylle-rgb/noah--repo` remains a GitHub 404. Two candidate repos found via search:
- `lylle-rgb/Noahrepo2` (last updated March 8, 2026)
- `lylle-rgb/Noah-workspace` (last updated March 7, 2026)

Neither is within the current session scope. Evening scan for Noah's Market Catalyst Agent cannot be completed. **Fleet operator action required:** correct the Noah repo name in session configuration.

---

## OpenClaw Release Tracker (as of June 22, 2026 Evening)

| Version | Status | Notes |
|---------|--------|-------|
| 2026.6.10-beta.1 | Pre-release | 109 PRs, agent reliability focus. Do NOT deploy. |
| **2026.6.9** | **✅ STABLE — TARGET** | Released June 21. 422 PRs. Upgrade now. |
| 2026.6.8 | ⛔ SKIP | Critical regressions. Never install. |
| 2026.6.6 | Superseded | Was safe target, now step-stone only |
| 2026.3.22 | **Current (Josh)** | 92 days behind. Upgrade urgent. |

---

## Summary — What Josh Needs to Do

**This week (in order):**
1. 🔴 Connect Google Workspace OAuth at https://5.78.142.81.sslip.io#general (Day 92 gap)
2. 🔴 Upgrade OpenClaw: staged path 2026.3.22 → 2026.5.27 → 2026.6.2 → 2026.6.5 → 2026.6.6 → 2026.6.9
3. 🔴 Bundle into that upgrade session: userTimezone, dreaming, compaction, heartbeat cron, fallback model fix
4. 🟠 Add BRAVE_API_KEY (free at api.search.brave.com) via AlphaClaw UI → Envars tab (can do this NOW, no upgrade needed)
5. 🟡 Fix TOOLS.md version conflict (see F33 exact changes above)
