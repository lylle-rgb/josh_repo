# Fleet Research — Josh (Heather Schwartz) | 2026-06-20 Evening Scan

**Scan type:** Platform delta + workspace audit + behavioral pattern review
**Date:** 2026-06-20 (evening)
**Instance:** Josh Meyers — Heather Schwartz (personal assistant)
**Repo:** lylle-rgb/josh_repo
**Prior scan:** 2026-06-19 evening — corrected 2026.6.8 regression warning; updated TOOLS.md + MEMORY.md

---

## TL;DR — No New Releases, No Progress on Open Items

- 2026.6.9-stable has **not shipped** as of June 20 evening. Hold at 2026.6.6 remains correct.
- heartbeat-state.json still all null — **Day 4** with no resolution.
- Google Workspace OAuth: **Day 90** disconnected.
- iMessage: **Day 54** paused.
- Noah session scope still broken (noah--repo 404). No Noah analysis possible.
- New: 2026.6.9-beta.1 (June 19) is in active development — monitoring.

---

## Finding 1 — 2026.6.9-Stable Not Yet Shipped (HOLD Continues)

**Risk: INFO — no action needed beyond holding**

As of June 20 evening, the upgrade situation is unchanged from last night:

| Channel | Version | Date | Notes |
|---------|---------|------|-------|
| npm `latest` (stable) | **2026.6.6** | June 12 | Current safe target |
| 2026.6.8 | — | June 16 | ⛔ NOT on npm latest — critical regressions |
| 2026.6.9-beta.1 | Pre-release | June 19 | Active development |
| 2026.6.9-stable | **Not shipped** | — | Watching |

**Hold recommendation unchanged:** Do NOT upgrade beyond 2026.6.6. 2026.6.8 has confirmed regressions in Discord image tools (#94266), memory search (#94316), and cron isolation. 2026.6.9-beta.1 is too new.

**2026.6.9-beta.1 improvements (when stable):**
- Telegram HTML support (tables, expandable blockquotes, preserved line breaks)
- Session history repair in agent recovery
- Stronger Codex integration with automatic plugin approvals
- Standalone provider plugins as npm releases (cleaner plugin management)
- Enhanced iOS Watch controls

Based on OpenClaw's recent cadence (betas typically take 3–7 days to stabilize), 2026.6.9-stable could arrive **late this week or early next week**. Monitor nightly.

**Staged upgrade path (unchanged):** 2026.3.22 → 2026.5.27 → 2026.6.2 → 2026.6.5 → 2026.6.6 → **[STOP — wait for 2026.6.9-stable]**

---

## Finding 2 — Heartbeat State: All Null — Day 4 (ESCALATING)

**Risk: HIGH — pattern now confirmed, not anomalous**

heartbeat-state.json remains entirely null for the fourth consecutive day:

```json
{
  "lastChecks": {
    "email": null,
    "calendar": null,
    "imessage": null,
    "memory_maintenance": null,
    "contacts": null
  }
}
```

Escalation from Day 3: at Day 4, this is no longer a startup hiccup. The pattern indicates one of:
- **A)** Heather's heartbeat cron is not configured on the VPS (most likely — fleet agent created the JSON file via GitHub but never deployed a cron job to the live server)
- **B)** Heather is running heartbeats but not writing the JSON (violates SOUL.md + AGENTS.md write discipline)
- **C)** Heather is not running heartbeats at all

**Critical observation:** `memory_maintenance` has no dependencies on Google or iMessage. Even with both offline, it should fire. Its null state strongly suggests option A — **the heartbeat cron was never deployed to the VPS**.

**What needs to happen (VPS):**
On upgrade to 2026.6.6+, add to openclaw.json cron section:
```json
{
  "schedule": "0 9 * * *",
  "task": "heartbeat_memory_maintenance",
  "description": "Daily MEMORY.md maintenance — no Google or iMessage required"
}
```
Also ask Heather directly in Discord: "Are you running heartbeat checks? When did you last check iMessage status?" — this will reveal whether it's a cron issue or a behavioral one.

**Consequence of 4 days null:** Josh has had **zero confirmed proactive monitoring** since deployment. Heather has not self-reported iMessage outage, has not confirmed it's still paused, has not run memory maintenance. The fleet has been flying blind on Heather's operational status.

---

## Finding 3 — Google Workspace Disconnected: Day 90 Milestone

**Risk: CRITICAL — top priority for Josh action**

Google Workspace OAuth has been unconnected for **90 days** as of June 20 (first flagged in fleet research around March 21, 2026). This blocks:
- All email monitoring (Gmail)
- All calendar monitoring and reminders
- All contacts access
- Three of the five heartbeat service checks

The 90-day milestone is significant: this is now a chronic gap, not a fresh setup issue. Every fleet scan since May has flagged this as the #1 priority for Josh to action directly.

**Action required from Josh:**
1. Open AlphaClaw UI: https://5.78.142.81.sslip.io#general
2. Under Google Workspace, connect OAuth (Google Cloud Console → client credentials → authorize Gmail + Calendar + Contacts)
3. Full steps in workspace/memory/onboarding-google.md

**Alternative path (no GCP needed):** AlphaClaw 0.9.18+ Remote MCP. Set `REMOTE_MCP_URL` + `REMOTE_MCP_API_TOKEN` in the Envars tab to a managed Google Workspace MCP server. Avoids the GCP OAuth setup entirely.

---

## Finding 4 — openclaw.json: Three Confirmed Missing Configs (Stale Day 90)

**Risk: HIGH (for post-upgrade config readiness)**

The live openclaw.json (last modified 2026-03-24) is missing three configurations that have been recommended for 88+ days:

