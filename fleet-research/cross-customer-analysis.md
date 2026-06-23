# Cross-Customer Fleet Analysis — 2026-06-23 Morning

**Researcher:** AlphaClaw Fleet Agent
**Customers analyzed:** Josh (Heather Schwartz) · Noah (Market Catalyst Agent)
**Previous scan:** 2026-06-23 Evening — F38 (HEARTBEAT.md), F39 (Discord V2), F40 (group chat context), F41 (MEMORY.md day counts)
**Changes this update:** F42 (Gemini preview sunset wave — 2-day warning). Noah scope still blocked Day 13.

---

## Fleet Headline: Gemini Preview Sunset Wave — 2 Models Shut Down June 25 (2 Days). Upgrade Window Day 4. Noah Blind Day 13.

### Josh (Heather Schwartz)

One new finding from morning research scan:

**F42 (NEW): Gemini preview model sunset wave — 2-day warning** — Google is shutting down `gemini-3.1-flash-image-preview` and `gemini-3-pro-image-preview` on June 25 (2 days). Josh's primary model `google/gemini-3-flash-preview` is in the same preview cohort. No confirmed shutdown date for this exact model ID, but the pattern is clear: Google is retiring preview-generation Gemini models as Gemini 3.5 Flash GA becomes the standard. Recommended action: verify at https://ai.google.dev/gemini-api/docs/deprecations, then migrate primary to `google/gemini-3.5-flash` (GA stable). Bundle with F31 fix (cross-provider fallback chain) in the upgrade session.

All GitHub-manageable workspace files remain current as of June 23 evening:
- ✅ All workspace files personalized and accurate
- ✅ MEMORY.md up-to-date (June 23 evening)
- ✅ HEARTBEAT.md — cron-not-deployed warning added (F38)
- ✅ TOOLS.md with regression warning and correct upgrade target
- ✅ gemini-3.5-flash fallback in place (Fallback 1 solid — GA stable)
- 🆕 F42: Gemini preview sunset wave — primary model at risk (MEDIUM-HIGH) — NEW
- ⛔ BRAVE_API_KEY not set — web search disabled (MEDIUM-HIGH, F30)
- ⛔ Google Workspace OAuth not connected (Day 94 — CRITICAL)
- ⛔ OpenClaw 2026.3.22 — upgrade window is OPEN Day 4 (HIGH)
- ⛔ Dreaming not enabled (HIGH — use minScore 0.8, add userTimezone first)
- ⛔ No compaction/memoryFlush (HIGH)
- ⛔ heartbeat-state.json all null Day 9 — cron not deployed (HIGH)
- ⚠️ Discord open to all (MEDIUM-HIGH)
- ⚠️ Same-provider fallback chain (MEDIUM — now combine with F42 migration)

**Key update this morning:**
- F42 NEW: Gemini preview sunset wave — 2 models shut down June 25. Verify `gemini-3-flash-preview` status and plan migration to `gemini-3.5-flash` (GA stable) — bundle into upgrade session.
- Upgrade window remains OPEN — npm `latest` = 2026.6.9, Day 4 clean.
- No new beta release overnight (2026.6.10-beta.2 remains Day 3 — do not install).

### Noah (Market Catalyst Agent)

**Completely blind — session scope mismatch, Day 13**

The session is scoped to `lylle-rgb/noah--repo`, which does not exist (GitHub 404). Actual Noah repos found via search:
- `lylle-rgb/Noahrepo2` — last updated 2026-03-08
- `lylle-rgb/Noah-workspace` — last updated 2026-03-07

Neither is accessible in the current session scope. All previously documented Noah findings remain unverifiable.

**F42 applies to Noah too:** If Noah's Market Catalyst Agent uses any Gemini preview model as primary or fallback, the sunset wave is equally urgent. For a trading bot with time-sensitive catalyst detection, a silent model fallback during market hours could cause missed signals. This should be verified immediately when scope is restored.

**Fleet operator action required (persistent — Day 13):** Correct the Noah session scope by adding `lylle-rgb/Noah-workspace` to the allowed repos for Noah's fleet agent session.

