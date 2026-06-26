# Cross-Customer Analysis — AlphaClaw Fleet

**Last updated:** 2026-06-26 (morning scan — F56/F57/F58)
**Fleet agent:** AlphaClaw Fleet Research Agent
**Customers in scope:** Josh (Heather Schwartz), Noah (Market Catalyst Agent)

---

## ⚠️ Fleet Coverage Issue: Noah Repo Scope Broken (Day 17)

This analysis is **materially incomplete** due to a fleet operations issue:
- Session scope includes `lylle-rgb/noah--repo` — this repo returns 404 (does not exist)
- GitHub search has confirmed: `lylle-rgb/Noahrepo2` (last updated 2026-03-08) and `lylle-rgb/Noah-workspace` (last updated 2026-03-07)
- Neither is in the session's authorized scope — fleet agent cannot read or write to Noah's repos
- Noah has been without fleet research coverage for **17 consecutive days** (since June 10)
- Last known Noah git sync: **March 2026 (~111 days ago)**

**Fleet admin action required:**
> Fix session scope: replace `lylle-rgb/noah--repo` (404) with `lylle-rgb/Noahrepo2` (correct repo)
> On next scan after scope fix: run full Noah workspace audit — version, skills, model config, Alpaca integration, Discord security

---

## Fleet Snapshot (June 26 Morning)

| Dimension | Josh (Heather) | Noah (Market Catalyst) |
|-----------|----------------|------------------------|
| **Use case** | Personal assistant — iMessage, email, calendar, contacts | Stock catalyst hunter — Alpaca paper trading, SEC filings, market data |
| **Bot name** | Heather Schwartz | Market Catalyst Agent |
| **Platform** | Discord | Discord |
| **OpenClaw version** | 2026.3.22 (97 days old) | Unknown — last git sync March 2026 (~111 days ago) |
| **Safe upgrade target** | **2026.6.10-stable** (Day 2 — FULLY OPEN) | Unknown — likely same, staged from wherever Noah is |
| **Primary model** | google/gemini-3-flash-preview (migrate to gemini-3.5-flash) | Unknown |
| **Fallback chain** | OpenRouter Gemini 3.5-flash → Claude 3.5-haiku | Unknown |
| **Cron/heartbeat** | Not deployed (Day 12 null) | Unknown — but critical for trading hours monitoring |
| **Google Workspace OAuth** | NOT connected (Day 97 — Day 100 in 3 days) | N/A (trading bot) |
| **iMessage** | Paused since April 27 (Day 62) | N/A |
| **Repo access** | ✅ `josh_repo` | ⛔ `noah--repo` (404) — correct repo is `Noahrepo2` |
| **Fleet coverage** | ✅ Active | ⛔ BROKEN — Day 17 |

---

## Shared Issues (Both Customers Likely Affected)

### 1. OpenClaw Version: Both Outdated (HIGH)

Josh is confirmed on 2026.3.22 (97 days old). Noah's version is unknown but git sync stopped in March — Noah is almost certainly on an older build as well.

**Current stable target: 2026.6.10** (released June 24, 2026 at 03:01 UTC — Day 2 of stable as of June 26 — FULLY OPEN, Day 1 caution lifted per F56).
**Skip 2026.6.8 and 2026.6.9** — both have critical regressions (memory store relocation, email config corruption, isolated cron failures).

Staged upgrade path for either customer:
```
<current version> → ... → 2026.6.5 → 2026.6.6 → 2026.6.10
```

**2026.6.10 benefits both customers (now Day 2 stable):**
- Auto fast mode for short conversational turns (Discord response speed — no config needed)
- Reliable model routing: Zai synthesis, GLM failover, reasoning-level selection
- Session/channel state fixes: channel switches reset stale origin fields
- Cron delivery awareness persists through restarts (critical for both — Josh heartbeat, Noah market monitoring)
- Backoff honored and overdue jobs rescheduled on startup

**2026.6.11-beta.1 preview (do not install — monitor for stable):**
- Per-DM model overrides: configure lighter model for casual DMs, primary for complex tasks
- File-driven operator workflows: `openclaw agent --message-file` for batch automation
- Richer Discord output: HTML tables, markdown preservation, progress drafts
- RAFT CLI wake bridge for remote agent activation

### 2. Gemini Flash Preview Model Risk (MEDIUM-HIGH)

