# Cross-Customer Fleet Analysis — 2026-06-20 Morning

**Researcher:** AlphaClaw Fleet Agent
**Customers analyzed:** Josh (Heather Schwartz) · Noah (Market Catalyst Agent)
**Previous scan:** 2026-06-20 evening — Day 4 heartbeat escalation, Google Workspace Day 90
**Changes this update:** Finding 28 (userTimezone) added. Noah scope still blocked Day 8. Version hold confirmed morning of June 20.

---

## Fleet Headline: Josh Workspace Current. One New Config Risk Found. Noah Still Blind (Day 8).

### Josh (Heather Schwartz)

New finding this morning: **`userTimezone` is not set in openclaw.json** (Finding 28). This is a silent risk that will cause heartbeat and dreaming schedules to evaluate against UTC (VPS timezone) rather than LA time (PDT, UTC−7 in June). The fix is one line. It must be added before any heartbeat activeHours or dreaming schedule.

All GitHub-manageable workspace files remain current:
- ✅ All workspace files personalized and accurate
- ✅ MEMORY.md up-to-date (June 19 evening)
- ✅ HEARTBEAT.md active schedule
- ✅ TOOLS.md with regression warning
- ✅ gemini-3.5-flash fallback in place
- 🆕 Finding 28: `userTimezone` not set — add before upgrading (MEDIUM-HIGH)
- ⛔ Google Workspace OAuth not connected (Day 90 — CRITICAL)
- ⛔ OpenClaw 2026.3.22 — 90+ days behind stable (HIGH — staged path ready)
- ⛔ Dreaming not enabled (HIGH — use minScore 0.8, add userTimezone first)
- ⛔ No compaction/memoryFlush (HIGH)
- ⛔ heartbeat-state.json all null Day 4 — cron likely not deployed (HIGH)
- ⚠️ Discord open to all (MEDIUM-HIGH)

**Key update this morning:**
- 2026.6.9-stable still not shipped — stable could arrive today/tomorrow based on beta cadence
- Hold confirmed: npm `latest` = 2026.6.6 (GitHub "Latest" badge ≠ npm stable — see findings.md)
- Staged path: 2026.3.22 → 2026.5.27 → 2026.6.2 → 2026.6.5 → 2026.6.6 → **[HOLD]** → 2026.6.9-stable

### Noah (Market Catalyst Agent)

**Completely blind — session scope mismatch, Day 8**

The session is scoped to `lylle-rgb/noah--repo`, which does not exist (GitHub 404). Actual Noah repos found:
- `lylle-rgb/Noahrepo2` — last updated 2026-03-08
- `lylle-rgb/Noah-workspace` — last updated 2026-03-07

Neither is accessible in the current session scope. All previously documented Noah findings remain unverifiable.

**Fleet operator action required (persistent — Day 8):** Correct the Noah session scope.

**AlphaClaw/OpenClaw features relevant to Noah (applies when scope is restored):**
- **Remote MCP (0.9.18):** Noah's Alpaca + SEC data tools are natural candidates for hosted financial MCP servers. This is the highest-ROI upgrade for a trading bot.
- **Alpaca MCP Server v2 (April 2026):** Alpaca released a major MCP Server v2 overhaul focused on scalability and reliability. If Noah is using Alpaca's MCP integration, v2 is worth upgrading to. Key: natural-language → market action with no-code wiring.
- **Per-agent thinking control (0.9.17):** Noah's Sonnet 4.6 could use deeper thinking for catalyst analysis, lighter for status crons — set per-task from AlphaClaw UI.
- **Cron SQLite fix (2026.6.8/6.9):** Noah's trading cron reliability would benefit significantly once 2026.6.9-stable lands.
- **userTimezone (Finding 28):** This applies to Noah too. If Noah's VPS is UTC and `userTimezone` is not set, any market-hours-aware scheduling (e.g., "only trade 9:30 AM–4 PM ET") will drift relative to UTC. Especially important for a trading bot.
- **ClawHavoc audit:** Noah has `gog-cli` installed — needs verification against official ClawHub listing.

---

## Version Status

