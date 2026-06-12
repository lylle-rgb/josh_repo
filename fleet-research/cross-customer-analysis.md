# Cross-Customer Analysis — Fleet Scan 2026-06-12

**Researcher:** AlphaClaw Fleet Agent  
**Customers analyzed:** Josh (Heather Schwartz) · Noah (Market Catalyst Agent)  
**Previous scan:** 2026-06-11

---

## Version Status

| Instance | Current | Latest Stable | Gap | Critical Bugs Unpatched |
|---|---|---|---|---|
| Josh / Heather | 2026.3.22 | 2026.6.5 | **3 months** | iMessage recovery, parallel search, cron wipe |
| Noah / Catalyst | 2026.4.15 | 2026.6.5 | **2 months** | MCP Anthropic 400s, extended-thinking recovery |

Both instances still need updates. Neither updated since the June 11 scan. Noah's bugs are higher-severity (silent session corruption on Anthropic). Josh's bugs are more operational.

---

## Workspace File Comparison

| File | Josh | Noah | Notes |
|---|---|---|---|
| `SOUL.md` | ✅ Identical | ✅ Identical | Same SHA — shared baseline |
| `AGENTS.md` | ✅ Identical | ✅ Identical | Same SHA — shared baseline |
| `TOOLS.md` (workspace) | ⚠️ Template only | ⚠️ Template only | **Neither customized** |
| `USER.md` | ✅ Filled | ❌ Blank template | Noah has no user context at all |
| `IDENTITY.md` | ❌ Not present | ❌ Blank template | Neither has real identity configured |
| `HEARTBEAT.md` | ⚠️ Empty (template) | ⚠️ Empty (template) | **Both dormant — no proactive checks running** |
| `MEMORY.md` | ❌ Missing | ❌ Missing | **Neither has a long-term fact store** |
| `BOOTSTRAP.md` | ✅ Present | ✅ Present | Both have bootstrap docs |

### NEW Gap Identified 2026-06-12: HEARTBEAT.md Not Populated in Either Instance

Both instances have the HEARTBEAT.md template file but no actual check tasks defined. This means both agents reply `HEARTBEAT_OK` on every heartbeat poll without doing any proactive work. For Josh, this means no email/calendar/weather monitoring. For Noah, it means no market-cycle-aware checks.

This is a 5-minute fix in each repo with high impact for both use cases.

### Ongoing Gap: No MEMORY.md in Either Repo

Neither instance has a `MEMORY.md`. Best practice per OpenClaw docs is to maintain a durable, compaction-safe long-term fact file. SOUL.md covers personality; AGENTS.md covers behavior rules; MEMORY.md covers persistent facts about the user and world.

### NEW Gap Identified 2026-06-12: Dreaming Not Configured in Either Instance

OpenClaw's optional background memory consolidation ("Dreaming") is not configured in either instance. This feature automates the MEMORY.md maintenance that both AGENTS.md files describe as a manual heartbeat task. It runs nightly, scores daily memory entries for significance, and promotes only high-signal items — keeping MEMORY.md curated without spending active session tokens.

Both instances would benefit from enabling this, but the configuration and memory structure differ by use case (see customer-specific sections below).

---

## Configuration Comparison

| Config area | Josh | Noah | Winner |
|---|---|---|---|
| Provider | Google/Gemini + OpenRouter fallback | Anthropic only | Josh (has fallback) |
| Primary model | gemini-3-flash-preview | claude-sonnet-4-6 | Even (different use cases) |
| Model fallbacks | ✅ 2 fallbacks configured | ❌ None | **Josh** |
| Compaction / memoryFlush | ❌ Not configured | ✅ Configured | **Noah** |
| contextPruning | ❌ Not configured | ⚠️ 5m TTL (too aggressive) | Neither ideal |
| Google Workspace | ❌ Not connected | ✅ Connected (full r/w) | **Noah** |
| Discord streaming | ❌ Off | N/A | Josh should enable |
| Discord group policy | ⚠️ Open (anyone) | ✅ Allowlist + pairing | **Noah** (more secure) |
| memory-core plugin | ❌ Not in config | ⚠️ Allowed but not in entries | Neither fully enabled |
| Dreaming | ❌ Not configured | ❌ Not configured | Neither |
| HEARTBEAT.md | ❌ Empty | ❌ Empty | Neither |
| MEMORY.md | ❌ Missing | ❌ Missing | Neither |
| Skills directory | ❌ None | ✅ gog-cli | **Noah** |
| Reports directory | ❌ None | ✅ Has output reports | **Noah** |
| Version | 2026.3.22 | 2026.4.15 | Noah (less outdated) |