**Findings relevant to Noah once scope is restored:**
- **F42 check:** Verify Noah's model config for any `*-preview` Gemini models — same sunset risk applies
- **F30 equivalent:** BRAVE_API_KEY — also likely not set for Noah. For a market catalyst hunter, real-time web search is arguably even more critical than for Heather. Catalyst detection depends on live news and SEC filing alerts.
- **Remote MCP (0.9.18):** Noah's Alpaca + SEC data tools are natural candidates for hosted financial MCP servers. Highest-ROI upgrade for a trading bot once scope is restored.
- **Alpaca MCP Server v2 (April 2026):** Major overhaul focused on scalability and reliability. Natural-language → market action with no-code wiring. Priority upgrade for Noah.
- **Per-agent thinking control (0.9.17):** Noah's Sonnet 4.6 could use deeper thinking for catalyst analysis, lighter for status crons — set per-task from AlphaClaw UI.
- **userTimezone:** If Noah's VPS is UTC and `userTimezone` is not set, market-hours-aware scheduling (e.g., "only trade 9:30 AM–4 PM ET") will drift. Critical for a trading bot tied to NYSE hours.
- **ClawHub audit:** Noah has `gog-cli` installed — needs verification against official ClawHub listing given 800+ malicious skills documented on ClawHub.

---

## Version Status

| Instance | Current | npm Stable | Safe Target | Gap | Notes |
|---|---|---|---|---|---|
| Josh / Heather | 2026.3.22 | **2026.6.9** | **2026.6.9-stable** | 94 days | Upgrade window OPEN Day 4 |
| Noah / Catalyst | 2026.4.15 (last known) | **2026.6.9** | **2026.6.9-stable** | ~67 days | Scope blocked — cannot verify |

**Upgrade window:** OPEN for both instances. 2026.6.9 confirmed stable 4 days. 2026.6.8 = permanent skip. Staged path for Josh: 2026.3.22 → 2026.5.27 → 2026.6.2 → 2026.6.5 → 2026.6.6 → 2026.6.9.

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
| `TOOLS.md` | ✅ Updated June 22 — correct targets | Unknown (blind) | |
| `USER.md` | ✅ Filled | Unknown (blind) | |
| `IDENTITY.md` | ✅ Heather | Unknown (blind) | |
| `HEARTBEAT.md` | ✅ Cron-not-deployed warning added (June 23) | Unknown (blind) | |
| `MEMORY.md` | ✅ Seeded + updated June 23 eve | Unknown (blind) | |
| `BOOTSTRAP.md` | ✅ Deleted | Unknown (blind) | |
| `heartbeat-state.json` | ✅ Created — all nulls Day 9 | Unknown (blind) | |
| `DREAMS.md` | ❌ Auto-creates when Dreaming enabled | Unknown (blind) | |

---

## Configuration Comparison

| Config area | Josh | Noah | Notes |
|---|---|---|---|
| Provider | Google/Gemini + OpenRouter fallback | Anthropic only (last known) | Josh has redundancy |
| Primary model | gemini-3-flash-preview ⚠️ | claude-sonnet-4-6 | F42: Josh's preview model at risk |
| Model fallbacks | ✅ 2 fallbacks — but same-provider gap (F31) | ❌ None (last known) | Both need cross-provider check |
| userTimezone | ❌ Not set (Finding 28) | Unknown (blind) | Both likely need this |
| Compaction / memoryFlush | ❌ Not configured | ✅ Configured | Noah safer |
| contextPruning | ❌ Not configured | ⚠️ 5m TTL (default) | Josh needs 6h TTL |
| Dreaming | ❌ Not configured | ❌ Not configured | Both need corrected config |
| Google Workspace | ❌ NOT connected | ✅ Connected (last known) | Critical Josh gap |
| Discord security | ⚠️ Open to all | ✅ Allowlist + pairing | Noah more secure |
| Discord streaming | ❌ Off | N/A | Josh: enable post-2026.6.9 |
| Skills | ✅ None — clean | ⚠️ gog-cli (needs ClawHub audit) | |
| BRAVE_API_KEY | ❌ Not set (F30) | ❌ Likely not set (F30 equivalent) | Both need this |
| Alpaca MCP v2 | N/A | Unknown — upgrade recommended | High value for Noah |
| Remote MCP | Not configured | Not configured | Both available via 0.9.18 |
| SOUL/AGENTS/TOOLS | ✅ All personalized | Unknown (blind) | |
| MEMORY.md | ✅ Seeded + current | Unknown (blind) | |
| Heartbeat execution | ❌ Not confirmed (null Day 9) | Unknown (blind) | |

---

## Priority Action Matrix (Fleet View — June 23 Morning)

