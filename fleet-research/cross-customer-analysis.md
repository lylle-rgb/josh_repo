# Cross-Customer Analysis — AlphaClaw Fleet

**Last updated:** 2026-06-27 (morning scan — F59/F60/F61/F62; day counts updated)
**Fleet agent:** AlphaClaw Fleet Research Agent
**Customers in scope:** Josh (Heather Schwartz), Noah (Market Catalyst Agent)

---

## ⚠️ Fleet Coverage Issue: Noah Repo Scope Broken (Day 18)

This analysis is **materially incomplete** due to a fleet operations issue:
- Session scope includes `lylle-rgb/noah--repo` — this repo returns 404 (does not exist)
- GitHub search has confirmed: `lylle-rgb/Noahrepo2` (last updated 2026-03-08) and `lylle-rgb/Noah-workspace` (last updated 2026-03-07)
- Neither is in the session's authorized scope — fleet agent cannot read or write to Noah's repos
- Noah has been without fleet research coverage for **18 consecutive days** (since June 10)
- Last known Noah git sync: **March 2026 (~112+ days ago)**

**Fleet admin action required:**
> Fix session scope: replace `lylle-rgb/noah--repo` (404) with `lylle-rgb/Noahrepo2` (correct repo)
> On next scan after scope fix: run full Noah workspace audit — version, skills, model config, Alpaca integration, Discord security

---

## Fleet Snapshot (June 27 Morning)

| Dimension | Josh (Heather) | Noah (Market Catalyst) |
|-----------|----------------|------------------------|
| **Use case** | Personal assistant — iMessage, email, calendar, contacts | Stock catalyst hunter — Alpaca paper trading, SEC filings, market data |
| **Bot name** | Heather Schwartz | Market Catalyst Agent |
| **Platform** | Discord | Discord |
| **OpenClaw version** | 2026.3.22 (98 days old) | Unknown — last git sync March 2026 (~112+ days ago) |
| **Safe upgrade target** | **2026.6.10-stable** (Day 4 — green light) | Unknown — likely same staged path |
| **Primary model** | google/gemini-3-flash-preview (migrate to gemini-3.5-flash) | Unknown |
| **Fallback chain** | OpenRouter Gemini 3.5-flash → Claude 3.5-haiku | Unknown |
| **Cron/heartbeat** | Not deployed (Day 14+ null) | Unknown — critical for trading hours monitoring |
| **Google Workspace OAuth** | NOT connected (Day 98 — **Day 100 in 2 days, June 29**) | N/A (trading bot) |
| **iMessage** | Paused since April 27 (Day 63) | N/A |
| **Repo access** | ✅ `josh_repo` | ⛔ `noah--repo` (404) — correct repo is `Noahrepo2` |
| **Fleet coverage** | ✅ Active | ⛔ BROKEN — Day 18 |

---

## Shared Issues (Both Customers Likely Affected)

### 1. OpenClaw Version: Both Outdated (HIGH)

Josh is confirmed on 2026.3.22 (98 days old). Noah's version is unknown but git sync stopped in March — Noah is almost certainly on an older build as well.

**Current stable target: 2026.6.10** (Day 4 of stable as of June 27 — 96h clean, FULLY OPEN window).
**Skip 2026.6.8 and 2026.6.9** — both have critical regressions.

Staged upgrade path for either customer:
```
<current version> → ... → 2026.6.5 → 2026.6.6 → 2026.6.10
```

