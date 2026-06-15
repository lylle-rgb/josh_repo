# Cross-Customer Fleet Analysis — 2026-06-15 Morning Update

**Researcher:** AlphaClaw Fleet Agent
**Customers analyzed:** Josh (Heather Schwartz) · Noah (Market Catalyst Agent)
**Previous scan:** 2026-06-14 morning | 2026-06-15 evening (prior session)
**Changes since June 14:** Zero fixes applied on either instance. Day 11 stagnant.

---

## ⛔ T-2 DAYS — FINAL WARNING: gemini-2.5-flash Deprecates June 17

**This is the last scheduled scan before the deadline.**

Josh's first fallback (`openrouter/google/gemini-2.5-flash`) will stop responding on June 17. The fix has been open for 3 scans with zero action. After June 17, the dead endpoint will be silently skipped — the fallback chain degrades from 3 hops to 2.

```
File: openclaw.json → agents.defaults.model.fallbacks[0]
CHANGE: "openrouter/google/gemini-2.5-flash"
TO:     "openrouter/google/gemini-3.5-flash"
```

gemini-3.5-flash is GA on OpenRouter since May 19. It is cheaper ($0.10/M input vs gemini-2.5-flash) and has better reasoning benchmarks. This is a dead-endpoint swap, not an upgrade decision.

**30 seconds. GitHub file editor. Zero risk.**

---

## ⛔ NOAH FLEET BLIND — Repo Scope Mismatch (Unresolved)

The session is scoped to `lylle-rgb/noah--repo`, which does not exist (GitHub 404). Search found two candidate repos:
- `lylle-rgb/Noahrepo2` — last updated 2026-03-08
- `lylle-rgb/Noah-workspace` — last updated 2026-03-07

Neither is accessible in this session. Noah analysis has been completely blind for this entire session (and prior evening session). Zero Noah findings can be generated, updated, or committed.

**Action required (fleet operator):** Correct the session scope to the right Noah repo name. The Noah findings documented below (from June 12–14 scans) remain valid but cannot be updated until access is restored.

---

## New Morning Research Findings (June 15)

Three new findings applicable to both instances from web research this morning:

| Finding | Josh Impact | Noah Impact |
|---------|-------------|-------------|
| Discord streaming "progress" mode (v2026.5.3+) | HIGH — replaces "off" with "progress" | N/A — Noah not on Discord |
| Nylas CLI email alternative | HIGH — may unblock 85-day email outage | MEDIUM — backup if GWS breaks |
| NVIDIA SkillSpector (v2026.6.1+) | LOW — passive on upgrade | LOW — passive on upgrade |

See `findings.md` Finding 13–15 for full detail and config.

---

## June 15 Morning: Status — Nothing Changed

| Instance | GitHub-only fixes open | Applied since June 14 | Days stagnant |
|---------|----------------------|----------------------|--------------|
| Josh / Heather | 10+ | 0 | 11 |
| Noah / Catalyst | Unknown (blind) | Unknown | Unknown |

---

## June 15 Morning: Immediate Action List (Fleet Priority)

1. **[⛔ 30s] Josh: Fix gemini-2.5-flash → gemini-3.5-flash in openclaw.json** — Deadline June 17
2. **[Fleet op] Correct Noah session scope** — Noahrepo2 or Noah-workspace
3. **[5 min] Josh: Create workspace/MEMORY.md** — 85 days with no long-term memory
4. **[5 min] Josh: Populate workspace/HEARTBEAT.md** — 85 days no proactive monitoring
5. **[5 min] Josh: Add hard rules to workspace/SOUL.md** — No-emoji rule override
6. **[1 min] Josh: Delete workspace/BOOTSTRAP.md** — Wasting context on every startup
7. **[Noah] Fix contextPruning TTL 5m → 30m** — ~420 sessions truncated (Day 30+)
8. **[Noah] Create workspace/MEMORY.md + enable Dreaming** — No long-term memory
9. **[Noah] Populate USER.md and IDENTITY.md** — Agent has no user context
10. **[Both] Add BRAVE_API_KEY to environment** — Enables native web search on upgrade

---

# Cross-Customer Analysis — 2026-06-14 Morning Update (Archived)