| Action | Josh | Noah | Effort |
|---|---|---|---|
| **Correct Noah session scope** | N/A | 🔴 CRITICAL (blind, Day 13) | Fleet op |
| F42: Verify + migrate from gemini-3-flash-preview | 🟠 MEDIUM-HIGH (new) | 🟠 CHECK (if using preview model) | VPS/config |
| Connect Google Workspace OAuth | 🔴 CRITICAL (Day 94) | N/A (done) | Josh manual |
| Set BRAVE_API_KEY (AlphaClaw UI — no VPS needed) | 🔴 MEDIUM-HIGH (F30) | 🔴 HIGH (F30 equiv.) | UI only |
| Upgrade to 2026.6.9-stable — window OPEN Day 4 | 🔴 HIGH (staged path ready) | 🔴 HIGH | VPS |
| Set `userTimezone: "America/Los_Angeles"` | 🔴 MEDIUM-HIGH (do first, pre-upgrade) | 🟠 UNKNOWN (likely needs it) | VPS edit |
| Add compaction + memoryFlush | 🔴 HIGH | N/A (done) | VPS |
| Enable Dreaming (minScore: 0.8) | 🔴 HIGH | 🔴 HIGH (unknown) | VPS |
| Fix fallback chain cross-provider (F31 + F42) | 🟡 MEDIUM | 🟡 UNKNOWN | VPS |
| Verify heartbeat execution (ask Heather in Discord) | 🟠 MEDIUM-HIGH | Unknown | Discord |
| Upgrade Alpaca to MCP Server v2 | N/A | 🟠 HIGH (when scope restored) | VPS |
| Audit skills (ClawHub) | ✅ Clean | 🟡 MEDIUM — gog-cli needs check | VPS |
| Deploy heartbeat cron to openclaw.json | 🟡 MEDIUM | Unknown | VPS |
| Fix contextPruning (Josh: 6h TTL) | 🟡 MEDIUM | N/A | VPS |
| Tighten Discord security | 🟡 MEDIUM | N/A | VPS |
| Set per-agent thinkingDefault (AlphaClaw UI) | 🟠 LOW | 🟠 LOW | UI only |
| Configure Remote MCP | 🟠 LOW | 🟠 HIGH (financial data) | UI + env |
| Enable Discord streaming ("progress") | 🟠 LOW (post 2026.6.9) | N/A | VPS |

---

## What Noah Has That Josh Doesn't
1. Google Workspace connected and fully authorized
2. Compaction + memoryFlush configured
3. Skills directory (gog-cli — needs ClawHub audit)
4. More recent OpenClaw version (2026.4.15 vs 2026.3.22)
5. More secure Discord policy (pairing + allowlist)
6. Anthropic-only model stack (no Gemini preview sunset risk — assuming Sonnet 4.6 is stable)

## What Josh Has That Noah Doesn't
1. All workspace files personalized and current
2. MEMORY.md seeded with long-term context
3. HEARTBEAT.md active with monitoring schedule and cron warning
4. BOOTSTRAP.md deleted (no wasted context tokens)
5. heartbeat-state.json created
6. Model fallbacks (2 fallbacks configured, though same-provider gap + preview risk)
7. Public AlphaClaw UI at known IP
8. Clean skills directory (no ClawHub exposure)

## Shared Gaps (Both Instances)
1. **BRAVE_API_KEY not set** — web search unavailable (F30 for Josh; equivalent gap assumed for Noah)
2. **`userTimezone` likely not set** — silent TZ drift risk for heartbeat/dreaming/cron schedules (Finding 28)
3. **Dreaming not configured** — no automated memory consolidation (minScore: 0.8)
4. **Not on 2026.6.9-stable** — upgrade window is OPEN, both need upgrading
5. **Remote MCP not configured** — new 0.9.18 capability; high value for both (especially Noah's financial data)
6. **Cross-provider fallback chain** — review needed for both (F31 confirmed for Josh; Noah's config unknown)
7. **F42 risk** — both should audit for Gemini preview models; Josh confirmed; Noah unknown (blind)

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
- *June 22 morning: F33–F37 (beta info, git sync fix, TOOLS.md cleaned up). Noah still blind (Day 11).*
- *June 23 evening: F38 (HEARTBEAT.md), F39 (Discord V2), F40 (group chat context), F41 (MEMORY.md counts). Noah still blind (Day 12).*
- *June 23 morning: F42 (Gemini preview sunset wave — 2-day warning). Noah still blind (Day 13).*
