# Fleet Research — Josh (Heather Schwartz) | 2026-06-19 Evening Scan

**Scan type:** Platform delta + workspace audit + regression monitoring
**Date:** 2026-06-19 (evening)
**Instance:** Josh Meyers — Heather Schwartz (personal assistant)
**Repo:** lylle-rgb/josh_repo
**Prior scan:** 2026-06-18 evening — Confirmed 2026.6.8 as stable target; updated TOOLS.md/MEMORY.md

---

## ⚠️ Priority Correction — 2026.6.8 Has Critical Regressions (Hold Upgrade)

**This overrides yesterday's scan.** The June 18 scan marked 2026.6.8 as "confirmed stable" and updated TOOLS.md/MEMORY.md to target it. Tonight's research reveals this was premature.

**ClawStat.us (automated regression tracker):** "Should you update OpenClaw v2026.6.8? Wait for next release."

High/critical regressions confirmed in v2026.6.8:
- **Discord image-tool failure** (#94266) — image sending broken in Discord
- **Memory-search provider breakage** (#94316) — local embedding broken
- **Remote Ollama streaming hang** (#94251)
- **Provider catalog discovery broken** (#93775)
- **Sub-agent tools broken** (#94158)
- **Codex native thread churn** (#93750)
- **Misleading terminal-turn fallback** (#94176)
- **Feishu discovery regression** (#93908)
- **Discord warning state after successful delivery** (#93875)
- Cron isolation regressions: isolated pre-execution watchdog aborts, hot-reload persistence races silently dropping cron jobs, intermittent model-call-started timeouts

**Impact on Heather:** At least three regressions directly hit Josh's setup — Discord image tools (#94266), memory/embedding search (#94316), and misleading fallbacks (#94176). The cron regressions would affect any scheduled tasks.

**Current npm channels:**
- `latest` (stable): **2026.6.6** (June 12 production release)
- `beta`: 2026.6.7-beta.1
- 2026.6.8 was released but has NOT been promoted to the stable `latest` tag

**2026.6.9 status:** Alpha lane (2026.6.9-alpha.6 as of June 18). Fixes target: memory instructions explicit, compaction ownership, update repair resumable, channels fail-closed, provider schema cleanup. Recommended to monitor this lane closely if running long sessions, scheduled jobs, or Discord delivery.

**Correction applied tonight:** TOOLS.md and MEMORY.md updated — upgrade target changed from 2026.6.8 to "hold at 2026.6.6; wait for 2026.6.9-stable."

**Risk level:** HIGH — yesterday's upgrade recommendation was incorrect. If Josh had acted on the June 18 scan's advice to upgrade directly to 2026.6.8, Heather's Discord tools and memory search would be broken on arrival.

---

## ⚡ Actions Applied Tonight (GitHub-Only)

### ✅ APPLIED — TOOLS.md Upgrade Path Corrected

**File:** `workspace/TOOLS.md`
**Change:** Reverted upgrade target from 2026.6.8 to "Hold at 2026.6.6; wait for 2026.6.9-stable." Added regression warning block. Updated staged upgrade path to stop at 2026.6.6 with explicit hold note.

### ✅ APPLIED — MEMORY.md Platform Target Corrected

**File:** `workspace/MEMORY.md`
**Change:** Updated platform target from 2026.6.8 to "Hold — wait for 2026.6.9-stable." Added regression discovery note under Known Configuration Issues. Updated last-updated date to June 19.

---

## Finding 1 — Heartbeat State: All Null — Day 3 (Persistent)

**Risk: MEDIUM-HIGH**

`workspace/memory/heartbeat-state.json` has had all null timestamps across three consecutive scans:
- June 17: created with nulls
- June 18: still null (Day 2)
- June 19: still null (Day 3)

This is no longer an anomaly — it is a confirmed pattern. Heather is either:
- Not performing heartbeat checks at all
- Performing them but not writing state (violating the "mental notes don't survive" rule)
- Performing iMessage status checks but not updating the JSON despite that check requiring no Google Workspace

**Compounding picture:** heartbeat-state.json all null + iMessage paused (53 days) + Google Workspace disconnected (89 days) = Heather has had **zero confirmed proactive outreach** since deployment.

**No GitHub fix available.** Requires Josh to test in Discord and/or add explicit mandatory-write language to SOUL.md (see soul-improvements file).

---

## Finding 2 — Web Research: No New OpenClaw Release Since 2026.6.8 (INFO)

No v2026.6.9-stable or newer release as of June 19 evening. The alpha lane (2026.6.9-alpha.6) is active and targeting the regressions above. Based on OpenClaw's release cadence, a stable fix could arrive within days to a week.

**Watch for:** OpenClaw 2026.6.9-stable. When it ships, the staged upgrade path should be re-evaluated — if 2026.6.9 is clean, Josh can run `openclaw update` targeting 2026.6.9 directly from 2026.6.6.

---

## Finding 3 — AlphaClaw 0.9.18 Remote MCP: Confirmed Value for Josh (INFO)

Per the prior scan, AlphaClaw 0.9.18 includes a Managed Remote MCP Server with env var configuration via the UI. Confirmed relevant integrations Josh could add without VPS SSH once Google Workspace is connected:

| Integration | Use case | Effort |
|-------------|---------|--------|
| Google Workspace MCP | Gmail + Calendar (already needs OAuth first) | Low (OAuth first) |
| Notion MCP | Bliss brand docs, task management | Low |
| Calendly MCP | Josh's scheduling via Discord | Low |
| GitHub MCP | Repo access for Bliss/Oben HiFi projects | Low |

All configurable via AlphaClaw UI → Envars tab once on 0.9.18+.

---

## Finding 4 — Noah Fleet: Persistent Blocker (Day 89+)

Session scope lists `lylle-rgb/noah--repo` which returns 404 and does not exist. Actual Noah repos found:
- `lylle-rgb/Noah-workspace` (created March 7, 2026) — likely the active repo
- `lylle-rgb/Noahrepo2` (created March 7, 2026)

Both are private and out of session scope. Noah (Market Catalyst Agent) analysis has been blocked for every scan in this series.

**Action for fleet operator:** Update session scope to include `lylle-rgb/Noah-workspace` before next scan. The current scope alias `lylle-rgb/noah--repo` is incorrect.

---

## Platform Status (June 19 Evening)

| Item | Current | Target | Status |
|------|---------|--------|--------|
| OpenClaw | 2026.3.22 | 2026.6.9-stable | ⛔ Hold — do NOT go to 2026.6.8 |
| npm latest | — | 2026.6.6 | 2026.6.8 not on stable channel |
| AlphaClaw | Unknown | 0.9.18 | Check Watchdog tab |
| Primary model | google/gemini-3-flash-preview | — | Active |
| Fallback 1 | openrouter/google/gemini-3.5-flash | — | Current |
| Fallback 2 | openrouter/anthropic/claude-3.5-haiku | claude-haiku-4-5 | Pending (after OpenClaw upgrade) |
| SOUL.md | Personalized June 17 | — | Good |
| AGENTS.md | Emoji override active | — | Good |
| TOOLS.md | Updated tonight | — | Regression warning added |
| MEMORY.md | Updated tonight | — | Corrected target |
| HEARTBEAT.md | Populated June 16 | — | Good |
| heartbeat-state.json | All null | — | ⚠️ Day 3 — no checks logged |
| Google Workspace | Not connected | — | ⛔ CRITICAL — day 89 |
| iMessage monitoring | Paused | — | ⛔ Day 53+ |
| Dreaming | Not enabled | — | ⛔ Day 89 |
| compaction/memoryFlush | Not configured | — | ⛔ Day 89 |

---

## Priority Action Queue (June 19 Evening)

| Priority | Action | Method | Effort |
|---------|--------|--------|-------|
| 0 — URGENT | Do NOT upgrade to OpenClaw 2026.6.8 | Passive — just hold | — |
| 1 — CRITICAL | Connect Google Workspace OAuth | AlphaClaw UI: https://5.78.142.81.sslip.io#general | ~30 min |
| 2 — HIGH | Verify heartbeat checks are running (ask Heather in Discord) | Discord DM to Heather | ~5 min |
| 3 — HIGH | When 2026.6.9-stable ships: upgrade via staged path | VPS: `openclaw update` (stop at 2026.6.6 for now) | ~30 min |
| 4 — HIGH | Add compaction + Dreaming to openclaw.json | VPS edit (configs in June 18 findings) | ~5 min |
| 5 — MEDIUM | Tighten Discord security (open → allowlist) | VPS: openclaw.json | ~5 min |
| 6 — FUTURE | Upgrade fallback 2 to claude-haiku-4-5 | openclaw.json after 2026.6.9-stable upgrade | ~1 min |
| 7 — FUTURE | Configure remote MCP integrations (Notion, Calendly) | AlphaClaw 0.9.18 UI | Variable |
| 8 — FLEET OPS | Fix Noah session scope (lylle-rgb/noah--repo → Noah-workspace) | Fleet config | ~5 min |