### Missing 1: compaction/memoryFlush
Without this, OpenClaw does not write memory before context compaction events. Heather can lose session context silently on long conversations.

```json
"compaction": {
  "reserveTokensFloor": 40000,
  "memoryFlush": {
    "enabled": true,
    "softThresholdTokens": 4000
  }
},
"contextPruning": {
  "mode": "cache-ttl",
  "ttl": "6h"
}
```

### Missing 2: Dreaming
Without Dreaming, MEMORY.md only gets updated when the fleet agent pushes an update or Heather does it manually. Automated nightly consolidation is off.

```json
"dreaming": {
  "enabled": true,
  "schedule": "0 3 * * *",
  "maxPromotion": 10,
  "minScore": 0.8,
  "minRecallCount": 3,
  "minUniqueQueries": 3
}
```

### Missing 3: Discord Security
Current config allows anyone in Josh's Discord server to DM Heather and get context-rich responses.

```json
"groupPolicy": "allowlist",
"dmPolicy": "allowlist",
"allowFrom": ["JOSH_DISCORD_USER_ID"]
```

**All three require VPS access.** Bundle them into one edit session when Josh upgrades to 2026.6.6.

---

## Finding 5 — AlphaClaw 0.9.18 Remote MCP: Still Unused (Day 19)

**Risk: LOW (missed opportunity, not a failure)**

AlphaClaw 0.9.18 (released June 1) includes Remote MCP server support. 19 days in, it's unused. This is the alternative path to Google Workspace if GCP OAuth remains blocked — point `REMOTE_MCP_URL` at a hosted Google Workspace MCP server in the Envars tab.

Also unused: per-agent thinking level control (AlphaClaw UI → agent model card → `thinkingDefault`). This could meaningfully improve Heather's response quality for complex scheduling tasks vs. casual messages — no openclaw.json edit needed.

---

## Finding 6 — Noah Fleet: Scope Blocker Persists (Day 90+)

**Risk: FLEET OPS — no Noah analysis possible**

Session scope lists `lylle-rgb/noah--repo` which returns 404. The actual repos:
- `lylle-rgb/Noah-workspace` (March 7, 2026) — likely active
- `lylle-rgb/Noahrepo2` (March 7, 2026) — backup/alt

Both are private and not in session scope. Noah (Market Catalyst Agent — Alpaca paper trading, SEC filings, market data) has received **zero fleet analysis** in this scan series.

**Action for fleet operator:** Update session scope to include `lylle-rgb/Noah-workspace` before next scan. The scope alias `lylle-rgb/noah--repo` is incorrect.

---

## Platform Status Table (June 20 Evening)

| Item | Current | Target | Status |
|------|---------|--------|--------|
| OpenClaw | 2026.3.22 | 2026.6.9-stable | ⏸ Hold at 2026.6.6 |
| 2026.6.9-stable | — | Not yet shipped | Watching |
| AlphaClaw | Unknown | 0.9.18 | Check Watchdog tab |
| Primary model | google/gemini-3-flash-preview | — | ⚠️ Preview — monitor deprecation |
| Fallback 1 | openrouter/google/gemini-3.5-flash | — | Active |
| Fallback 2 | openrouter/anthropic/claude-3.5-haiku | claude-haiku-4-5 | Pending (after upgrade) |
| SOUL.md | Personalized June 17 | — | Good |
| MEMORY.md | Updated June 19 | — | Good |
| TOOLS.md | Updated June 19 | — | Good |
| HEARTBEAT.md | Populated June 16 | — | Good |
| heartbeat-state.json | All null | — | ⛔ Day 4 — cron likely not deployed |
| Google Workspace | Not connected | — | ⛔ Day 90 — top priority |
| iMessage | Paused | — | ⛔ Day 54 |
| Dreaming | Not enabled | — | ⛔ Day 90 |
| compaction/memoryFlush | Not configured | — | ⛔ Day 90 |
| Discord security | Open (allowFrom: *) | allowlist | ⚠️ Risk |
| Remote MCP | Not configured | — | Opportunity |
| Noah analysis | Blocked (wrong scope) | — | ⛔ Day 90+ |

---

## Priority Action Queue (June 20 Evening)

| Priority | Action | Method | Effort |
|----------|--------|--------|--------|
| 1 — CRITICAL | Connect Google Workspace OAuth | AlphaClaw UI: https://5.78.142.81.sslip.io#general | ~30 min |
| 2 — HIGH | Ask Heather in Discord: are heartbeat checks running? | Discord DM | ~2 min |
| 3 — HIGH | When 2026.6.9-stable ships: run `openclaw update` (staged path to 2026.6.6 first) | VPS: `openclaw update` | ~30 min |
| 4 — HIGH | Add compaction + Dreaming + Discord allowlist to openclaw.json (VPS, bundle with upgrade) | VPS edit | ~10 min |
| 5 — FLEET OPS | Fix Noah session scope (noah--repo → Noah-workspace) | Fleet config | ~5 min |
| 6 — FUTURE | Upgrade fallback 2 to claude-haiku-4-5 | openclaw.json (after upgrade) | ~1 min |
| 7 — FUTURE | Configure Remote MCP for Google Workspace (alternative OAuth path) | AlphaClaw UI Envars tab | Variable |

---

## Changes Applied Tonight (GitHub-Only)

- `fleet-research/2026-06-20-evening-findings.md` — this file
- `fleet-research/2026-06-20-evening-soul-improvements.md` — behavioral recommendations
- `fleet-research/findings.md` — master summary updated with correct upgrade status + Day 4 null
- `workspace/MEMORY.md` — updated dates and Day 4 heartbeat escalation