**Researcher:** AlphaClaw Fleet Agent
**Customers analyzed:** Josh (Heather Schwartz) · Noah (Market Catalyst Agent)
**Previous scan:** 2026-06-13 morning
**Changes since June 13:** Zero fixes applied on either instance.

---

## ⛔ DEADLINE IN 3 DAYS — Josh: gemini-2.5-flash Deprecates June 17

**Deadline: June 17, 2026 — 3 days from now**

Josh's first fallback model (`openrouter/google/gemini-2.5-flash`) will stop responding when Google shuts down `gemini-2.5-flash` and `gemini-2.5-pro` on June 17. OpenRouter routes to the underlying Google API — the route dies when Google kills the model.

**GitHub file editor, 30 seconds:**
```json
// openclaw.json → agents.defaults.model.fallbacks
"openrouter/google/gemini-2.5-flash"  →  "openrouter/google/gemini-3.5-flash"
```

`gemini-3.5-flash` reached GA May 19 at Google I/O. Available on OpenRouter today. This is the highest-priority action across the entire fleet.

---

## ⛔ NOAH-99 DAY 30 | contextPruning TTL=5m — ~420 Sessions Truncated

**Status:** Day 30. Zero action.

```json
// openclaw.json → agents.defaults.contextPruning
"ttl": "5m"  →  "ttl": "30m"
```

---

## New June 14 Intelligence

### 1. Cron Bug #11726 Confirmed (Affects Both)

OpenClaw cron issue #11726 (confirmed closed with workaround): cron jobs with `sessionTarget: "main"` do **not** reliably wake the main agent. The correct pattern for any cron job is:
- Use direct Discord channel delivery (not `sessionTarget: "main"`)
- Add `wakeMode: "now"` explicitly to cron definitions

This affects both instances. When Josh or Noah set up cron automation, use isolated sessions with direct delivery, not main-session targeting.

### 2. Josh's Email/iMessage Outage Is 44–47 Days Old — Not 9

Analysis of `workspace/memory/inbox-state.json` timestamps shows:
- **iMessage paused:** ~April 27, 2026 (47 days ago as of June 13)
- **Email last checked:** ~April 30, 2026 (44 days ago as of June 13)

The earlier estimate of "9 days" was wrong. Heather has not checked Josh's email or iMessage in over 6 weeks. The Google Workspace disconnection gap is far larger than previously documented.

### 3. Noah's AE Target Companies Report Is 52 Days Stale

The only output report in Noah's repo (`workspace/reports/ae-target-companies-2026-04-22.md`) was generated April 22, 2026. The Market Catalyst Agent has not produced a single deliverable since its first scan.

### 4. OpenClaw Meeting Notes Feature (2026.5.26)

OpenClaw 2026.5.26 shipped a **Meeting Notes** feature: the agent can transcribe Discord voice calls in real time. Both instances could use this:
- **Josh:** Heather transcribes Josh's executive calls and business meetings
- **Noah:** Market Catalyst Agent records and summarizes trading thesis discussions

Requires upgrade to at least 2026.5.26. Josh is on 2026.3.22 (misses it). Noah is on 2026.4.15 (also misses it).

### 5. Latest Beta Is 2026.6.5-beta.6 (June 9)

Do not chase the beta. Upgrade target remains 2026.6.5 stable for both instances.

---

## June 14 Status vs June 13: Nothing Changed

| Instance | GitHub-only fixes open | Applied since June 13 | Days stagnant |
|---------|----------------------|----------------------|--------------|
| Josh / Heather | 8 | 0 | 1 |
| Noah / Catalyst | 12+ | 0 | 1 |

---

## Immediate Action List (Fleet Priority, GitHub-Only — June 14)

1. **Josh: Fix gemini-2.5-flash fallback → gemini-3.5-flash** (30s, ⛔ deadline June 17)
2. **Noah: Fix contextPruning TTL 5m → 30m** (30s, Day 30)
3. **Noah: Create workspace/memory/2026-06-14.md** (2min — establishes memory dir)
4. **Noah: Enable memory-core in plugins.entries with Dreaming config** (2min)
5. **Noah: Add fallback models to openclaw.json** (2min — requires OPENROUTER_API_KEY)
6. **Both: Create workspace/MEMORY.md** (5min each)
7. **Noah: Populate IDENTITY.md and USER.md** (10min)
8. **Both: Populate TOOLS.md** (5min each)
9. **Noah: Fix HEARTBEAT.md fenced code block** (5min)
10. **Noah: Add paper-trading-only guardrail to AGENTS.md** (5min)

