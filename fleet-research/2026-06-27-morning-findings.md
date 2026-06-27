# Fleet Research: Josh (Heather) — Morning Scan
**Date:** 2026-06-27 | **Scan type:** Morning (running after June 27 evening scan) | **Agent:** AlphaClaw Fleet Research

## Scan Note
This morning scan ran after the June 27 evening scan (2026-06-27-evening-findings.md), which covered all active open issues (F1–F10) comprehensively. This scan adds new intelligence from deeper web research: undocumented 2026.6.10 CLI features, AlphaClaw version confirmation, Noah trading ecosystem intelligence, and Day 98 Google Workspace escalation framing.

---

## New Findings

### F59 — MEDIUM: 2026.6.10 Has Additional CLI Features Not Yet in findings.md
**Risk:** MEDIUM — useful capabilities not yet in upgrade planning docs

2026.6.10 includes several CLI-level features not captured in the current findings.md that add direct value for Josh/Heather:

- **Explicit compaction:** New CLI command to manually trigger context compaction — Heather can proactively manage long session memory before it degrades quality. Previously only automatic.
- **Dry-run message previews:** Preview external messages (emails, Discord sends) before executing — directly serves Josh's "ask before acting externally" rule in SOUL.md. Safer external actions without extra manual confirmation prompts.
- **Session renaming:** Cleaner history management in AlphaClaw UI browse view
- **Duration display:** Session and turn duration visible in CLI — helps diagnose slow turns, identify provider routing issues
- **SSH tunnel preflight checks:** Error detection before tunnel failures — reduces silent SSH connectivity issues that cause confusing downstream errors

**Relevance for Josh:** Dry-run previews and explicit compaction are the highest-value additions. Dry-run lets Heather show Josh a draft of any external message before sending — zero code needed. Explicit compaction gives Heather control over long-session context degradation.

**Action:** No config required. All CLI features become available immediately after reaching 2026.6.10 in the staged upgrade path.

---

### F60 — INFO: AlphaClaw 0.9.18 Confirmed as Latest — No June Update
**Risk:** LOW — confirmation, no action needed

Confirmed via GitHub releases page: AlphaClaw **0.9.18** (June 1, 2026) is the current stable version. No 0.9.19 or 0.9.20 released in June 2026.

**0.9.18 capabilities (for reference — already in Finding 23):**
- **OpenAI-compatible API proxy**: `/v1/chat/completions` and `/v1/embeddings` — server-side app integration; disabled by default; enable via General → Features in AlphaClaw UI
- **Remote MCP server support**: Auto-register remote MCP servers via `REMOTE_MCP_URL` + `REMOTE_MCP_API_TOKEN` in AlphaClaw Envars tab — enables Google Workspace MCP, Notion, Calendly access without VPS SSH
- **Security**: Timing-safe gateway token comparison, rate-limit on failed auth, header stripping on proxied responses

**New angle — Remote MCP as Google Workspace alternative (not previously documented):**
AlphaClaw 0.9.18's Remote MCP env var support is an alternative path to the Day 98 Google Workspace blocker. If Josh sets:
```
REMOTE_MCP_URL=<Google Workspace MCP endpoint>
REMOTE_MCP_API_TOKEN=<token>
```
in the AlphaClaw Envars tab, Heather gets email/calendar/contacts access WITHOUT going through the AlphaClaw OAuth flow. This is worth exploring if the OAuth flow has friction or if Josh prefers MCP-based auth.

**Action:** Primary path remains the OAuth flow (5 minutes). Remote MCP is an alternative if OAuth fails or is unavailable. Document in MEMORY.md for Heather's reference.

---

### F61 — CRITICAL: Google Workspace Day 98 — Day 100 is June 29 (2 Days)
**Risk:** CRITICAL — Day 100 escalation threshold imminent

Day 98 of Google Workspace OAuth disconnect. Day 100 arrives **June 29, 2026** — 2 days from now.

Per MEMORY.md lesson added June 27 evening: "At Day 100 milestones and every 10 days after, surface persistently unresolved gaps to Josh proactively with their concrete fix steps."

**Escalation framing for Heather (use this on next main session):**
> "We're 2 days from Day 100 without email or calendar. That's a 100-day blind spot on your inbox and schedule. The fix is 5 minutes in the browser: AlphaClaw General tab → Google Workspace OAuth. I can do everything else — but until then, I can't see your email or calendar."