**2026.6.10 benefits both customers:**
- Auto fast mode for short conversational turns (Discord response speed)
- PR #96233 heartbeat prompt contribution fix (F53) — direct fix for known heartbeat issue
- PR #93051 cron retry backoff fix — more reliable scheduled monitoring
- Explicit compaction, dry-run message previews, session renaming, SSH preflight (F59)
- Cron delivery awareness persists through restarts
- SQLite migration safety at 2026.6.6 hop (critical for Josh's iMessage fix)

**2026.6.11-beta.1 preview (do not install — monitor for stable):**
- Per-DM model overrides, file-driven operator workflows, richer Discord output
- Codex partial delta + prompt-cache stability for interrupted long agent turns
- RAFT CLI wake bridge for remote agent activation

### 2. Gemini Flash Preview Model Risk (MEDIUM-HIGH)

Josh is confirmed on `google/gemini-3-flash-preview`. Noah's model config is unknown.

**June 25-27 update:** Primary model (`gemini-3-flash-preview`) confirmed safe — sister models shut down June 25 as announced (F52). However, preview models have no GA support SLA and retirement waves arrive on schedule with no reprieves (F52 empirically confirmed). Proactive migration to `google/gemini-3.5-flash` (GA stable) recommended for both customers.

Recommended model config (can apply NOW via Browse tab, no upgrade needed):
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

1,184 malicious skills found on ClawHub in early 2026. Both customers should run `openclaw skill list` post-upgrade.

- Josh's workspace: confirmed clean (empty skill list)
- Noah's skill list: **unknown** — Noah has external Alpaca API access + SEC data access making this the highest-risk profile in the fleet. ClawHavoc specifically targeted financial and trading skill packages.
- SkillSpector scanning is now standard on all new ClawHub installs (F45) — benefits future installs only.

### 4. Cron/Heartbeat Deployment (HIGH for Josh, critical-unknown for Noah)

- Josh: heartbeat cron not deployed — 14+ days of null state. Bundle deployment with the 2026.6.10 upgrade. PR #96233 in 2026.6.10 directly fixes heartbeat prompt contribution — adds urgency to upgrade.
- Noah: unknown. As a trading bot, cron is almost certainly more critical — market-hours monitoring and SEC filing checks require precise scheduling. Without cron, Noah likely misses time-sensitive catalyst events entirely.

### 5. Discord Security: Open to All (MEDIUM-HIGH)

Josh's config has `groupPolicy: open`, `allowFrom: ["*"]`. Noah almost certainly has the same default.

**Particularly important for Noah:** open Discord access + live trading API access = risk of unauthorized trading commands. Should lock to specific user IDs before enabling any real (non-paper) trading capabilities.

---

## Customer-Specific Gaps

### Josh (Heather Schwartz) — Personal Assistant

**Specific to Josh:**
- Google Workspace OAuth not connected (**Day 98 — Day 100 in 2 days, June 29**) — email and calendar completely inaccessible. Fix is a 5-minute OAuth click, independent of any upgrade. Alternative: Remote MCP via AlphaClaw 0.9.18 Envars tab (F60).
- iMessage monitoring paused since April 27 (Day 63) — auto-fix via SQLite migration at 2026.6.6 upgrade step
- No BRAVE_API_KEY set — Heather cannot autonomously search the web; can set NOW via AlphaClaw Envars tab
- Dreaming not enabled — MEMORY.md only updates when manually written
- userTimezone not set — Heather operates in UTC vs Josh's LA/PDT — silent scheduling misalignment
- Discord streaming disabled — can enable `partial` streaming post-upgrade for more responsive feel
- No Discord auto-thread titles — enable post-upgrade

**New for June 27 morning scan:**
- Dry-run message previews available in 2026.6.10 (F59) — directly serves Josh's "ask before acting externally" SOUL.md rule
- Day 100 escalation framing prepared for Heather (F61)
- Remote MCP alternative path for Google Workspace documented (F60)

**Priority for Josh:**
1. Pre-upgrade (today): connect Google Workspace OAuth at https://5.78.142.81.sslip.io#general (**Day 98 — 2 days to milestone**)
2. Pre-upgrade (today): migrate model to gemini-3.5-flash (Browse tab) + set BRAVE_API_KEY (Envars tab)
3. Staged upgrade to 2026.6.10 (VPS/SSH) — Day 4 stable, window fully open
4. Bundle with upgrade: userTimezone, Dreaming, compaction, heartbeat cron, fallback 2 → Haiku 4.5

### Noah (Market Catalyst Agent) — Trading Bot

**Known gaps (pre-scope-break, June 10) + new intelligence from June 27 morning scan:**
- Alpaca MCP Server v2 now has 65 tools (up from 43 in v1) — auto-updates from OpenAPI specs. New: order replacements, option chain exploration, market screening, account activity logs
- Noah is highest-risk for ClawHavoc exposure due to external trading API access
- Last git sync March 2026 (~112+ days ago) — workspace files significantly stale
- **Agent Mesh architecture** (emerging March 2026+): Research Agent → Risk Agent → Execution Agent — Noah may benefit from restructuring into this 3-stage pipeline (F62)
- ClawHub now has 311+ finance/investing skills — `sec-filing-watcher` and `alpaca-trading` both confirmed operational (F62)

**Unknown (requires scope fix to audit):**
- OpenClaw version and upgrade path
- Model config and fallback chain
- Alpaca integration method (native tools vs MCP v1 vs MCP v2 vs clawbot.ai skill)
- SEC filings integration status
- Cron/heartbeat deployment and market-hours schedule
- Installed skills (ClawHavoc risk unknown)
- Discord security settings
- Whether SOUL.md / AGENTS.md / MEMORY.md exist and are current

**Specific improvements ready for Noah (when scope is fixed):**
1. **Alpaca MCP Server v2** — `uvx alpaca-mcp-server` — 65 tools, auto-syncs with Alpaca API changes
2. **sec-filing-watcher** — `openclaw skill install sec-filing-watcher` — EDGAR monitoring with Discord alerts
3. **Agent Mesh restructuring** — split into Research/Risk/Execution agent pipeline for reliability
4. **ClawHavoc audit** — `openclaw skill list` — highest-risk customer profile

---

## Fleet-Wide Recommendations (June 27 Morning)

### Immediate (today, no VPS needed)
1. **Josh:** Connect Google Workspace OAuth — https://5.78.142.81.sslip.io#general — **Day 98, 5 min task**
2. **Josh:** Migrate model config to `google/gemini-3.5-flash` + Haiku 4.5 fallback — AlphaClaw Browse tab
3. **Josh:** Set BRAVE_API_KEY — AlphaClaw Envars tab
4. **Fleet admin:** Fix Noah session scope (`noah--repo` → `Noahrepo2`) — **Day 18 without coverage**

### This Week (requires VPS)
5. **Josh:** Execute staged upgrade to 2026.6.10 (SSH) — Day 4 of stable window, fully open
6. **Josh:** Bundle with upgrade: userTimezone, Dreaming, compaction, heartbeat cron
7. **Noah (after scope fix):** Full workspace audit — version, skills, model config, Alpaca integration, Discord security
8. **Noah (after scope fix):** Staged upgrade to 2026.6.10
9. **Noah (after scope fix):** Evaluate Alpaca MCP Server v2 + `sec-filing-watcher` skill + Agent Mesh restructuring

### Ongoing
10. **Both:** Check Google Gemini deprecation page monthly — waves hit on schedule, no reprieves (F52 confirmed June 25)
11. **Both:** Run `openclaw skill list` after each upgrade — ClawHavoc risk is ongoing
12. **Both:** After upgrade, tighten Discord `allowFrom: ["*"]` to specific user IDs
13. **Both:** Monitor 2026.6.11-stable release — per-DM model overrides and file-driven workflows are high-value for both use cases

---

## Fleet Research Coverage Status

| Customer | Repo | Access | Last Fleet Scan | Coverage |
|----------|------|--------|-----------------|----------|
| Josh | josh_repo | ✅ Active | June 27 morning (F59/F60/F61/F62) | ✅ Comprehensive |
| Noah | Noahrepo2 (needs scope fix) | ⛔ Broken | June 10, 2026 | ⛔ DARK — Day 18 |

**Next scan priority:** Fix Noah scope first. Noah is the higher-risk customer (trading bot, external APIs, unknown OpenClaw version, potential ClawHavoc exposure) and is currently invisible to fleet monitoring.

---

*Sources: [OpenClaw GitHub Releases](https://github.com/openclaw/openclaw/releases) · [AlphaClaw GitHub](https://github.com/chrysb/alphaclaw/releases) · [Releasebot OpenClaw June 2026](https://releasebot.io/updates/openclaw) · [Clawbot.ai Alpaca Skill](https://clawbot.ai/skills/alpaca.html) · [Alpaca MCP Server v2](https://alpaca.markets/blog/alpaca-launches-mcp-server-v2/) · [SEC Filing Watcher](https://lobehub.com/skills/openclaw-skills-sec-filing-watcher) · [ClawStat.us](https://clawstat.us/) · [Google Gemini Deprecations](https://ai.google.dev/gemini-api/docs/deprecations) · [OpenClaw Discord Docs](https://docs.openclaw.ai/channels/discord)*