---

# Cross-Customer Analysis — Fleet Scan 2026-06-12 (Archived)

**Researcher:** AlphaClaw Fleet Agent
**Customers analyzed:** Josh (Heather Schwartz) · Noah (Market Catalyst Agent)
**Previous scan:** 2026-06-11

---

## Version Status

| Instance | Current | Latest Stable | Gap | Critical Bugs Unpatched |
|---|---|---|---|---|
| Josh / Heather | 2026.3.22 | 2026.6.5 | **3 months** | iMessage recovery, parallel search, cron wipe |
| Noah / Catalyst | 2026.4.15 | 2026.6.5 | **2 months** | MCP Anthropic 400s, extended-thinking recovery |

---

## Workspace File Comparison

| File | Josh | Noah | Notes |
|---|---|---|---|
| `SOUL.md` | ✅ Present | ✅ Present | Same SHA — shared baseline |
| `AGENTS.md` | ✅ Present | ✅ Present | Same SHA — shared baseline |
| `TOOLS.md` (workspace) | ⚠️ Template only | ⚠️ Template only | **Neither customized** |
| `USER.md` | ✅ Filled | ❌ Blank template | Noah has no user context at all |
| `IDENTITY.md` | ✅ Present (Heather) | ❌ Blank template | Noah has no real identity |
| `HEARTBEAT.md` | ⚠️ Empty template | ⚠️ Empty template | **Both dormant** |
| `MEMORY.md` | ❌ Missing | ❌ Missing | **Neither has a long-term fact store** |
| `BOOTSTRAP.md` | ✅ Present (stale) | ✅ Present | Should be deleted on both |

---

## Configuration Comparison

| Config area | Josh | Noah | Winner |
|---|---|---|---|
| Provider | Google/Gemini + OpenRouter fallback | Anthropic only | Josh (has fallback) |
| Primary model | gemini-3-flash-preview | claude-sonnet-4-6 | Even |
| Model fallbacks | ✅ 2 fallbacks configured | ❌ None | **Josh** |
| Compaction / memoryFlush | ❌ Not configured | ✅ Configured | **Noah** |
| contextPruning | ❌ Not configured | ⚠️ 5m TTL (too aggressive) | Neither ideal |
| Google Workspace | ❌ Not connected | ✅ Connected (full r/w) | **Noah** |
| Discord streaming | ❌ Off | N/A | Josh should enable (use "progress" mode) |
| Discord group policy | ⚠️ Open (anyone) | ✅ Allowlist + pairing | **Noah** (more secure) |
| memory-core plugin | ❌ Not in config | ⚠️ Allowed but not in entries | Neither fully enabled |
| Dreaming | ❌ Not configured | ❌ Not configured | Neither |
| HEARTBEAT.md | ❌ Empty | ❌ Empty | Neither |
| MEMORY.md | ❌ Missing | ❌ Missing | Neither |
| Skills directory | ❌ None | ✅ gog-cli | **Noah** |
| Version | 2026.3.22 | 2026.4.15 | Noah (less outdated) |

---

## Use-Case-Specific Gaps

### Josh (Personal Assistant — Heather Schwartz)

**Critical gap: Google Workspace not connected.**
Heather is supposed to manage Josh's email, calendar, and contacts. Without a connected Google account, she can't do any of it. Noah's instance has full Google Workspace access. Josh has zero. Email and iMessage have been offline 85+ days.

**High gap: No memory protection, no long-term memory.**
No compaction/memoryFlush + no MEMORY.md + no Dreaming = no continuity. Personal assistant relationships are built on remembering. Every session reset destroys context. Noah has memoryFlush; Josh has none of these.

**Security gap: Discord open to all.**
Josh's Discord uses `groupPolicy: open` and `dmPolicy: open` with `allowFrom: ["*"]`. Anyone can DM Heather and get responses. Heather has access to Josh's calendar and contacts. Noah uses pairing + allowlist. This is a significant exposure risk.