---

## Use-Case-Specific Gaps

### Josh (Personal Assistant — Heather Schwartz)

**Critical gap: Google Workspace not connected.**  
Heather is supposed to manage Josh's email, calendar, and contacts. Without a connected Google account, she can't do any of it. Noah's instance has full Google Workspace access. Josh has zero.

**High gap: No memory protection, no long-term memory.**  
No compaction/memoryFlush + no MEMORY.md + no Dreaming = no continuity. Personal assistant relationships are built on remembering. Every session reset destroys context. Noah has memoryFlush; Josh has none of these.

**Security gap: Discord open to all.**  
Josh's Discord uses `groupPolicy: open` and `dmPolicy: open` with `allowFrom: ["*"]`. Anyone can DM Heather and get responses. Heather has access to Josh's calendar and contacts. Noah uses pairing + allowlist. This is a significant exposure risk that should be addressed alongside the Google Workspace setup.

**New (2026-06-12): HEARTBEAT.md empty + Dreaming not configured.**  
Both of these mean proactive behavior is completely dormant despite being designed into AGENTS.md.

### Noah (Market Catalyst Agent)

**Critical gap: Active Anthropic bugs.**  
MCP coercion (Anthropic 400s) and extended-thinking recovery bugs are both unpatched on 2026.4.15. These cause silent session corruption during analysis runs. Update to 2026.6.5 is the single highest-priority action.

**Medium gap: Blank user and identity files.**  
Market Catalyst doesn't know who Noah is, their timezone (critical for market hours), risk tolerance, or trading focus. Josh's USER.md is detailed and useful. Noah's is an empty template.

**Medium gap: memory-core plugin not in entries.**  
memory-core is in the plugin allow-list but not in entries — it may not be loading at all.

**New (2026-06-12): HEARTBEAT.md empty + Dreaming not configured.**  
For a trading agent, a populated HEARTBEAT.md could implement market-hours-aware checks: pre-market catalyst scan, post-close summary, earnings calendar review. Dreaming would preserve catalyst signals and watchlist context long-term.

---

## Priority Action Matrix (Fleet View)

| Action | Josh | Noah | Effort |
|---|---|---|---|
| Update to 2026.6.5 | 🔴 HIGH | 🔴 HIGH | Low |
| Connect Google Workspace | 🔴 CRITICAL | N/A (done) | Medium |
| Add compaction + memoryFlush | 🔴 HIGH | N/A (done) | Low |
| Create MEMORY.md + Enable Dreaming | 🔴 HIGH | 🔴 HIGH | Low |
| Populate HEARTBEAT.md | 🟡 MEDIUM | 🟡 MEDIUM | Low (5 min) |
| Fill in USER.md | N/A (done) | 🟡 MEDIUM | Low |
| Tighten Discord security | 🟡 MEDIUM | N/A (done) | Low |
| Enable memory-core in entries | N/A | 🟡 MEDIUM | Low |
| Customize TOOLS.md | 🟠 LOW | 🟠 LOW | Low |
| Increase contextPruning TTL | N/A | 🟠 LOW | Low |
| Enable Discord streaming | 🟠 LOW | N/A | Low |

---

## What Noah Has That Josh Doesn't
1. Google Workspace connected and fully authorized
2. Compaction + memoryFlush configured
3. Skills directory (gog-cli installed)
4. Reports output directory
5. More secure Discord policy (pairing + allowlist)
6. memory-core in plugin allowlist
7. More recent version (2026.4.15 vs 2026.3.22)

## What Josh Has That Noah Doesn't
1. USER.md filled in (user context actually exists)
2. Model fallbacks (OpenRouter fallbacks provide resilience)
3. Public AlphaClaw UI accessible at real IP

## Shared Gaps (Both Instances)
1. **MEMORY.md missing** — neither has a durable long-term fact store
2. **Dreaming not configured** — automated memory consolidation not running on either instance
3. **HEARTBEAT.md empty** — proactive polling behavior completely dormant on both
4. **TOOLS.md blank** — neither has environment-specific notes filled in
5. **Not updated to 2026.6.5** — both are running older versions with known bugs

---

*Scan completed: 2026-06-12 (morning). Next recommended scan: 2026-06-13 morning.*
