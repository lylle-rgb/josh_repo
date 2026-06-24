# Cross-Customer Analysis — AlphaClaw Fleet

**Last updated:** 2026-06-24 (morning scan)
**Fleet agent:** AlphaClaw Fleet Research Agent
**Customers in scope:** Josh (Heather Schwartz), Noah (Market Catalyst Agent)

---

## ⚠️ Fleet Coverage Issue: Noah Repo Scope Broken (Day 15)

This analysis is **materially incomplete** due to a fleet operations issue:
- Session scope includes `lylle-rgb/noah--repo` — this repo returns 404 (does not exist)
- GitHub search found: `lylle-rgb/Noahrepo2` (last updated 2026-03-08) and `lylle-rgb/Noah-workspace` (last updated 2026-03-07)
- Neither is in the session's authorized scope — fleet agent cannot read or write to Noah's repos
- Noah has been without fleet research coverage for **15 consecutive days** (since June 11)
- Last known Noah git sync: **March 2026 (~108 days ago)**

**Fleet admin action required:**
> Fix session scope: replace `lylle-rgb/noah--repo` (404) with `lylle-rgb/Noahrepo2` (correct repo)
> On next scan after scope fix: run full Noah workspace audit

---

## Fleet Snapshot (June 24 Morning)

| Dimension | Josh (Heather) | Noah (Market Catalyst) |
|-----------|----------------|------------------------|
| **Use case** | Personal assistant — iMessage, email, calendar, contacts | Stock catalyst hunter — Alpaca paper trading, SEC filings, market data |
| **Bot name** | Heather Schwartz | Market Catalyst Agent |
| **Platform** | Discord | Discord |
| **OpenClaw version** | 2026.3.22 (95 days old) | Unknown — last git sync March 2026 |
| **Safe upgrade target** | 2026.6.10-stable (June 24) | Unknown |
| **Primary model** | google/gemini-3-flash-preview | Unknown |
| **Fallback chain** | OpenRouter Gemini 3.5-flash → Claude 3.5-haiku | Unknown |
| **Cron/heartbeat** | Not deployed (10+ days null) | Unknown |
| **Google Workspace OAuth** | NOT connected (Day 95) | N/A (trading bot) |
| **iMessage** | Paused since April 27 | N/A |
| **Repo access** | ✅ `josh_repo` | ⛔ `noah--repo` (404) |
| **Fleet coverage** | ✅ Active | ⛔ BROKEN — Day 15 |

---

## Shared Issues (Both Customers Likely Affected)

### 1. OpenClaw Version: Both Outdated (HIGH)

Josh is confirmed on 2026.3.22 (95 days old). Noah's version is unknown but git sync stopped in March—Noah is almost certainly on an older build as well.

**Critical new finding this morning:** The upgrade target changed. Both customers should target **2026.6.10** (stable as of June 24 at 03:01 UTC). Skip both 2026.6.8 and 2026.6.9 — both have critical regressions.

Stageed upgrade path for either customer:
```
<current version> → ... → 2026.6.5 → 2026.6.6 → 2026.6.10
```

**2026.6.10 benefits both customers:**
- Auto fast mode for short conversational turns (Discord response speed)
- Better provider routing (Zhipu GLM failover, reasoning-level selection)
- Session/channel state fixes (channel switches, cron delivery)
- Cron reliability improvements (backoff honored, overdue jobs rescheduled on startup)

### 2. Gemini Flash Preview Model Risk (MEDIUM-HIGH)

Josh is confirmed on `google/gemini-3-flash-preview`. Noah's model config is unknown.

**Morning scan confirmed:** `gemini-3-flash-preview` has NO announced shutdown date as of June 24.
The sister image preview models (`gemini-3.1-flash-image-preview`, `gemini-3-pro-image-preview`)
were shut down June 25 — but the core flash model Heather uses is confirmed safe.

However, for both customers: proactive migration to `google/gemini-3.5-flash` (GA stable) is
recommended to avoid reliance on preview models with no support SLA.

### 3. ClawHavoc Skill Audit (MEDIUM)

1,184 malicious skills were found on ClawHub in early 2026. Both customers should run
`openclaw skill list` post-upgrade to confirm no malicious skills are present. Josh's workspace
is confirmed empty (safe). Noah's skill list is unknown — Noah has external API access
(Alpaca, SEC, market data) making this the highest-risk profile in the fleet.

### 4. Cron/Heartbeat Deployment (HIGH for Josh, unknown for Noah)

Josh: heartbeat cron not deployed — 10+ days of null state.
Noah: unknown, but as a trading bot, cron is likely more critical (market hours monitoring, SEC filing checks).

### 5. Discord Security: Open to All (MEDIUM-HIGH)

