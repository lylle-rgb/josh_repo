# Cross-Customer Analysis — Fleet Scan 2026-06-11

**Researcher:** AlphaClaw Fleet Agent  
**Customers analyzed:** Josh (Heather Schwartz) · Noah (Market Catalyst Agent)

---

## Version Status

| Instance | Current | Latest Stable | Gap | Critical Bugs Unpatched |
|---|---|---|---|---|
| Josh / Heather | 2026.3.22 | 2026.6.5 | **3 months** | iMessage recovery, parallel search, cron wipe |
| Noah / Catalyst | 2026.4.15 | 2026.6.5 | **2 months** | MCP Anthropic 400s, extended-thinking recovery |

Both instances need updates. Noah's bugs are higher-severity (silent session corruption on Anthropic). Josh's bugs are more operational (iMessage failures, search gaps).

---

## Workspace File Comparison

| File | Josh | Noah | Notes |
|---|---|---|---|
| `SOUL.md` | ✅ Identical | ✅ Identical | Same SHA — shared baseline |
| `AGENTS.md` (bootstrap) | ✅ Identical | ✅ Identical | Same SHA — shared baseline |
| `TOOLS.md` (workspace) | ⚠️ Template only | ⚠️ Template only | Same SHA — **neither customized** |
| `TOOLS.md` (bootstrap) | ✅ Filled (AlphaClaw UI) | ✅ Filled (localhost UI) | Different — Noah has Google account listed |
| `USER.md` | ✅ Filled | ❌ Blank template | Josh’s is solid; Noah’s has no user context |
| `IDENTITY.md` | ❌ Missing from workspace | ❌ Blank template | Neither instance has a real identity configured |
| `HEARTBEAT.md` | ✅ Present | ✅ Present | Both have heartbeat |
| `BOOTSTRAP.md` | ✅ Present | ✅ Present | Both have bootstrap docs |
| `MEMORY.md` | ❌ Missing | ❌ Missing | **Neither instance has a MEMORY.md** |

### Gap: No MEMORY.md in either repo

Best practice (per OpenClaw docs and community) is to maintain a `MEMORY.md` at the workspace root — a durable file that survives compaction and holds facts that should never be forgotten. SOUL.md covers personality; AGENTS.md covers behavior rules; MEMORY.md covers persistent facts about the user and world.

Neither instance has one. This means both agents rely entirely on session memory and whatever gets written to other files — there’s no dedicated long-term fact store.

**Recommendation:** Create `workspace/memory/MEMORY.md` (or `workspace/MEMORY.md`) in both repos with a structured template covering:
- Key facts about the user
- Ongoing projects and status
- Preferences discovered over time
- Known constraints and hard rules

---

## Configuration Comparison

| Config area | Josh | Noah | Winner |
|---|---|---|---|
| Provider | Google/Gemini + OpenRouter fallback | Anthropic only | Josh (has fallback) |
| Primary model | gemini-3-flash-preview | claude-sonnet-4-6 | Even (different use cases) |
| Model fallbacks | ✅ 2 fallbacks configured | ❌ None | **Josh** |
| Compaction / memoryFlush | ❌ Not configured | ✅ Configured | **Noah** |
| contextPruning | ❌ Not configured | ⚠️ 5m TTL (may be too aggressive) | Neither ideal |
| Google Workspace | ❌ Not connected | ✅ Connected (full r/w) | **Noah** |
| Discord streaming | ❌ Off | N/A (no setting) | Josh should enable |
| Discord group policy | ⚠️ Open (anyone can DM) | ✅ Allowlist + pairing | **Noah** (more secure) |
| memory-core plugin | ❌ Not in config | ⚠️ Allowed but not in entries | Neither fully enabled |
| Skills directory | ❌ None | ✅ gog-cli | **Noah** |
| Reports directory | ❌ None | ✅ Has output reports | **Noah** |

---

## Use-Case-Specific Gaps

### Josh (Personal Assistant — Heather Schwartz)

**Critical gap: Google Workspace not connected.**  
This is the single most important fix. Heather is supposed to manage Josh's email, calendar, and contacts. Without a connected Google account, she can't do any of it. Noah's instance has `Ngkatz.ai@gmail.com` with full read/write on all Google services. Josh has zero.

**Secondary gap: No memory protection.**  
Personal assistant relationships are built on continuity. Every session compaction with no memoryFlush means Heather loses what Josh told her. Noah has this configured. Josh doesn't.

**Discord security gap:**  
Josh's Discord is set to `"groupPolicy": "open"` and `"dmPolicy": "open"` with `"allowFrom": ["*"]`. This means literally anyone can DM Heather and get responses. Noah's instance uses `pairing` and `allowlist`. For a personal assistant with access to Josh's calendar and contacts, this is a significant exposure risk.

### Noah (Market Catalyst Agent)

**Critical gap: Blank user and identity files.**  
Market Catalyst doesn't know who Noah is, their timezone (critical for market hours), risk tolerance, or trading focus. Every session starts from scratch. Josh’s USER.md is detailed and useful. Noah’s is an empty template.

**Active bug risk: MCP coercion + extended thinking.**  
Noah is on Anthropic and 2 months behind on a fix that directly prevents silent session corruption on Anthropic providers. This is a production risk for any live or paper trading activity.

**Cron state wipe:**  
Any scheduled catalyst scans set up before the SQLite migration (somewhere around 2026.4.x–2026.5.x) may have been silently wiped. Worth auditing whether any cron jobs are actually firing.

---

## Priority Action Matrix (Fleet View)

| Action | Josh | Noah | Effort |
|---|---|---|---|
| Update to 2026.6.5 | 🔴 HIGH | 🔴 HIGH | Low |
| Connect Google Workspace | 🔴 CRITICAL | N/A (done) | Medium |
| Add compaction + memoryFlush | 🔴 HIGH | N/A (done) | Low |
| Fill in USER.md | N/A (done) | 🟡 MEDIUM | Low |
| Create MEMORY.md | 🟡 MEDIUM | 🟡 MEDIUM | Low |
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

## What Josh Has That Noah Doesn't
1. USER.md filled in (user context actually exists)
2. Model fallbacks (OpenRouter fallbacks provide resilience)
3. Public AlphaClaw UI (accessible at real IP, not just localhost)

---

## Shared Recommendations (Apply to Both)

1. **Update to 2026.6.5** — single most important action. One command, zero config changes required.
2. **Create workspace/MEMORY.md** — durable fact file that survives compaction. Neither instance has one.
3. **Populate workspace/TOOLS.md** — both instances have the blank template; neither has any actual environment notes.

---

*Scan completed: 2026-06-11. Next recommended scan: 2026-06-18.*