**New (2026-06-12): HEARTBEAT.md empty + Dreaming not configured.**
Both of these mean proactive behavior is completely dormant despite being designed into AGENTS.md.

### Noah (Market Catalyst Agent)

**Critical gap: Active Anthropic bugs.**
MCP coercion (Anthropic 400s) and extended-thinking recovery bugs are both unpatched on 2026.4.15. Update to 2026.6.5 is the single highest-priority action.

**Medium gap: Blank user and identity files.**
Market Catalyst doesn't know who Noah is, their timezone (critical for market hours), risk tolerance, or trading focus. AE report is 52+ days stale.

**Medium gap: memory-core plugin not in entries.**
memory-core is in the plugin allow-list but not in entries — it may not be loading.

**New (2026-06-12): HEARTBEAT.md empty + Dreaming not configured.**
For a trading agent, a populated HEARTBEAT.md could implement market-hours-aware checks: pre-market catalyst scan, post-close summary, earnings calendar review. Dreaming would preserve catalyst signals and watchlist context long-term.

---

## Priority Action Matrix (Fleet View — Current)

| Action | Josh | Noah | Effort |
|---|---|---|---|
| Fix gemini-2.5-flash fallback (⛔ June 17) | 🔴 CRITICAL | N/A | 30s |
| Fix contextPruning TTL 5m→30m | N/A | 🔴 CRITICAL (Day 30+) | 30s |
| Correct Noah session scope | N/A | 🔴 CRITICAL (blind) | Fleet op |
| Update to 2026.6.5 | 🔴 HIGH | 🔴 HIGH | Low |
| Connect Google Workspace | 🔴 CRITICAL | N/A (done) | Medium |
| Add compaction + memoryFlush | 🔴 HIGH | N/A (done) | Low |
| Create MEMORY.md + Enable Dreaming | 🔴 HIGH | 🔴 HIGH | Low |
| Populate HEARTBEAT.md | 🟡 MEDIUM | 🟡 MEDIUM | Low (5 min) |
| Fill in USER.md / IDENTITY.md | N/A (done) | 🟡 MEDIUM | Low |
| Fix emoji contradiction in SOUL.md | 🟡 HIGH | N/A | Low (5 min) |
| Tighten Discord security | 🟡 MEDIUM | N/A (done) | Low |
| Enable memory-core in entries | N/A | 🟡 MEDIUM | Low |
| Customize TOOLS.md | 🟠 LOW | 🟠 LOW | Low |
| Enable Discord streaming ("progress" mode) | 🟠 LOW | N/A | Low |
| Add BRAVE_API_KEY | 🟠 LOW | 🟠 LOW | Low |

---

## What Noah Has That Josh Doesn't
1. Google Workspace connected and fully authorized (though unused for 52+ days)
2. Compaction + memoryFlush configured
3. Skills directory (gog-cli installed)
4. Reports output directory
5. More secure Discord policy (pairing + allowlist)
6. memory-core in plugin allowlist
7. More recent version (2026.4.15 vs 2026.3.22)

## What Josh Has That Noah Doesn't
1. USER.md filled in (user context actually exists)
2. Model fallbacks (OpenRouter fallbacks — fix the gemini-2.5-flash one by June 17)
3. Public AlphaClaw UI accessible at real IP

## Shared Gaps (Both Instances)
1. **MEMORY.md missing** — neither has a durable long-term fact store
2. **Dreaming not configured** — automated memory consolidation not running
3. **HEARTBEAT.md empty** — proactive polling completely dormant on both
4. **TOOLS.md blank** — neither has environment-specific notes filled in
5. **Not updated to 2026.6.5** — both missing Meeting Notes, Parallel search, Anthropic recovery fixes
6. **Cron bug #11726** — both should use isolated sessions + direct Discord delivery
7. **BOOTSTRAP.md stale** — wasting context tokens on every session startup

---

*June 12 scan completed: 2026-06-12 morning.*
*June 13 update appended: 2026-06-13 morning.*
*June 14 update appended: 2026-06-14 morning.*
*June 15 morning update: 2026-06-15 morning. Noah blind. Gemini deadline T-2.*
