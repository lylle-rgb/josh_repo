# Cross-Customer Fleet Analysis — 2026-06-17 Morning Update

**Researcher:** AlphaClaw Fleet Agent
**Customers analyzed:** Josh (Heather Schwartz) · Noah (Market Catalyst Agent)
**Previous scan:** 2026-06-16 morning
**Changes since last scan:** Josh workspace fully personalized. 2026.6.8 now stable. Noah still blind.

---

## Fleet Headline: Josh Workspace Complete, OpenClaw 2026.6.8 Stable

### Josh (Heather Schwartz)

All GitHub-manageable workspace files are now current:
- ✅ SOUL.md — personalized
- ✅ AGENTS.md — personalized (emoji override at top)
- ✅ TOOLS.md — populated with AlphaClaw UI, Discord, iMessage, models
- ✅ HEARTBEAT.md — active monitoring schedule
- ✅ MEMORY.md — seeded with Josh context
- ✅ BOOTSTRAP.md — deleted
- ✅ heartbeat-state.json — created
- ✅ gemini-3.5-flash fallback — in place

Remaining gaps all require **VPS access** or **Josh's manual action**:
1. Connect Google Workspace OAuth (`https://5.78.142.81.sslip.io#general`) — CRITICAL
2. Upgrade OpenClaw to 2026.6.8 — HIGH
3. Add compaction/memoryFlush + Dreaming to openclaw.json — HIGH
4. Tighten Discord security (open→allowlist) — MEDIUM-HIGH

### Noah (Market Catalyst Agent)

**Completely blind — session scope mismatch, Day 5+**

The session is scoped to `lylle-rgb/noah--repo`, which does not exist (GitHub 404). The actual Noah repos found are:
- `lylle-rgb/Noahrepo2` — last updated 2026-03-08
- `lylle-rgb/Noah-workspace` — last updated 2026-03-07

Neither is in scope. All previously documented Noah findings remain valid but cannot be verified or updated until scope is corrected.

**[Fleet operator action required]:** Set the correct Noah repo in session scope. Use `Noahrepo2` or `Noah-workspace` — one of them is the live instance.

---

## 2026.6.8 Now Stable — What This Means for Both Instances

OpenClaw 2026.6.8 graduated from beta to stable on June 16–17. The new upgrade target for both instances is **2026.6.8** (not 2026.6.6).

| Instance | Current | New Target | Key unlock |
|---|---|---|---|
| Josh / Heather | 2026.3.22 | 2026.6.8 | iMessage fixes, gateway stability, Haiku 4.5 |
| Noah / Catalyst | 2026.4.15 (last known) | 2026.6.8 | MCP Anthropic 400 fix, extended-thinking recovery |

**Key 2026.6.8 fixes for Noah (from archived findings):**
- MCP tool result coercion (Anthropic 400s) — affects SEC filing reads via MCP tools
- Extended-thinking recovery — affects complex multi-step catalyst analysis
- Memory/state recovery after compaction
- Safer model routing (Noah runs claude-sonnet-4-6 as primary)

---

## Version Status

| Instance | Current | Latest Stable | Gap | Critical Bugs Unpatched |
|---|---|---|---|---|
| Josh / Heather | 2026.3.22 | 2026.6.8 | **87 days** | iMessage recovery, gateway wedge, relay leak |
| Noah / Catalyst | 2026.4.15 (last known) | 2026.6.8 | ~60 days | MCP Anthropic 400s, extended-thinking recovery |

---

## Workspace File Status (Updated June 17)

| File | Josh | Noah | Notes |
|---|---|---|---|
| `SOUL.md` | ✅ Personalized (June 17) | Unknown (blind) | Josh: Josh's hard rules added |
| `AGENTS.md` | ✅ Personalized (June 17) | Unknown (blind) | Josh: emoji suspension at top |
| `TOOLS.md` (workspace) | ✅ Populated (June 17) | Unknown (blind) | Josh: AlphaClaw UI, models, Discord, iMessage |
| `USER.md` | ✅ Filled (Josh context) | Unknown (blind) | |
| `IDENTITY.md` | ✅ Heather | Unknown (blind) | |
| `HEARTBEAT.md` | ✅ Active schedule (June 16) | Unknown (blind) | |
| `MEMORY.md` | ✅ Seeded (June 16) | Unknown (blind) | |
| `BOOTSTRAP.md` | ✅ Deleted (June 17) | Unknown (blind) | |
| `heartbeat-state.json` | ✅ Created (June 17) | Unknown (blind) | |

---

## Configuration Comparison (Updated June 17)

| Config area | Josh | Noah | Notes |
|---|---|---|---|
| Provider | Google/Gemini + OpenRouter fallback | Anthropic only (last known) | Josh has redundancy |
| Primary model | gemini-3-flash-preview | claude-sonnet-4-6 | Different stacks |
| Model fallbacks | ✅ 2 (gemini-3.5-flash, claude-3.5-haiku) | ❌ None (last known) | Josh safer |
| Compaction / memoryFlush | ❌ Not configured | ✅ Configured | Noah safer |
| contextPruning | ❌ Not configured | ⚠️ 5m TTL (too aggressive) | Neither ideal |
| Dreaming | ❌ Not configured | ❌ Not configured | Both need this |
| Google Workspace | ❌ NOT connected | ✅ Connected (last known) | Critical Josh gap |
| Discord security | ⚠️ Open to all (MEDIUM-HIGH risk) | ✅ Allowlist + pairing | Noah more secure |
| Discord streaming | ❌ Off | N/A | Josh: enable "progress" |
| Skills directory | ❌ None | ✅ gog-cli (last known) | |
| SOUL/AGENTS/TOOLS | ✅ All personalized (June 17) | Unknown (blind) | Josh complete |
| MEMORY.md | ✅ Seeded (June 16) | Unknown (blind) | |
| HEARTBEAT.md | ✅ Active (June 16) | Unknown (blind) | |
| BOOTSTRAP.md | ✅ Deleted (June 17) | Unknown (blind) | |

