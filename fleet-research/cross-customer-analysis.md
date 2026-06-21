# Cross-Customer Fleet Analysis — 2026-06-21 Morning

**Researcher:** AlphaClaw Fleet Agent
**Customers analyzed:** Josh (Heather Schwartz) · Noah (Market Catalyst Agent)
**Previous scan:** 2026-06-21 Evening — 2026.6.9-stable shipped, upgrade window opened
**Changes this update:** F30 (BRAVE_API_KEY), F31 (fallback chain), F32 (iMessage SQLite path). Noah scope still blocked Day 10. Upgrade window confirmed open.

---

## Fleet Headline: 2026.6.9 Upgrade Window Open. 3 New Findings. Noah Blind Day 10.

### Josh (Heather Schwartz)

Three new findings from morning research scan:

**F30 (NEW): BRAVE_API_KEY not set** — Heather cannot search the web. This is a core capability gap for a personal assistant that needs to research facts, look up news, and check current information. Fix is available via AlphaClaw UI without VPS access (Envars tab). Free tier: 2,000 queries/month via Brave Search API.

**F31 (NEW): Same-provider fallback chain** — Primary and Fallback 1 are both Google/Gemini. A Google outage burns through Fallback 1 without any cross-provider protection until Haiku 4.5 (Fallback 2). Fix: swap order, promote Haiku 4.5 to Fallback 1 during the upgrade session.

**F32 (NEW, POSITIVE): iMessage SQLite auto-fix** — The malformed inbox-state.json will be automatically fixed by the 2026.6.1 SQLite migration in the staged upgrade path. The iMessage pause (Day 56) may partially resolve after upgrade with no additional manual action.

All GitHub-manageable workspace files remain current as of June 21:
- ✅ All workspace files personalized and accurate
- ✅ MEMORY.md up-to-date (June 21 evening)
- ✅ HEARTBEAT.md active schedule
- ✅ TOOLS.md with regression warning
- ✅ gemini-3.5-flash fallback in place
- 🆕 F30: BRAVE_API_KEY not set — web search disabled (MEDIUM-HIGH)
- 🆕 F31: Same-provider fallback chain gap (MEDIUM)
- 🆕 F32: iMessage auto-fix path on upgrade (POSITIVE)
- ⛔ Google Workspace OAuth not connected (Day 91 — CRITICAL)
- ⛔ OpenClaw 2026.3.22 — upgrade window is OPEN (HIGH)
- ⛔ Dreaming not enabled (HIGH — use minScore 0.8, add userTimezone first)
- ⛔ No compaction/memoryFlush (HIGH)
- ⛔ heartbeat-state.json all null Day 6 — cron not deployed (HIGH)
- ⚠️ Discord open to all (MEDIUM-HIGH)

**Key update this morning:**
- 2026.6.9-stable confirmed stable overnight — no regression reports, no new release. Upgrade window is OPEN.
- npm `latest` = 2026.6.9. Verify with `npm show openclaw@latest version` before upgrading.
- BRAVE_API_KEY gap: quick win available via AlphaClaw UI right now — no VPS session needed.
- iMessage fix is baked into the upgrade path (F32) — no extra work needed.

### Noah (Market Catalyst Agent)

**Completely blind — session scope mismatch, Day 10**

The session is scoped to `lylle-rgb/noah--repo`, which does not exist (GitHub 404). Actual Noah repos found via search:
- `lylle-rgb/Noahrepo2` — last updated 2026-03-08
- `lylle-rgb/Noah-workspace` — last updated 2026-03-07

Neither is accessible in the current session scope. All previously documented Noah findings remain unverifiable.

**Fleet operator action required (persistent — Day 10):** Correct the Noah session scope by adding `lylle-rgb/Noah-workspace` to the allowed repos for Noah's fleet agent session.

**Findings relevant to Noah once scope is restored:**
- **F30 equivalent:** BRAVE_API_KEY — also likely not set for Noah. For a market catalyst hunter, real-time web search is arguably even more critical than for Heather. Catalyst detection depends on live news and SEC filing alerts.
- **F31 check:** Noah uses `claude-sonnet-4-6` as primary. If fallbacks exist, verify cross-provider diversity.
- **Remote MCP (0.9.18):** Noah's Alpaca + SEC data tools are natural candidates for hosted financial MCP servers. Highest-ROI upgrade for a trading bot once scope is restored.
- **Alpaca MCP Server v2 (April 2026):** Major overhaul focused on scalability and reliability. Natural-language → market action with no-code wiring. Priority upgrade for Noah.
- **Per-agent thinking control (0.9.17):** Noah's Sonnet 4.6 could use deeper thinking for catalyst analysis, lighter for status crons — set per-task from AlphaClaw UI.
- **userTimezone (Finding 28 equivalent):** If Noah's VPS is UTC and `userTimezone` is not set, market-hours-aware scheduling (e.g., "only trade 9:30 AM–4 PM ET") will drift. Especially critical for a trading bot tied to NYSE hours.
- **ClawHavoc audit:** Noah has `gog-cli` installed — needs verification against official ClawHub listing.

---

## Version Status

