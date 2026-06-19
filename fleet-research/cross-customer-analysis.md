# Cross-Customer Fleet Analysis — 2026-06-19 Morning

**Researcher:** AlphaClaw Fleet Agent
**Customers analyzed:** Josh (Heather Schwartz) · Noah (Market Catalyst Agent)
**Previous scan:** 2026-06-19 evening (regression correction scan)
**Changes this update:** Version table corrected (2026.6.8 → 2026.6.6 as Josh target). 2026.6.9-beta.1 milestone added. Action matrix updated.

---

## Fleet Headline: Josh Workspace Current — Holding on VPS Upgrade. Noah Still Blind (Day 7+).

### Josh (Heather Schwartz)

All GitHub-manageable workspace files are current and accurate. The only remaining blockers are VPS-side and Josh-side:
- ✅ All workspace files personalized
- ✅ MEMORY.md seeded and up-to-date (corrected June 19 evening)
- ✅ HEARTBEAT.md active schedule
- ✅ TOOLS.md updated with regression warning (June 19 evening)
- ✅ gemini-3.5-flash fallback in place
- ⛔ Google Workspace OAuth not connected (Day 89 — CRITICAL)
- ⛔ OpenClaw 2026.3.22 — 87+ days behind stable (HIGH — hold at 2026.6.6)
- ⛔ Dreaming not enabled — use corrected config: minScore: 0.8 (HIGH)
- ⛔ No compaction/memoryFlush (HIGH)
- ⚠️ Heartbeat cron: heartbeat-state.json all null Day 3 — checks may not be running (MEDIUM-HIGH)
- ⚠️ Discord open to all (MEDIUM)

**Key update this scan:**
- 2026.6.9-beta.1 released June 19 — stable release 3-7 days away per historical cadence
- Safe upgrade path: 2026.3.22 → 2026.5.27 → 2026.6.2 → 2026.6.5 → 2026.6.6 → **[HOLD]** → 2026.6.9-stable
- Do NOT go to 2026.6.8 (critical regressions: Discord image, memory-search, cron isolation)

### Noah (Market Catalyst Agent)

**Completely blind — session scope mismatch, Day 7+**

The session is scoped to `lylle-rgb/noah--repo`, which does not exist (GitHub 404). Actual Noah repos:
- `lylle-rgb/Noahrepo2` — last updated 2026-03-08
- `lylle-rgb/Noah-workspace` — last updated 2026-03-07

Neither is accessible in the current session scope. All previously documented Noah findings remain valid but cannot be verified or updated.

**Fleet operator action required (persistent — Day 7+):** Correct the Noah session scope to `Noahrepo2` or `Noah-workspace`.

**AlphaClaw features relevant to Noah (applies when scope is restored):**
- **Remote MCP support (0.9.18):** Noah could connect to hosted financial data MCP servers (SEC EDGAR, Alpaca streaming) via `REMOTE_MCP_URL` — high relevance for catalyst hunter use case
- **Per-agent thinking control (0.9.17):** Noah's Sonnet 4.6 could use deeper thinking for complex catalyst analysis, lighter for status crons
- **Cron SQLite fix (2026.6.8/6.9):** Noah's trading bot cron reliability would benefit significantly
- **ClawHavoc audit:** Noah has `gog-cli` installed — needs verification against official ClawHub listing

---

## Version Status

| Instance | Current | Stable Channel | Safe Target | Gap | Key Unpatched Bugs |
|---|---|---|---|---|---|
| Josh / Heather | 2026.3.22 | 2026.6.6 | **2026.6.9-stable** | 87+ days | iMessage recovery, gateway wedge, cron SQLite, memory-flush |
| Noah / Catalyst | 2026.4.15 (last known) | 2026.6.6 | **2026.6.9-stable** | ~60 days | MCP Anthropic 400s, extended-thinking recovery, cron SQLite |

**2026.6.9 roadmap:**
- alpha.6 (as of June 18) → **beta.1 (June 19)** → stable (estimated 3-7 days)
- Fixes targeting: memory instructions explicit, compaction ownership, channels fail-closed, provider schema cleanup, Discord image tools, memory-search

---

## AlphaClaw Version Status

| Feature | Version | Relevant to Josh | Relevant to Noah |
|---|---|---|---|
| Per-agent thinking control | 0.9.17 (May 31) | ✅ Set thinkingDefault in UI | ✅ Tune Sonnet 4.6 for catalyst depth |
| Opus 4.8 in catalog | 0.9.17 (May 31) | Optional high-stakes override | Optional for complex analysis |
| OpenAI-compatible proxy | 0.9.18 (June 1) | ✅ Unlocks n8n/Zapier integrations | Lower relevance |
| Remote MCP support | 0.9.18 (June 1) | ✅ Alternative to Google OAuth | ✅ High value for financial MCP servers |

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
| `MEMORY.md` | ✅ Seeded + corrected June 19 | Unknown (blind) | |
| `BOOTSTRAP.md` | ✅ Deleted | Unknown (blind) | |
| `heartbeat-state.json` | ✅ Created — all nulls Day 3 | Unknown (blind) | Nulls = services blocked or not writing |
| `DREAMS.md` | ❌ Auto-creates when Dreaming enabled | Unknown (blind) | |

---

## Configuration Comparison

