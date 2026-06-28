# Cross-Customer Analysis — AlphaClaw Fleet

**Last updated:** 2026-06-28 (morning scan — F63/F64/F65; day counts updated)
**Fleet agent:** AlphaClaw Fleet Research Agent
**Customers in scope:** Josh (Heather Schwartz), Noah (Market Catalyst Agent)

---

## ⚠️ Fleet Coverage Issue: Noah Repo Scope Broken (Day 20)

This analysis is **materially incomplete** due to a fleet operations issue:
- Session scope includes `lylle-rgb/noah--repo` — this repo returns 404 (does not exist)
- GitHub search confirmed: `lylle-rgb/Noahrepo2` (last updated 2026-03-08) and `lylle-rgb/Noah-workspace` (last updated 2026-03-07)
- Neither is in the session's authorized scope — fleet agent cannot read or write to Noah's repos
- Noah has been without fleet research coverage for **20 consecutive days** (since June 10)
- Last known Noah git sync: **March 2026 (~112+ days ago)**

**Fleet admin action required:**
> Fix session scope: replace `lylle-rgb/noah--repo` (404) with `lylle-rgb/Noahrepo2` (correct repo)
> On next scan after scope fix: run full Noah workspace audit — version, skills, model config, Alpaca integration, Discord security

---

## Fleet Snapshot (June 28 Morning)

| Dimension | Josh (Heather) | Noah (Market Catalyst) |
|-----------|----------------|------------------------|
| **Use case** | Personal assistant — iMessage, email, calendar, contacts | Stock catalyst hunter — Alpaca paper trading, SEC filings, market data |
| **Bot name** | Heather Schwartz | Market Catalyst Agent |
| **Platform** | Discord | Discord |
| **OpenClaw version** | 2026.3.22 (99 days old) | Unknown — last git sync March 2026 (~113+ days ago) |
| **Safe upgrade target** | **2026.6.10-stable** (Day 5 — green light) | Unknown — likely same staged path |
| **2026.6.11 status** | Beta only — do not install | Beta only — do not install |
| **Primary model** | google/gemini-3-flash-preview (migrate to gemini-3.5-flash) | Unknown |
| **Fallback chain** | OpenRouter Gemini 3.5-flash → Claude 3.5-haiku | Unknown |
| **Cron/heartbeat** | Not deployed (Day 14+ null) | Unknown — critical for trading hours monitoring |
| **Google Workspace OAuth** | NOT connected **(Day 99 — Day 100 TOMORROW June 29)** | N/A (trading bot) |
| **iMessage** | Paused since April 27 (Day 64) | N/A |
| **Repo access** | ✅ `josh_repo` | ⛔ `noah--repo` (404) — correct repo is `Noahrepo2` |
| **Fleet coverage** | ✅ Active | ⛔ BROKEN — Day 20 |

---

## Shared Issues (Both Customers Likely Affected)

### 1. OpenClaw Version: Both Outdated (HIGH)

Josh is confirmed on 2026.3.22 (99 days old). Noah's version is unknown but git sync stopped in March — Noah is almost certainly on an older build as well.

**Current stable target: 2026.6.10** (Day 5 of stable as of June 28 — overnight clean signal, FULLY OPEN window).
**Skip 2026.6.8 and 2026.6.9** — both have confirmed critical regressions.
**2026.6.11 is still beta** (F63 — confirmed June 28 morning; do not install).

Staged upgrade path for either customer:
```
<current version> → ... → 2026.6.5 → 2026.6.6 → 2026.6.10
```