| Instance | Current | npm Stable | Safe Target | Gap | Notes |
|---|---|---|---|---|---|
| Josh / Heather | 2026.3.22 | **2026.6.9** | **2026.6.9-stable** | 90 days | Upgrade window OPEN |
| Noah / Catalyst | 2026.4.15 (last known) | **2026.6.9** | **2026.6.9-stable** | ~67 days | Scope blocked — cannot verify |

**Upgrade window:** OPEN for both instances. No new release overnight. 2026.6.8 = permanent skip. Staged path for Josh: 2026.3.22 → 2026.5.27 → 2026.6.2 → 2026.6.5 → 2026.6.6 → 2026.6.9.

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
| `MEMORY.md` | ✅ Seeded + updated June 21 | Unknown (blind) | |
| `BOOTSTRAP.md` | ✅ Deleted | Unknown (blind) | |
| `heartbeat-state.json` | ✅ Created — all nulls Day 6 | Unknown (blind) | |
| `DREAMS.md` | ❌ Auto-creates when Dreaming enabled | Unknown (blind) | |

---

## Configuration Comparison

| Config area | Josh | Noah | Notes |
|---|---|---|---|
| Provider | Google/Gemini + OpenRouter fallback | Anthropic only (last known) | Josh has redundancy |
| Primary model | gemini-3-flash-preview | claude-sonnet-4-6 | Different stacks |
| Model fallbacks | ✅ 2 fallbacks — but same-provider gap (F31) | ❌ None (last known) | Both need cross-provider check |
| userTimezone | ❌ Not set (Finding 28) | Unknown (blind) | Both likely need this |
| Compaction / memoryFlush | ❌ Not configured | ✅ Configured | Noah safer |
| contextPruning | ❌ Not configured | ⚠️ 5m TTL (default) | Josh needs 6h TTL |
| Dreaming | ❌ Not configured | ❌ Not configured | Both need corrected config |
| Google Workspace | ❌ NOT connected | ✅ Connected (last known) | Critical Josh gap |
| Discord security | ⚠️ Open to all | ✅ Allowlist + pairing | Noah more secure |
| Discord streaming | ❌ Off | N/A | Josh: enable post-2026.6.9 |
| Skills | ✅ None — clean | ⚠️ gog-cli (needs ClawHavoc audit) | |
| BRAVE_API_KEY | ❌ Not set (F30) | ❌ Likely not set (F30 equivalent) | Both need this |
| Alpaca MCP v2 | N/A | Unknown — upgrade recommended | High value for Noah |
| Remote MCP | Not configured | Not configured | Both available via 0.9.18 |
| SOUL/AGENTS/TOOLS | ✅ All personalized | Unknown (blind) | |
| MEMORY.md | ✅ Seeded + current | Unknown (blind) | |
| Heartbeat execution | ❌ Not confirmed (null Day 6) | Unknown (blind) | |

---

## Priority Action Matrix (Fleet View — June 21 Morning)

| Action | Josh | Noah | Effort |
|---|---|---|---|
| **Correct Noah session scope** | N/A | 🔴 CRITICAL (blind, Day 10) | Fleet op |
| Connect Google Workspace OAuth | 🔴 CRITICAL (Day 91) | N/A (done) | Josh manual |
| Set BRAVE_API_KEY (AlphaClaw UI — no VPS needed) | 🔴 MEDIUM-HIGH (F30) | 🔴 HIGH (F30 equiv.) | UI only |
| Upgrade to 2026.6.9-stable — window OPEN | 🔴 HIGH (staged path ready) | 🔴 HIGH | VPS |
| Set `userTimezone: "America/Los_Angeles"` | 🔴 MEDIUM-HIGH (do first, pre-upgrade) | 🟠 UNKNOWN (likely needs it) | VPS edit |
| Add compaction + memoryFlush | 🔴 HIGH | N/A (done) | VPS |
| Enable Dreaming (minScore: 0.8) | 🔴 HIGH | 🔴 HIGH (unknown) | VPS |
| Fix fallback chain cross-provider (F31) | 🟡 MEDIUM | 🟡 UNKNOWN | VPS |
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
6. Model fallbacks (2 fallbacks configured, though same-provider gap exists)
7. Public AlphaClaw UI at known IP
8. Clean skills directory (no ClawHavoc exposure)

## Shared Gaps (Both Instances)
1. **BRAVE_API_KEY not set** — web search unavailable (F30 for Josh; equivalent gap assumed for Noah)
2. **`userTimezone` likely not set** — silent TZ drift risk for heartbeat/dreaming/cron schedules (Finding 28)
3. **Dreaming not configured** — no automated memory consolidation (minScore: 0.8)
4. **Not on 2026.6.9-stable** — upgrade window is OPEN, both need upgrading
5. **Remote MCP not configured** — new 0.9.18 capability; high value for both (especially Noah's financial data)
6. **Cross-provider fallback chain** — review needed for both (F31 confirmed for Josh; Noah's config unknown)

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
- *June 21 evening: 2026.6.9-stable shipped — upgrade window OPENED. Noah still blind (Day 9).*
- *June 21 morning: F30 (Brave search), F31 (fallback chain), F32 (iMessage SQLite path). Upgrade window confirmed open. Noah still blind (Day 10).*