**Fix paths:**
1. **Primary (5 min):** AlphaClaw UI → General tab → Google Workspace OAuth → https://5.78.142.81.sslip.io#general
2. **Alternative (no OAuth):** AlphaClaw Envars tab → `REMOTE_MCP_URL` + `REMOTE_MCP_API_TOKEN` → Google Workspace via Remote MCP (AlphaClaw 0.9.18 feature — see F60)

**Action:** Heather must raise this proactively on next main session. Use day count framing. No new fleet action — escalation responsibility passes to Heather.

---

### F62 — HIGH (Noah): Finance Skills Ecosystem + Trading Architecture Intelligence
**Risk:** HIGH when Noah scope is restored — intelligence gathered proactively

Noah scope still broken (Day 18). Collecting intelligence for Market Catalyst Agent for when scope is fixed:

**ClawHub finance ecosystem (June 2026):**
- 13,700+ total skills on ClawHub marketplace
- 311+ finance/investing skills specifically
- Key skills confirmed operational:
  - `sec-filing-watcher` — EDGAR monitoring for 8-K/10-Q/10-K/S-1; JSON-configured ticker watchlist; Discord alerts with filing summaries
  - `alpaca-trading` on clawbot.ai — confirmed operational; full Alpaca API (paper + live, all order types, portfolio management, market data/quotes/bars)

**Agent Mesh architecture (emerging pattern, March 2026+):**
The market is moving from single omnipotent trading agents toward pipeline-style Agent Meshes:
```
Research Agent (SEC/news signals)
  → Risk Agent (position sizing, drawdown limits)
    → Execution Agent (Alpaca order routing)
```
Noah's Market Catalyst Agent is currently designed as a single agent. Splitting into a 3-stage pipeline would improve reliability and allow separate model selection per stage (fast model for signal detection, reasoning model for risk sizing).

**Alpaca MCP Server v2 (when Noah scope is restored):**
- 65 tools vs 43 in v1
- Auto-updates from OpenAPI specs — stays compatible without client-side changes
- New in v2: order replacements, option chain exploration, market screening, account activity logs, API changelog tracking
- Install: `uvx alpaca-mcp-server`
- ClawHub Alpaca skill on clawbot.ai confirmed operational as alternative integration path

**Action:** Hold — requires Noah scope fix first. Fleet admin action: replace `noah--repo` (404) with `Noahrepo2`. On first scan after fix:
1. `openclaw skill list` — check for ClawHavoc-flagged finance skills
2. Verify Alpaca integration version (v1 MCP / v2 MCP / direct API / clawbot.ai skill)
3. If on v1 or direct: plan upgrade to Alpaca MCP v2
4. Evaluate sec-filing-watcher install and Agent Mesh restructuring

---

## Status Snapshot (June 27 Morning)

| Issue | Day Count | Status |
|-------|-----------|--------|
| Google Workspace OAuth | **Day 98** | ⚠️ CRITICAL — Day 100 in 2 days (June 29) |
| OpenClaw upgrade (→2026.6.10) | Day 4 of stable | ⏳ Upgrade window open — needs VPS time |
| Heartbeat cron undeployed | Day 14+ | ⏳ Bundle with upgrade (PR #96233 fix in 2026.6.10) |
| iMessage monitoring paused | Day 63 | ⏳ Auto-fix on upgrade through 2026.6.6 |
| Noah scope broken | Day 18 | ⏳ Fleet admin fix needed |
| Model migration (→gemini-3.5-flash) | N/A | ⏳ Can do now via Browse tab |
| BRAVE_API_KEY | N/A | ⏳ Can do now via Envars tab |

## What Changed Since Evening Scan
- **F59:** 2026.6.10 CLI features documented (explicit compaction, dry-run previews, session rename, SSH preflight, duration display)
- **F60:** AlphaClaw 0.9.18 confirmed current; Remote MCP alternative path for Google Workspace documented
- **F61:** Day 98 escalation framing written for Heather — ready to use on next main session
- **F62:** Noah trading ecosystem intelligence gathered (Agent Mesh pattern, Alpaca v2, finance skills count)
- Day counts incremented: Google Workspace 97→98, iMessage 62→63, Noah scope 17→18 (now consistent with evening scan)

---

_Generated by AlphaClaw Fleet Research Agent — 2026-06-27 Morning_
_Scan scope: AlphaClaw GitHub releases (chrysb/alphaclaw), OpenClaw 2026.6.10/6.11 release notes, Alpaca trading ecosystem, ClawHub finance skills, community trading patterns_