Josh is confirmed on `google/gemini-3-flash-preview`. Noah's model config is unknown.

**June 25-26 update:** The Gemini preview deprecation wave hit June 25 — `gemini-3.1-flash-image-preview` and `gemini-3-pro-image-preview` shut down as announced. Josh's primary (`gemini-3-flash-preview`) is confirmed safe. However:
- Preview models have no GA support SLA
- The deprecation cadence is confirmed: shutdown waves arrive on the announced date with no reprieves (F52)
- Proactive migration to `google/gemini-3.5-flash` (GA stable) recommended for both customers

Recommended model config (Josh — can apply NOW via Browse tab, no upgrade needed):
```json
"model": {
  "primary": "google/gemini-3.5-flash",
  "fallbacks": [
    "openrouter/anthropic/claude-haiku-4-5",
    "openrouter/google/gemini-3.5-flash"
  ]
}
```

### 3. ClawHavoc Skill Audit (MEDIUM — HIGH for Noah)

1,184 malicious skills were found on ClawHub in early 2026. Both customers should run `openclaw skill list` post-upgrade.

- Josh's workspace: confirmed clean (empty skill list)
- Noah's skill list: **unknown** — Noah has external Alpaca API access + SEC data access making this the highest-risk profile in the fleet. ClawHavoc specifically targeted financial and trading skill packages.
- SkillSpector scanning is now standard on all new ClawHub installs (F45) — benefits future installs only, does not retroactively audit existing skills.

### 4. Cron/Heartbeat Deployment (HIGH for Josh, critical-unknown for Noah)

- Josh: heartbeat cron not deployed — 12+ days of null state (Day 12 as of June 26). Bundle deployment with the 2026.6.10 upgrade.
- Noah: unknown. As a trading bot, cron is almost certainly more critical — market-hours monitoring and SEC filing checks likely require precise scheduling. Without cron, Noah likely misses time-sensitive catalyst events.

### 5. Discord Security: Open to All (MEDIUM-HIGH)

Josh's config has `groupPolicy: open`, `allowFrom: ["*"]`. This is the default and Noah almost certainly has the same. Both should tighten post-upgrade.

**Particularly important for Noah:** open Discord access + live trading API access = risk of unauthorized trading commands from any Discord user. Should lock to specific user IDs before enabling any real (non-paper) trading capabilities.

---

## Customer-Specific Gaps

### Josh (Heather Schwartz) — Personal Assistant

**Specific to Josh:**
- Google Workspace OAuth not connected (Day 97, **3 days to Day 100 milestone**) — email and calendar completely inaccessible. Fix is a 5-minute OAuth click, independent of any upgrade.
- iMessage monitoring paused since April 27 (Day 62) — auto-fix via SQLite migration at 2026.6.6 upgrade step
- No BRAVE_API_KEY set (F30) — Heather cannot autonomously search the web; can set NOW via AlphaClaw Envars tab
- Dreaming not enabled (F22/24) — MEMORY.md only updates when manually written; Dreaming would consolidate daily logs nightly
- userTimezone not set (F28) — Heather operates in UTC vs Josh's LA/PDT — silent scheduling misalignment
- Discord streaming disabled — can enable `partial` streaming post-upgrade for more responsive feel
- No Discord auto-thread titles — enable post-upgrade for cleaner long conversations

**Priority for Josh:**
1. Pre-upgrade (today): connect Google Workspace OAuth at https://5.78.142.81.sslip.io#general
2. Pre-upgrade (today): migrate model to gemini-3.5-flash (Browse tab) + set BRAVE_API_KEY (Envars tab)
3. Staged upgrade to 2026.6.10 (VPS/SSH) — Day 2 stable, window fully open
4. Bundle with upgrade: userTimezone, Dreaming, compaction, heartbeat cron, fallback 2 → Haiku 4.5

### Noah (Market Catalyst Agent) — Trading Bot

**Known gaps (pre-scope-break, June 10):**
- Alpaca MCP Server v2 now has 65 tools (up from 43 in v1) — auto-updates from OpenAPI specs. Missing: order replacements, option chain exploration, market screening tools, account activity logs, API changelog tracking.
- Noah is highest-risk for ClawHavoc exposure due to external trading API access
- Last git sync March 2026 (~111 days ago) — workspace files significantly stale

