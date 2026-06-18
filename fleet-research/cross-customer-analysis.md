# Cross-Customer Fleet Analysis — 2026-06-18 Morning

**Researcher:** AlphaClaw Fleet Agent
**Customers analyzed:** Josh (Heather Schwartz) · Noah (Market Catalyst Agent)
**Previous scan:** 2026-06-17 morning / 2026-06-18 evening
**Changes this scan:** 4 new findings (23-26). AlphaClaw 0.9.17/18 features documented. Dreaming config corrected. ClawHavoc audit flagged. Heartbeat block fully diagnosed.

---

## Fleet Headline: Josh Workspace Complete — Waiting on VPS. Noah Still Blind (Day 6+).

### Josh (Heather Schwartz)

All GitHub-manageable workspace files are current. The only remaining blockers are VPS-side:
- ✅ All workspace files personalized
- ✅ MEMORY.md seeded
- ✅ HEARTBEAT.md active schedule
- ✅ gemini-3.5-flash fallback in place
- ⛔ Google Workspace OAuth not connected (Day 88 — CRITICAL)
- ⛔ OpenClaw 2026.3.22 — 87+ days behind 2026.6.8 (HIGH)
- ⛔ Dreaming not enabled — use **corrected** config, minScore: 0.8 (HIGH)
- ⛔ No compaction/memoryFlush (HIGH)
- ⛔ Heartbeat cron never fired — memory_maintenance permanently null (MEDIUM)
- ⛔ Discord open to all (MEDIUM-HIGH)

**New this morning (Findings 23-26):**
- AlphaClaw 0.9.17/0.9.18 features available now: per-agent thinking control, Opus 4.8 in catalog, OpenAI proxy, Remote MCP support (new alternative path to Google Workspace via hosted MCP)
- Dreaming config correction: minScore must be 0.8 (not 0.7 as previously documented)
- ClawHavoc security warning: Josh has no installed skills — no risk
- Heartbeat diagnosis: all 5 checks blocked at root; memory_maintenance can fire after upgrade without Google/iMessage

### Noah (Market Catalyst Agent)

**Completely blind — session scope mismatch, Day 6+**

The session is scoped to `lylle-rgb/noah--repo`, which does not exist (GitHub 404). The actual Noah repos found in the account:
- `lylle-rgb/Noahrepo2` — last updated 2026-03-08
- `lylle-rgb/Noah-workspace` — last updated 2026-03-07

Neither is accessible in the current session scope. All previously documented Noah findings remain valid but cannot be verified or updated.

**Fleet operator action required (persistent — Day 6+):** Correct the Noah session scope to `Noahrepo2` or `Noah-workspace`.

**New AlphaClaw features relevant to Noah (applying when scope is restored):**
- **Remote MCP support (0.9.18)**: Noah could connect to hosted financial data MCP servers (SEC EDGAR, Alpaca streaming) via `REMOTE_MCP_URL` without local installation — high relevance for the catalyst hunter use case
- **Per-agent thinking control (0.9.17)**: Noah's Sonnet 4.6 could be set to deeper thinking for complex catalyst analysis and lighter thinking for status/health check crons
- **Cron SQLite fix (2026.6.8)**: Noah's trading bot cron relies on status tracking — this fix directly improves cron reliability
- **ClawHavoc audit**: Noah has `gog-cli` installed — needs verification against official ClawHub listing

---

## Version Status

| Instance | Current | Latest Stable | Gap | Critical Bugs Unpatched |
|---|---|---|---|
---|
| Josh / Heather | 2026.3.22 | 2026.6.8 | **88 days** | iMessage recovery, gateway wedge, relay leak, cron SQLite |
| Noah / Catalyst | 2026.4.15 (last known) | 2026.6.8 | ~60 days | MCP Anthropic 400s, extended-thinking recovery, cron SQLite |

---

## AlphaClaw Version Status

| Feature | Version | Relevant to Josh | Relevant to Noah |
|---|---|---|---|
| Per-agent thinking control | 0.9.17 (May 31) | ✅ Set thinkingDefault in UI | ✅ Tune Sonnet 4.6 for catalyst depth |
| Opus 4.8 in catalog | 0.9.17 (May 31) | Optional high-stakes override | Optional for complex analysis |
| OpenAI-compatible proxy | 0.9.18 (June 1) | ✅ Unlocks n8n/Zapier integrations | Lower relevance |
| Remote MCP support | 0.9.18 (June 1) | ✅ Alternative to Google OAuth | ✅ High value for financial MCP servers |

Both instances are running AlphaClaw 0.9.18 (current) and have access to all these features via the AlphaClaw Setup UI.

---

## Workspace File Status

| File | Josh | Noah | Notes |
|---|---|---|---|
| `SOUL.md` | ✅ Personalized (June 17) | Unknown (blind) | |
| `AGENTS.md` | ✅ Personalized (June 17) | Unknown (blind) | |
| `TOOLS.md` | ✅ Populated (June 17) | Unknown (blind) | |
| `USER.md` | ✅ Filled | Unknown (blind) | |
| `IDENTITY.md` | ✅ Heather | Unknown (blind) | |
| `HEARTBEAT.md` | ✅ Active schedule | Unknown (blind) | |
| `MEMORY.md` | ✅ Seeded | Unknown (blind) | |
| `BOOTSTRAP.md` | ✅ Deleted | Unknown (blind) | |
| `heartbeat-state.json` | ✅ Created — all nulls | Unknown (blind) | Nulls = services blocked, not missing file |
| `DREAMS.md` | ❌ Will auto-create when Dreaming enabled | Unknown (blind) | Do NOT load into context |