Josh's config has `groupPolicy: open`, `allowFrom: ["*"]`. This is likely the default and Noah
probably has the same. Both should tighten after upgrade. Particularly important for Noah:
open Discord access + trading capabilities = risk of unauthorized trading commands.

---

## Customer-Specific Gaps

### Josh (Heather Schwartz) — Personal Assistant

**Specific to Josh:**
- Google Workspace OAuth not connected (Day 95) — email and calendar completely inaccessible
- iMessage monitoring paused since April 27 (Day 60) — auto-fix via SQLite migration in upgrade
- No BRAVE_API_KEY set — Heather cannot autonomously search the web
- Dreaming not enabled — MEMORY.md only updates when fleet agent or Heather manually writes
- userTimezone not set — silent timezone misalignment (VPS UTC vs Josh LA/PDT)

**Priority for Josh:**
1. Google Workspace OAuth — nothing else unblocks email/calendar
2. Upgrade to 2026.6.10 (staged, bundled with config changes)
3. Model migration to gemini-3.5-flash (can do now via Browse tab)
4. BRAVE_API_KEY (can do now via Envars tab)

### Noah (Market Catalyst Agent) — Trading Bot

**Known gaps (pre-scope-break):**
- Alpaca MCP Server v2 now has 65 tools (up from 43) — auto-updates from OpenAPI specs. If Noah is
  using an older Alpaca integration, he is missing: order replacements, option chain exploration,
  market screening tools, account activity logs, and API changelog tracking.
- Noah is highest-risk for ClawHavoc exposure due to external trading API access
- Last git sync March 2026 (~108 days ago) — workspace files may be significantly stale

**Unknown (requires scope fix to audit):**
- OpenClaw version
- Model config and fallback chain
- Alpaca integration method (native tools vs MCP server vs direct API)
- SEC filings integration status
- Cron/heartbeat deployment
- Installed skills
- Discord security settings
- Whether any SOUL.md / AGENTS.md / MEMORY.md files exist

**Specific improvement opportunity for Noah (Alpaca MCP v2):**
```
Alpaca MCP Server v2 launched (April 2026, updated June 16, 2026)
- 65 tools vs 43 in v1
- Built on FastMCP + OpenAPI specs — auto-aligns with Alpaca API changes
- New: order replacements, option chain exploration, market screening, account activity logs
- Install: uvx alpaca-mcp-server (Alpaca paper trading + market data in one MCP)
- Directly enables Noah's SEC catalyst + trading workflow without manual API calls
```

---

## Fleet-Wide Recommendations (Morning Scan June 24)

### Immediate (today, no VPS needed)
1. **Josh:** Migrate model config to gemini-3.5-flash via AlphaClaw Browse tab
2. **Josh:** Set BRAVE_API_KEY in AlphaClaw Envars tab
3. **Fleet admin:** Fix Noah session scope (`noah--repo` → `Noahrepo2`)

### This Week (requires VPS)
4. **Josh:** Bundle upgrade to 2026.6.10 with config changes (userTimezone, compaction, dreaming, heartbeat cron)
5. **Josh:** Connect Google Workspace OAuth
6. **Noah (after scope fix):** Full workspace audit; determine OpenClaw version; run ClawHavoc skill check
7. **Noah (after scope fix):** Upgrade to 2026.6.10 (staged path from wherever Noah currently is)
8. **Noah (after scope fix):** Evaluate Alpaca MCP Server v2 integration

### Ongoing
9. **Both:** Check Google Gemini deprecation page monthly for model sunset announcements
10. **Both:** Run `openclaw skill list` after each upgrade — ClawHavoc risk is ongoing
11. **Both:** After upgrade, tighten Discord `allowFrom: ["*"]` to specific user IDs

---

## Fleet Research Coverage Status

| Customer | Repo | Access | Last Fleet Scan | Coverage |
|----------|------|--------|-----------------|----------|
| Josh | josh_repo | ✅ Active | June 24 morning | ✅ Comprehensive |
| Noah | Noahrepo2 (needs scope fix) | ⛔ Broken | June 11, 2026 | ⛔ DARK — Day 15 |

**Next scan priority:** Fix Noah scope first. Noah is the higher-risk customer (trading bot, external APIs,
unknown OpenClaw version, potential ClawHavoc exposure) and is currently invisible to fleet monitoring.

---

*Sources: [OpenClaw Releases](https://github.com/openclaw/openclaw/releases) · [ClawStat.us](https://clawstat.us/) · [Google Gemini Deprecations](https://ai.google.dev/gemini-api/docs/deprecations) · [Alpaca MCP Server v2](https://alpaca.markets/blog/alpaca-launches-mcp-server-v2/) · [AlphaClaw GitHub](https://github.com/chrysb/alphaclaw)*