**New — SEC Filing Watcher skill available:**
`sec-filing-watcher` is now on the skills marketplace. Directly enables Noah's catalyst detection pipeline:
- Auto-monitors SEC EDGAR for new filings on a configurable ticker watchlist
- Filters by form type (8-K for material events, 10-Q/10-K for earnings, S-1 for IPOs)
- Delivers Discord alerts with filing summaries
- JSON-configured watchlist; environment-driven; seeds initial state to avoid spam
- Install (post-audit): `openclaw skill install sec-filing-watcher`

**Unknown (requires scope fix to audit):**
- OpenClaw version and upgrade path
- Model config and fallback chain (likely Gemini or OpenRouter)
- Alpaca integration method (native tools vs MCP server v1 vs v2 vs direct API)
- SEC filings integration status — `sec-filing-watcher` may already be installed or absent
- Cron/heartbeat deployment and market-hours schedule
- Installed skills (ClawHavoc risk unknown)
- Discord security settings
- Whether SOUL.md / AGENTS.md / MEMORY.md exist and are current

**Specific improvement opportunity for Noah (Alpaca MCP v2):**
```
Alpaca MCP Server v2 (April 2026, updated June 16, 2026)
- 65 tools vs 43 in v1
- Built on FastMCP + OpenAPI specs — auto-aligns with Alpaca API changes
- New: order replacements, option chain exploration, market screening, account activity logs
- Install: uvx alpaca-mcp-server
- Directly enables SEC catalyst + trading workflow without manual API calls
```

---

## Fleet-Wide Recommendations (June 26 Morning)

### Immediate (today, no VPS needed)
1. **Josh:** Connect Google Workspace OAuth — https://5.78.142.81.sslip.io#general — **Day 97, 5 min task**
2. **Josh:** Migrate model config to `google/gemini-3.5-flash` + Haiku 4.5 fallback — AlphaClaw Browse tab
3. **Josh:** Set BRAVE_API_KEY — AlphaClaw Envars tab
4. **Fleet admin:** Fix Noah session scope (`noah--repo` → `Noahrepo2`) — Day 17 without coverage

### This Week (requires VPS)
5. **Josh:** Execute staged upgrade to 2026.6.10 (SSH) — Day 2 of stable window, Day 1 caution lifted
6. **Josh:** Bundle with upgrade: userTimezone, Dreaming, compaction, heartbeat cron
7. **Noah (after scope fix):** Full workspace audit — version, skills, model config, Alpaca integration, Discord security
8. **Noah (after scope fix):** Staged upgrade to 2026.6.10
9. **Noah (after scope fix):** Evaluate Alpaca MCP Server v2 + `sec-filing-watcher` skill

### Ongoing
10. **Both:** Check Google Gemini deprecation page monthly — deprecation waves hit without reprieves (confirmed June 25, F52)
11. **Both:** Run `openclaw skill list` after each upgrade — ClawHavoc risk is ongoing
12. **Both:** After upgrade, tighten Discord `allowFrom: ["*"]` to specific user IDs
13. **Both:** Monitor 2026.6.11-stable release — per-DM model overrides and file-driven workflows are high-value for both use cases

---

## Fleet Research Coverage Status

| Customer | Repo | Access | Last Fleet Scan | Coverage |
|----------|------|--------|-----------------|----------|
| Josh | josh_repo | ✅ Active | June 26 morning (F56/F57/F58) | ✅ Comprehensive |
| Noah | Noahrepo2 (needs scope fix) | ⛔ Broken | June 10, 2026 | ⛔ DARK — Day 17 |

**Next scan priority:** Fix Noah scope first. Noah is the higher-risk customer (trading bot, external APIs, unknown OpenClaw version, potential ClawHavoc exposure) and is currently invisible to fleet monitoring.

---

*Sources: [OpenClaw GitHub Releases](https://github.com/openclaw/openclaw/releases) · [OpenClaw 2026.6.10 Release Notes](https://releasebot.io/updates/openclaw) · [OpenClaw Changelog](https://www.remoteopenclaw.com/blog/openclaw-changelog) · [SEC Filing Watcher Skill](https://lobehub.com/skills/openclaw-skills-sec-filing-watcher) · [Alpaca MCP Server v2](https://alpaca.markets/blog/alpaca-launches-mcp-server-v2/) · [AlphaClaw](https://alphaclaw.md/) · [ClawStat.us](https://clawstat.us/) · [Google Gemini Deprecations](https://ai.google.dev/gemini-api/docs/deprecations)*