| Instance | Current | npm Stable | Safe Target | Gap | Notes |
|---|---|---|---|---|---|
| Josh / Heather | 2026.3.22 | 2026.6.6 | **2026.6.9-stable** | 90 days | userTimezone not set |
| Noah / Catalyst | 2026.4.15 (last known) | 2026.6.6 | **2026.6.9-stable** | ~67 days | Scope blocked — cannot verify |

**2026.6.9 roadmap:**
- beta.1 (June 19) → stable (estimated today–tomorrow based on 3-7 day beta cadence)
- Fixes landing in 2026.6.9: Discord image tools, memory-search, cron isolation, agent recovery, compaction ownership

---

## AlphaClaw Version Status

| Feature | Version | Relevant to Josh | Relevant to Noah |
|---|---|---|---|
| Per-agent thinking control | 0.9.17 (May 31) | ✅ Set thinkingDefault in UI | ✅ Tune Sonnet 4.6 for catalyst depth |
| Opus 4.8 in catalog | 0.9.17 (May 31) | Optional high-stakes override | Optional for complex analysis |
| OpenAI-compatible proxy | 0.9.18 (June 1) | ✅ Unlocks n8n/Zapier integrations | Lower relevance |
| Remote MCP support | 0.9.18 (June 1) | ✅ Google Workspace alternative | ✅ HIGH — Alpaca MCP v2, SEC EDGAR |
| Alpaca MCP Server v2 | April 2026 | — | ✅ HIGH — trading infra upgrade |

---

## Workspace File Status

| File | Josh | Noah | Notes |
|---|---|---|---|
| `SOUL.md` | ✅ Personalized (June 17) | Unknown (blind) | |
| `AGENTS.md` | ✅ Personalized (June 17) | Unknown (blind) | |
| `TOOLS.md` | ✅ Updated June 19 — regression warning | Unknown (blind) | |
| `USER.md` | ✅ Filled | Unknown (blind) | |
| `IDENTITY.md` | ✅ Heather | Unknown (blind) | |
| `HEARTBEAT.md` | ✅ Active schedule | Unknown (blind) | |
| `MEMORY.md` | ✅ Seeded + updated June 19 | Unknown (blind) | |
| `BOOTSTRAP.md` | ✅ Deleted | Unknown (blind) | |
| `heartbeat-state.json` | ✅ Created — all nulls Day 4 | Unknown (blind) | |
| `DREAMS.md` | ❌ Auto-creates when Dreaming enabled | Unknown (blind) | |

---

## Configuration Comparison

| Config area | Josh | Noah | Notes |
|---|---|---|---|
| Provider | Google/Gemini + OpenRouter fallback | Anthropic only (last known) | Josh has redundancy |
| Primary model | gemini-3-flash-preview | claude-sonnet-4-6 | Different stacks |
| Model fallbacks | ✅ 2 (gemini-3.5-flash, claude-3.5-haiku) | ❌ None (last known) | Josh safer |
| userTimezone | ❌ Not set (NEW risk — Finding 28) | Unknown (blind) | Both likely need this |
| Compaction / memoryFlush | ❌ Not configured | ✅ Configured | Noah safer |
| contextPruning | ❌ Not configured | ⚠️ 5m TTL (default) | Josh needs 6h TTL |
| Dreaming | ❌ Not configured | ❌ Not configured | Both need corrected config |
| Google Workspace | ❌ NOT connected | ✅ Connected (last known) | Critical Josh gap |
| Discord security | ⚠️ Open to all | ✅ Allowlist + pairing | Noah more secure |
| Discord streaming | ❌ Off | N/A | Josh: enable post-2026.6.9-stable |
| Skills | ✅ None — clean | ⚠️ gog-cli (needs ClawHavoc audit) | |
| Alpaca MCP v2 | N/A | Unknown — upgrade recommended | High value for Noah |
| Remote MCP | Not configured | Not configured | Both available via 0.9.18 |
| SOUL/AGENTS/TOOLS | ✅ All personalized | Unknown (blind) | |
| MEMORY.md | ✅ Seeded + current | Unknown (blind) | |
| Heartbeat execution | ❌ Not confirmed (null Day 4) | Unknown (blind) | |

---

## Priority Action Matrix (Fleet View — June 20 Morning)