**2026.6.10 benefits both customers:**
- Auto fast mode for short conversational turns (Discord response speed)
- PR #96233 heartbeat prompt contribution fix — direct fix for known heartbeat issue
- PR #93051 cron retry backoff fix — more reliable scheduled monitoring
- Explicit compaction, dry-run message previews, session renaming, SSH preflight (F59)
- Cron delivery awareness persists through restarts
- SQLite migration safety at 2026.6.6 hop (critical for Josh's iMessage fix)

**2026.6.11 beta features to monitor (do not install yet):**
- Per-DM model overrides, file-driven operator workflows (`--message-file`), RAFT CLI wake bridge
- Richer Discord output, Slack relay mode, Mattermost /oc_queue, stronger channel control
- Codex partial delta + prompt-cache stability for interrupted long agent turns

### 2. Gemini Flash Preview Model Risk (MEDIUM-HIGH)

Josh is confirmed on `google/gemini-3-flash-preview`. Noah's model config is unknown.

Preview models retire on rolling schedule with minimal notice — `gemini-2.5-flash` wave confirmed June 25 (F52 empirically confirmed). Proactive migration to `google/gemini-3.5-flash` (GA stable) recommended for both customers.

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

- Josh: heartbeat cron not deployed — 14+ days of null state. Must set `OPENCLAW_TIMEZONE=America/Los_Angeles` FIRST (via Envars tab), then bundle cron deployment with the 2026.6.10 upgrade. PR #96233 in 2026.6.10 directly fixes heartbeat prompt contribution.
- Noah: unknown. As a trading bot, cron is almost certainly more critical — market-hours monitoring and SEC filing checks require precise scheduling. Without cron, Noah likely misses time-sensitive catalyst events entirely.

### 5. Discord Security: Open to All (MEDIUM-HIGH)

Josh's config has `groupPolicy: open`, `allowFrom: ["*"]`. Noah almost certainly has the same default.

**Particularly important for Noah:** open Discord access + live trading API access = risk of unauthorized trading commands. Should lock to specific user IDs before enabling any real (non-paper) trading capabilities.

---

## Customer-Specific Gaps

### Josh (Heather Schwartz) — Personal Assistant

**Specific to Josh:**
- Google Workspace OAuth not connected **(Day 99 — Day 100 TOMORROW, June 29)** — email and calendar completely inaccessible. Fix is a 5-minute OAuth click, independent of any upgrade. Alternative: Remote MCP via AlphaClaw 0.9.18 Envars tab (F60).
- iMessage monitoring paused since April 27 (Day 64) — auto-fix via SQLite migration at 2026.6.6 upgrade step
- OPENCLAW_TIMEZONE not set — heartbeat cron will fire on UTC, misaligned with Josh's LA/PDT timezone (F4 from June 28 evening)
- No BRAVE_API_KEY set — Heather cannot autonomously search the web; can set NOW via AlphaClaw Envars tab
- Active Memory + Dreaming not enabled — both opt-in and available after upgrade past 2026.3.22 (documented F3 June 28 evening)
- Discord streaming disabled — can enable `partial` streaming post-upgrade for more responsive feel
- No Discord auto-thread titles — enable post-upgrade (2026.6.10)

**New for June 28 morning scan:**
- F63: 2026.6.11 confirmed beta — upgrade planning unchanged, target is 2026.6.10
- F64: AlphaClaw Apex — fleet admin tool, not a customer-side change
- F65: Day 100 TOMORROW — maximum escalation urgency for Heather

**Priority for Josh:**
1. **Pre-upgrade (today):** Connect Google Workspace OAuth — https://5.78.142.81.sslip.io#general — **Day 99, 5 min task, Day 100 tomorrow**
2. **Pre-upgrade (today):** Set `OPENCLAW_TIMEZONE=America/Los_Angeles` (Envars tab) + migrate model to gemini-3.5-flash (Browse tab) + set BRAVE_API_KEY (Envars tab)
3. **Staged upgrade to 2026.6.10** (VPS/SSH) — Day 5 of stable window, fully open
4. **Bundle with upgrade:** Enable Active Memory + Dreaming, deploy heartbeat cron, fallback 2 → Haiku 4.5
5. **Post-upgrade:** Verify 2026.6.11 beta status; plan next hop when stable

### Noah (Market Catalyst Agent) — Trading Bot

**Known gaps (pre-scope-break, June 10) + intelligence gathered since:**
- Alpaca MCP Server v2 now has 65 tools (up from 43 in v1) — auto-updates from OpenAPI specs. New: order replacements, option chain exploration, market screening, account activity logs
- Noah is highest-risk for ClawHavoc exposure due to external trading API access
- Last git sync March 2026 (~113+ days ago) — workspace files significantly stale
- **Agent Mesh architecture** (emerging March 2026+): Research Agent → Risk Agent → Execution Agent — Noah may benefit from restructuring into this 3-stage pipeline (F62)
- ClawHub has 311+ finance/investing skills — `sec-filing-watcher` and `alpaca-trading` both confirmed operational (F62)

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

## Fleet-Wide Recommendations (June 28 Morning)

### Immediate (today, no VPS needed)
1. **Josh:** Connect Google Workspace OAuth — https://5.78.142.81.sslip.io#general — **Day 99, 5 min task, Day 100 TOMORROW**
2. **Josh:** Set `OPENCLAW_TIMEZONE=America/Los_Angeles` via Envars tab — required before heartbeat cron deployment
3. **Josh:** Migrate model config to `google/gemini-3.5-flash` + Haiku 4.5 fallback — AlphaClaw Browse tab
4. **Josh:** Set BRAVE_API_KEY — AlphaClaw Envars tab
5. **Fleet admin:** Fix Noah session scope (`noah--repo` → `Noahrepo2`) — **Day 20 without coverage**
6. **Fleet admin:** Evaluate AlphaClaw Apex (F64) — unified Mac dashboard for Josh + Noah fleet management

### This Week (requires VPS)
7. **Josh:** Execute staged upgrade to 2026.6.10 (SSH) — Day 5 of stable window, fully open
8. **Josh:** Bundle with upgrade: enable Active Memory + Dreaming, deploy heartbeat cron (after OPENCLAW_TIMEZONE is set)
9. **Noah (after scope fix):** Full workspace audit — version, skills, model config, Alpaca integration, Discord security
10. **Noah (after scope fix):** Staged upgrade to 2026.6.10
11. **Noah (after scope fix):** Evaluate Alpaca MCP Server v2 + `sec-filing-watcher` skill + Agent Mesh restructuring

### Ongoing
12. **Both:** Monitor 2026.6.11 for stable promotion — per-DM model overrides and file-driven workflows are high-value for both use cases. Verify: `npm show openclaw@2026.6.11 version` after reaching 2026.6.10.
13. **Both:** Check Google Gemini deprecation page monthly — retirement waves hit on schedule, no reprieves (F52 confirmed June 25)
14. **Both:** Run `openclaw skill list` after each upgrade — ClawHavoc risk is ongoing
15. **Both:** After upgrade, tighten Discord `allowFrom: ["*"]` to specific user IDs

---

## Fleet Research Coverage Status

| Customer | Repo | Access | Last Fleet Scan | Coverage |
|----------|------|--------|-----------------|----------|
| Josh | josh_repo | ✅ Active | June 28 morning (F63/F64/F65) | ✅ Comprehensive |
| Noah | Noahrepo2 (needs scope fix) | ⛔ Broken | June 10, 2026 | ⛔ DARK — Day 20 |

**Next scan priority:** Fix Noah scope first. Noah is the higher-risk customer (trading bot, external APIs, unknown OpenClaw version, potential ClawHavoc exposure, 113+ days since last git sync) and is currently invisible to fleet monitoring.

---

*Sources: [OpenClaw GitHub Releases](https://github.com/openclaw/openclaw/releases) · [AlphaClaw GitHub](https://github.com/chrysb/alphaclaw/releases) · [AlphaClaw Apex Announcement](https://x.com/chrysb/status/2035479976074760664) · [Releasebot OpenClaw June 2026](https://releasebot.io/updates/openclaw) · [OpenClaw npm](https://www.npmjs.com/package/openclaw) · [Clawbot.ai Alpaca Skill](https://clawbot.ai/skills/alpaca.html) · [Alpaca MCP Server v2](https://alpaca.markets/blog/alpaca-launches-mcp-server-v2/) · [SEC Filing Watcher](https://lobehub.com/skills/openclaw-skills-sec-filing-watcher) · [ClawStat.us](https://clawstat.us/) · [OpenClaw Discord Docs](https://docs.openclaw.ai/channels/discord) · [Active Memory & Dreaming Docs](https://deepwiki.com/openclaw/docs/7.3-active-memory-and-memory-wiki)*