| Config area | Josh | Noah | Notes |
|---|---|---|---|
| Provider | Google/Gemini + OpenRouter fallback | Anthropic only (last known) | Josh has redundancy |
| Primary model | gemini-3-flash-preview | claude-sonnet-4-6 | Different stacks |
| Model fallbacks | ✅ 2 (gemini-3.5-flash, claude-3.5-haiku) | ❌ None (last known) | Josh safer |
| Compaction / memoryFlush | ❌ Not configured | ✅ Configured | Noah safer |
| contextPruning | ❌ Not configured | ⚠️ 5m TTL (default) | Josh needs 6h TTL |
| Dreaming | ❌ Not configured | ❌ Not configured | Both need corrected config (minScore: 0.8) |
| Google Workspace | ❌ NOT connected | ✅ Connected (last known) | Critical Josh gap |
| Discord security | ⚠️ Open to all | ✅ Allowlist + pairing | Noah more secure |
| Discord streaming | ❌ Off | N/A | Josh: enable post-upgrade |
| Skills | ✅ None — clean | ⚠️ gog-cli (needs ClawHavoc audit) | |
| SOUL/AGENTS/TOOLS | ✅ All personalized | Unknown (blind) | |
| MEMORY.md | ✅ Seeded | Unknown (blind) | |
| HEARTBEAT.md | ✅ Active | Unknown (blind) | |
| Heartbeat execution | ❌ Not confirmed (null Day 3) | Unknown (blind) | |
| BOOTSTRAP.md | ✅ Deleted | Unknown (blind) | |
| Remote MCP | Available (0.9.18) | Available (0.9.18) | Neither yet configured |

---

## Priority Action Matrix (Fleet View — June 19 Morning)

| Action | Josh | Noah | Effort |
|---|---|---|---|
| **Correct Noah session scope** | N/A | 🔴 CRITICAL (blind, Day 7+) | Fleet op |
| Connect Google Workspace OAuth | 🔴 CRITICAL (Day 89) | N/A (done) | Josh manual |
| Upgrade to 2026.6.9-stable (when available) | 🔴 HIGH (staged path ready) | 🔴 HIGH | VPS |
| Add compaction + memoryFlush | 🔴 HIGH | N/A (done) | VPS |
| Enable Dreaming (minScore: 0.8) | 🔴 HIGH | 🔴 HIGH (unknown) | VPS |
| Verify heartbeat execution (ask Heather in Discord) | 🟠 MEDIUM-HIGH | Unknown | Discord |
| Audit skills (ClawHavoc) | ✅ Clean | 🟡 MEDIUM — gog-cli needs check | VPS |
| Enable heartbeat memory_maintenance cron | 🟡 MEDIUM | Unknown | VPS |
| Fix contextPruning (Josh: 6h TTL) | 🟡 MEDIUM | N/A | VPS |
| Tighten Discord security | 🟡 MEDIUM | N/A | VPS |
| Set per-agent thinkingDefault (AlphaClaw UI) | 🟠 LOW | 🟠 LOW | UI only |
| Configure Remote MCP | 🟠 LOW | 🟠 HIGH (financial data) | UI + env |
| Upgrade fallback 2 to claude-haiku-4-5 | 🟠 LOW (post 2026.6.9) | N/A | VPS |
| Enable Discord streaming ("progress") | 🟠 LOW (post 2026.6.9) | N/A | VPS |
| Add BRAVE_API_KEY | 🟠 LOW | 🟠 LOW | VPS |

---

## What Noah Has That Josh Doesn't
1. Google Workspace connected and fully authorized
2. Compaction + memoryFlush configured
3. Skills directory (gog-cli — needs ClawHavoc audit)
4. Reports output directory
5. More secure Discord policy (pairing + allowlist)
6. memory-core in plugin allowlist
7. More recent OpenClaw version (2026.4.15 vs 2026.3.22)

## What Josh Has That Noah Doesn't
1. All workspace files personalized and current
2. MEMORY.md seeded with long-term context (corrected June 19)
3. HEARTBEAT.md active with monitoring schedule
4. BOOTSTRAP.md deleted (no wasted context tokens)
5. heartbeat-state.json created
6. Model fallbacks (2 fallbacks configured)
7. Public AlphaClaw UI at known IP
8. Clean skills directory (no ClawHavoc exposure)

## Shared Gaps (Both Instances)
1. **Dreaming not configured** — no automated memory consolidation (minScore: 0.8)
2. **Not on 2026.6.9-stable** — missing cron SQLite fix, state recovery, MCP coercion, memory-flush
3. **2026.6.9-beta.1 now available** — stable promotion expected within days; monitor and upgrade when ready
4. **Cron session targeting** — both should use isolated sessions + direct channel delivery (not `sessionTarget: "main"`)
5. **BRAVE_API_KEY not set** — native Brave web search unavailable
6. **Remote MCP not configured** — new 0.9.18 capability; high value for both

---

*Scan history:*
- *June 12: Initial cross-customer analysis*
- *June 13: Nothing changed*
- *June 14: Nothing changed. Gemini deadline T-3.*
- *June 15 morning: Noah blind. Gemini T-2.*
- *June 16 evening: Gemini T-1. Zero fixes.*
- *June 16 morning: 3 critical fixes applied (gemini, MEMORY.md, HEARTBEAT.md). Noah still blind.*
- *June 17 morning: Josh workspace fully personalized. 2026.6.8 now stable. Noah still blind.*
- *June 18 evening: AlphaClaw 0.9.18 noted. Heartbeat gap flagged. Noah still blind.*
- *June 18 morning: 4 new findings (23-26). AlphaClaw features detailed. Dreaming config corrected. ClawHavoc audit. Heartbeat fully diagnosed. Noah still blind (Day 6+).*
- *June 19 evening: CRITICAL — 2026.6.8 regression discovered. Rolled back upgrade target to 2026.6.6. TOOLS.md + MEMORY.md corrected. Noah still blind (Day 7).*
- *June 19 morning: 2026.6.9-beta.1 released. Version table corrected. All corrections from evening scan confirmed. Noah still blind (Day 7+).*