| Action | Josh | Noah | Effort |
|---|---|---|---|
| **Correct Noah session scope** | N/A | 🔴 CRITICAL (blind, Day 8) | Fleet op |
| Connect Google Workspace OAuth | 🔴 CRITICAL (Day 90) | N/A (done) | Josh manual |
| Set `userTimezone: "America/Los_Angeles"` | 🔴 MEDIUM-HIGH (do first, pre-upgrade) | 🟠 UNKNOWN (likely needs it) | VPS edit |
| Upgrade to 2026.6.9-stable (when available) | 🔴 HIGH (staged path ready) | 🔴 HIGH | VPS |
| Add compaction + memoryFlush | 🔴 HIGH | N/A (done) | VPS |
| Enable Dreaming (minScore: 0.8) | 🔴 HIGH | 🔴 HIGH (unknown) | VPS |
| Verify heartbeat execution (ask Heather in Discord) | 🟠 MEDIUM-HIGH | Unknown | Discord |
| Upgrade Alpaca to MCP Server v2 | N/A | 🟠 HIGH (when scope restored) | VPS |
| Audit skills (ClawHavoc) | ✅ Clean | 🟡 MEDIUM — gog-cli needs check | VPS |
| Deploy heartbeat cron to openclaw.json | 🟡 MEDIUM | Unknown | VPS |
| Fix contextPruning (Josh: 6h TTL) | 🟡 MEDIUM | N/A | VPS |
| Tighten Discord security | 🟡 MEDIUM | N/A | VPS |
| Set per-agent thinkingDefault (AlphaClaw UI) | 🟠 LOW | 🟠 LOW | UI only |
| Configure Remote MCP | 🟠 LOW | 🟠 HIGH (financial data) | UI + env |
| Upgrade fallback 2 to claude-haiku-4-5 | 🟠 LOW (post 2026.6.9) | N/A | VPS |
| Enable Discord streaming ("progress") | 🟠 LOW (post 2026.6.9) | N/A | VPS |

---

## What Noah Has That Josh Doesn't
1. Google Workspace connected and fully authorized
2. Compaction + memoryFlush configured
3. Skills directory (gog-cli — needs ClawHavoc audit)
4. More recent OpenClaw version (2026.4.15 vs 2026.3.22)
5. More secure Discord policy (pairing + allowlist)

## What Josh Has That Noah Doesn't
1. All workspace files personalized and current
2. MEMORY.md seeded with long-term context
3. HEARTBEAT.md active with monitoring schedule
4. BOOTSTRAP.md deleted (no wasted context tokens)
5. heartbeat-state.json created
6. Model fallbacks (2 fallbacks configured)
7. Public AlphaClaw UI at known IP
8. Clean skills directory (no ClawHavoc exposure)

## Shared Gaps (Both Instances)
1. **`userTimezone` likely not set** — silent TZ drift risk for heartbeat/dreaming/cron schedules (Finding 28)
2. **Dreaming not configured** — no automated memory consolidation (minScore: 0.8)
3. **Not on 2026.6.9-stable** — missing cron SQLite fix, state recovery, memory-flush, Discord image tools
4. **2026.6.9-stable pending** — monitor daily; stable promotion expected imminently based on beta cadence
5. **BRAVE_API_KEY not set** — native Brave web search unavailable
6. **Remote MCP not configured** — new 0.9.18 capability; high value for both (especially Noah's financial data)

---

*Scan history:*
- *June 12: Initial cross-customer analysis*
- *June 13–14: Nothing changed. Gemini deadline approaching.*
- *June 15 morning: Noah blind. Gemini T-2.*
- *June 16: 3 critical fixes (gemini fallback, MEMORY.md, HEARTBEAT.md). Noah still blind.*
- *June 17: Josh workspace fully personalized. Noah still blind.*
- *June 18: AlphaClaw 0.9.18 noted. Heartbeat gap flagged. Noah still blind (Day 6+).*
- *June 19 evening: CRITICAL — 2026.6.8 regressions discovered. Rolled back to 2026.6.6. Noah still blind (Day 7).*
- *June 19 morning: 2026.6.9-beta.1 released. All corrections confirmed. Noah still blind (Day 7+).*
- *June 20 evening: Day 4 heartbeat null escalated. Google Workspace Day 90. Noah still blind (Day 7+).*
- *June 20 morning: Finding 28 (userTimezone). Hold confirmed. Alpaca MCP v2 noted for Noah. Noah still blind (Day 8).*