---

## Priority Action Matrix (Fleet View — June 17 Morning)

| Action | Josh | Noah | Effort |
|---|---|---|---|
| ~~Fix gemini-2.5-flash~~ | ✅ DONE | N/A | ✅ |
| ~~Create MEMORY.md~~ | ✅ DONE | Unknown | Josh done |
| ~~Populate HEARTBEAT.md~~ | ✅ DONE | Unknown | Josh done |
| ~~Personalize SOUL.md~~ | ✅ DONE | Unknown | Josh done |
| ~~Personalize AGENTS.md~~ | ✅ DONE | Unknown | Josh done |
| ~~Populate TOOLS.md~~ | ✅ DONE | Unknown | Josh done |
| ~~Delete BOOTSTRAP.md~~ | ✅ DONE | Unknown | Josh done |
| **Correct Noah session scope** | N/A | 🔴 CRITICAL (blind) | Fleet op |
| Connect Google Workspace OAuth | 🔴 CRITICAL | N/A (done) | Josh manual |
| Upgrade to 2026.6.8 | 🔴 HIGH | 🔴 HIGH | VPS |
| Add compaction + memoryFlush | 🔴 HIGH | N/A (done) | VPS |
| Enable Dreaming | 🔴 HIGH | 🔴 HIGH (unknown) | VPS |
| Fix contextPruning TTL (Noah) | N/A | 🔴 HIGH (Day 60+) | VPS |
| Tighten Discord security | 🟡 MEDIUM-HIGH | N/A | VPS |
| Create MEMORY.md (Noah) | N/A | 🟡 MEDIUM (unknown) | GitHub |
| Enable memory-core in entries (Noah) | N/A | 🟡 MEDIUM | VPS |
| Populate HEARTBEAT.md (Noah) | N/A | 🟡 MEDIUM (unknown) | GitHub |
| Upgrade fallback 2 to Haiku 4.5 | 🟠 LOW (post 2026.6.8) | N/A | VPS |
| Enable Discord streaming ("progress") | 🟠 LOW | N/A | VPS |
| Add BRAVE_API_KEY | 🟠 LOW | 🟠 LOW | VPS |

---

## Key Open Items Requiring Human Action

### Fleet Operator
1. **Correct Noah session scope** — add `Noahrepo2` or `Noah-workspace` to allow list

### Josh (manual, ~10 min)
2. **Connect Google Workspace OAuth** — `https://5.78.142.81.sslip.io#general` → Google Workspace tab → authorize Gmail, Calendar, Contacts
   - Josh: guides in `workspace/memory/onboarding-google.md`

### VPS actions (can be done by fleet operator with VPS access)
3. `openclaw update` (staged to 2026.6.8)
4. Add to `openclaw.json`: compaction/memoryFlush, Dreaming, contextPruning, Discord streaming, Discord allowlist
5. After 2026.6.8: upgrade fallback 2 to `claude-haiku-4-5`

---

## What Noah Has That Josh Doesn't (From Archived Findings)
1. Google Workspace connected and fully authorized
2. Compaction + memoryFlush configured
3. Skills directory (gog-cli installed)
4. Reports output directory
5. More secure Discord policy (pairing + allowlist)
6. memory-core in plugin allowlist
7. More recent version (2026.4.15 vs 2026.3.22)

## What Josh Has That Noah Doesn't (Updated June 17)
1. All workspace files personalized and current
2. MEMORY.md seeded with long-term context
3. HEARTBEAT.md active with monitoring schedule
4. BOOTSTRAP.md deleted (not burning context tokens)
5. heartbeat-state.json in place
6. Model fallbacks (2 fallbacks configured)
7. Public AlphaClaw UI at known IP

## Shared Gaps (Both Instances)
1. **Dreaming not configured** — no automated memory consolidation
2. **Not upgraded to 2026.6.8** — both missing significant stability and iMessage fixes
3. **Cron bug #11726** — both should use isolated sessions + direct channel delivery, not `sessionTarget: "main"`
4. **BRAVE_API_KEY not set** — native Brave web search unavailable on upgrade

---

*Scan history:*
- *June 12: Initial cross-customer analysis*
- *June 13: Nothing changed*
- *June 14: Nothing changed. Gemini deadline T-3.*
- *June 15 morning: Noah blind. Gemini T-2.*
- *June 16 evening: Gemini T-1. Zero fixes.*
- *June 16 morning: 3 critical fixes applied (gemini, MEMORY.md, HEARTBEAT.md). Noah still blind.*
- *June 17 morning: Josh workspace fully personalized. 2026.6.8 now stable. Noah still blind.*