---

## Configuration Comparison

| Config area | Josh | Noah | Notes |
|---|---|---|---|
| Provider | Google/Gemini + OpenRouter fallback | Anthropic only (last known) | Josh has redundancy |
| Primary model | gemini-3-flash-preview | claude-sonnet-4-6 | Different stacks |
| Model fallbacks | ✅ 2 (gemini-3.5-flash, claude-3.5-haiku) | ❌ None (last known) | Josh safer |
| Compaction / memoryFlush | ❌ Not configured | ✅ Configured | Noah safer |
| contextPruning | ❌ Not configured | ⚠️ 5m TTL (default, acceptable) | Josh needs 6h TTL configured |
| Dreaming | ❌ Not configured | ❌ Not configured | Both need corrected config (minScore: 0.8) |
| Google Workspace | ❌ NOT connected | ✅ Connected (last known) | Critical Josh gap; Remote MCP now an option |
| Discord security | ⚠️ Open to all | ✅ Allowlist + pairing | Noah more secure |
| Discord streaming | ❌ Off | N/A | Josh: enable "progress" |
| Skills (ClawHub) | ✅ None installed — clean | ⚠️ gog-cli (needs ClawHavoc audit) | |
| SOUL/AGENTS/TOOLS | ✅ All personalized | Unknown (blind) | |
| MEMORY.md | ✅ Seeded | Unknown (blind) | |
| HEARTBEAT.md | ✅ Active | Unknown (blind) | |
| Heartbeat cron | ❌ Never fired | Unknown (blind) | Josh: memory_maintenance first |
| BOOTSTRAP.md | ✅ Deleted | Unknown (blind) | |
| Remote MCP | Available (0.9.18) | Available (0.9.18) | New — neither yet configured |

---

## Priority Action Matrix (Fleet View — June 18 Morning)

| Action | Josh | Noah | Effort |
|---|---|---|
---|
| **Correct Noah session scope** | N/A | 🔴 CRITICAL (blind, Day 6+) | Fleet op |
| Connect Google Workspace OAuth | 🔴 CRITICAL (Day 88) | N/A (done) | Josh manual |
| Upgrade to 2026.6.8 | 🔴 HIGH | 🔴 HIGH | VPS |
| Add compaction + memoryFlush | 🔴 HIGH | N/A (done) | VPS |
| Enable Dreaming (minScore: 0.8) | 🔴 HIGH | 🔴 HIGH (unknown) | VPS |
| Audit skills (ClawHavoc) | ✅ Clean | 🟡 MEDIUM — gog-cli needs check | VPS |
| Enable heartbeat memory_maintenance cron | 🟡 MEDIUM | Unknown | VPS |
| Fix contextPruning (Josh: 6h TTL) | 🟠 LOW | N/A (configured) | VPS |
| Tighten Discord security | 🟡 MEDIUM-HIGH | N/A | VPS |
| Set per-agent thinkingDefault (AlphaClaw UI) | 🟠 LOW | 🟠 LOW | UI only |
| Configure Remote MCP if managed MCP available | 🟠 LOW | 🟠 LOW (high value for financial data) | UI + env |
| Upgrade fallback 2 to Haiku 4.5 | 🟠 LOW (post 2026.6.8) | N/A | VPS |
| Enable Discord streaming ("progress") | 🟠 LOW | N/A | VPS |
| Add BRAVE_API_KEY | 🟠 LOW | 🟠 LOW | VPS |

---

## Key Open Items Requiring Human Action

### Fleet Operator (URGENT — persistent)
1. **Correct Noah session scope** — add `Noahrepo2` or `Noah-workspace` to session allow list. This has been open Day 6+. Every scan of Noah produces zero findings.

### Josh (manual, ~10 min)
2. **Connect Google Workspace OAuth** at `https://5.78.142.81.sslip.io#general` → Google Workspace tab → authorize Gmail, Calendar, Contacts
   - Guide: `workspace/memory/onboarding-google.md`
   - Alternative: ask AlphaClaw to configure `REMOTE_MCP_URL` pointing to a managed Google Workspace MCP service (no GCP OAuth needed)

### VPS actions
3. `openclaw update` (staged to 2026.6.8)
4. Add compaction/memoryFlush, Dreaming (minScore: 0.8 — corrected), contextPruning, heartbeat cron, Discord streaming, Discord allowlist to openclaw.json
5. Run `openclaw skill list` and audit against ClawHub (ClawHavoc check)
6. After 2026.6.8: upgrade fallback 2 to `claude-haiku-4-5`

---

## What Noah Has That Josh Doesn't
1. Google Workspace connected and fully authorized
2. Compaction + memoryFlush configured
3. Skills directory (gog-cli — needs ClawHavoc audit)
4. Reports output directory
5. More secure Discord policy (pairing + allowlist)
6. memory-core in plugin allowlist
7. More recent version (2026.4.15 vs 2026.3.22)

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
1. **Dreaming not configured** — no automated memory consolidation (use corrected config: minScore 0.8)
2. **Not upgraded to 2026.6.8** — both missing cron SQLite fix, state recovery, MCP coercion fix
3. **Cron bug #11726** — both should use isolated sessions + direct channel delivery (not `sessionTarget: "main"`)
4. **BRAVE_API_KEY not set** — native Brave web search unavailable
5. **Remote MCP not configured** — new capability in 0.9.18, high value for both instances

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
