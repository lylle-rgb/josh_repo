# Cross-Customer Analysis — AlphaClaw Apex Fleet

**Last Updated:** 2026-05-29 Morning (Day 41)
**Instances:** Josh (Heather Schwartz, personal assistant) | Noah (Market Catalyst Agent, stock research)
**Scan cadence:** Morning + Evening daily

---

## Day 41 Morning — New Research (2026-05-29)

### Overnight Release Check — 2026.5.27 Still Latest

No new OpenClaw release overnight. 2026.5.27 is still current stable. Latest pre-release remains `2026.5.27-beta.1`.

| Instance | Current | Upgrade Target | Days Behind |
|----------|---------|----------------|-------------|
| Josh | 2026.3.22 | 2026.5.27 | **69 days** |
| Noah | 2026.4.15 | 2026.5.27 | **44 days** |

---

### gog-cli Confirmed: Noah Has Full Google Workspace Access — Unused for 44 Days

Deep investigation of `skills/gog-cli/SKILL.md` and `gogcli/state.json` this morning resolves NOAH-76 (evening: "purpose unknown"). This is a major capability revelation for Noah.

**gog-cli is a Google Workspace CLI** authenticated to `Ngkatz.ai@gmail.com` with full read/write access:
- Gmail (search, send, reply, labels, drafts, filters)
- Calendar (events, create, RSVP, conflict detection, freebusy)
- Drive (upload, download, organize, share)
- Sheets (read, write, append, export)
- Docs (read, write, export, sed-style edit)
- Tasks (list, create, complete)
- Contacts (list, search, create)
- Meet (list spaces)

**Critical gaps exposed:**
1. **gmailWatch.enabled: false** — real-time Gmail push is OFF. No email events reach the agent.
2. **TOOLS.md is blank** — the agent has no knowledge of gog-cli's existence.
3. **AGENTS.md has no Google Workspace instructions** — the agent never uses gog-cli.
4. **HEARTBEAT.md is structurally broken** — so polling Gmail during heartbeats doesn't happen either.

**Trading relevance for Noah:**
- Gmail → EDGAR 8-K alerts, broker research, earnings preannouncements
- Calendar → PDUFA dates, earnings dates, lock-up expirations, FOMC meetings
- Sheets → Portfolio tracking, P&L log, catalyst scoring model
- Drive/Docs → Trade thesis archive, research report storage
- Tasks → Catalyst follow-up queue, watchlist management

**Josh comparison:** Josh's agent uses iMessage/calendar/email natively via OpenClaw's built-in channel tools (not gog-cli). Josh's Google-equivalent is already operational. Noah has equivalent capability sitting dormant via gog-cli.

---

### Active Memory Plugin — Eligibility Diverges

OpenClaw's Active Memory plugin (requires ≥ 2026.4.10) is available to Noah but not Josh:

| Instance | Version | Active Memory Eligible? |
|----------|---------|------------------------|
| Josh | 2026.3.22 | ❌ Needs upgrade first |
| Noah | 2026.4.15 | ✅ Can enable now |

For Josh, Active Memory becomes available once upgraded to 2026.5.27.

For Noah, Active Memory is high-value now — but only after MEMORY.md is created and memory-core is enabled. The three steps should be applied together:
1. `memory-core` in plugins.entries (enable the store)
2. `workspace/MEMORY.md` (give it something to work with)
3. `active-memory` plugin config (auto-load relevant context per session)

See Noah's morning findings (findings-2026-05-29-morning.md) for exact configs.

---

### Gemini 3.5 Flash on OpenRouter — Josh Fallback Chain Update

Google released **Gemini 3.5 Flash** on OpenRouter on May 19, 2026. It is newer and more capable than Gemini 3.1 Flash Lite (released May 7).

This is Josh-specific (Noah uses Anthropic exclusively and has no OpenRouter fallbacks).

**Updated Josh fallback chain:**
```json
"fallbacks": [
  "openrouter/google/gemini-3-5-flash",
  "openrouter/google/gemini-3-1-flash-lite-preview",
  "openrouter/google/gemini-2.5-flash"
]
```

This supersedes the Day 40 recommendation (which used Gemini 3.1 Flash Lite as primary fallback). Gemini 3.5 Flash should now lead the fallback chain.

---

### memory-lancedb-pro — Both Instances, But Noah Priority

Community plugin `memory-lancedb-pro` (CortexReach/memory-lancedb-pro) offers enhanced memory over built-in memory-core:
- Hybrid Retrieval (Vector + BM25)
- Cross-Encoder Reranking
- Multi-Scope Isolation

For Noah: higher priority due to ticker/filing exact-match requirements (BM25 handles "NVDA 8-K" better than vector alone). Noah's Day 40-recommended `textWeight: 0.5` can be expressed natively if using this plugin.

For Josh: useful eventually, but lower priority than getting basic memory working at all.

---

## Day 41 Fleet Comparison (Full)

| Dimension | Josh / Heather | Noah / Market Catalyst |
|---|---|---|
| OpenClaw version | 2026.3.22 | 2026.4.15 |
| Latest stable | **2026.5.27** | **2026.5.27** |
| Gap (days behind) | **69 days** | **44 days** |
| compaction config | 🔴 **MISSING — Day 41** | ✅ Configured (threshold too low) |
| contextPruning TTL | ❌ Not set | 🔴 **5m — DAY 15 CRITICAL** |
| softThresholdTokens | N/A | ⚠️ 4000 (rec: 10000) |
| memory-core | ❌ Version-gated (< 2026.4.10) | 🔴 Allowlisted, **no entries — Day 31** |
| Active Memory plugin | ❌ Version-gated (< 2026.4.10) | 🟢 **Eligible now — not configured** |
| MEMORY.md | ❌ **Day 42** | ❌ **Day 42** |
| HEARTBEAT.md | ⚠️ Empty | 🔴 **Structurally broken — Day 42** |
| IDENTITY.md | ✅ Heather (populated) | ❌ **Blank template — Day 42** |
| USER.md | ✅ Josh (populated) | ❌ **Blank template — Day 42** |
| SOUL.md | ⚠️ Generic template | ⚠️ Generic template |
| AGENTS.md | ⚠️ Emoji contradiction | ⚠️ No trading rules, no gog-cli |
| TOOLS.md | ⚠️ Blank template | ⚠️ Blank — gog-cli undocumented |
| Dead fallback model | 🔴 claude-3.5-haiku (update to 3.5-flash chain) | N/A |
| Gemini 3.5 Flash fallback | 🟢 Recommended (Day 41, supersedes 3.1 Lite) | N/A |
| Google Workspace (gog-cli) | ❌ Not installed | 🔴 Installed + authed, **UNUSED Day 44** |
| gmailWatch | N/A | 🔴 **Disabled** |
| iMessage bridge | 🔴 Paused — fix in 2026.5.27 | N/A |
| OTEL v2 observability | 🟡 Available post-upgrade | 🟢 Available post-upgrade (high value) |
| defineToolPlugin | 🟡 Available post-upgrade | 🟢 Available post-upgrade (Alpaca plugin) |
| Memory hybrid search | 📋 Pre-planned (halfLife: 60d) | 📋 Pre-planned (halfLife: 14d) |
| Transcript infrastructure | 🟡 Available post-upgrade | 🟢 Available post-upgrade (source provenance) |
| xAI/Grok pipeline | N/A | 🟢 Available post-upgrade |
| Alpaca MCP V2 | N/A | 🟢 Available post-upgrade (61 actions, V1 compat BROKEN) |
| EdgarTools MCP | N/A | 🟢 Available post-upgrade |
| Cron jobs | ❌ None | ❌ **None — idle during market hours** |
| strictInlineEval | Not set | 🔴 `false` in financial env |
| memory-lancedb-pro | 📋 Future option | 🟡 Evaluate vs. built-in memory-core |
| Cumulative findings | **~132** | **~161** |
| Resolved findings | **0** | **0** |
| Days since last implementation | **41** | **41** |

---

## Shared Config Snippet Library (Current — Day 41)

### contextPruning — Noah (CRITICAL — apply now)
```json
"contextPruning": {
  "mode": "cache-ttl",
  "ttl": "30m"
}
```

### compaction — Josh (add to agents.defaults)
```json
"compaction": {
  "reserveTokensFloor": 30000,
  "memoryFlush": { "enabled": true, "softThresholdTokens": 6000 }
}
```

### memory-core — Noah (enable now, apply with MEMORY.md)
```json
"memory-core": {
  "enabled": true,
  "config": {
    "deduplication": true,
    "temporalDecay": true,
    "search": {
      "hybrid": {
        "enabled": true,
        "vectorWeight": 0.5,
        "textWeight": 0.5,
        "candidateMultiplier": 6
      },
      "mmr": { "enabled": true, "lambda": 0.6 },
      "temporalDecay": { "enabled": true, "halfLifeDays": 14 }
    }
  }
}
```

### Active Memory — Noah (apply after memory-core + MEMORY.md)
```json
"active-memory": {
  "enabled": true,
  "config": {
    "agents": ["main"],
    "allowedChatTypes": ["direct"],
    "modelFallback": "anthropic/claude-sonnet-4-6",
    "queryMode": "recent",
    "promptStyle": "balanced",
    "timeoutMs": 15000,
    "maxSummaryChars": 300,
    "persistTranscripts": false
  }
}
```

### Active Memory — Josh (apply post-upgrade to 2026.5.27)
```json
"active-memory": {
  "enabled": true,
  "config": {
    "agents": ["main"],
    "allowedChatTypes": ["direct"],
    "modelFallback": "google/gemini-3-flash-preview",
    "queryMode": "recent",
    "promptStyle": "balanced",
    "timeoutMs": 15000,
    "maxSummaryChars": 220,
    "persistTranscripts": false
  }
}
```

### Josh fallback chain — UPDATED Day 41 (Gemini 3.5 Flash leads)
```json
"fallbacks": [
  "openrouter/google/gemini-3-5-flash",
  "openrouter/google/gemini-3-1-flash-lite-preview",
  "openrouter/google/gemini-2.5-flash"
]
```

### memory-core — Josh (apply post-upgrade)
```json
"memory-core": {
  "enabled": true,
  "config": {
    "deduplication": true,
    "temporalDecay": true,
    "search": {
      "hybrid": {
        "enabled": true,
        "vectorWeight": 0.6,
        "textWeight": 0.4,
        "candidateMultiplier": 4
      },
      "mmr": { "enabled": true, "lambda": 0.7 },
      "temporalDecay": { "enabled": true, "halfLifeDays": 60 }
    }
  }
}
```

### Cron jobs — Noah (post-upgrade to 2026.5.27)
```json
"cron": {
  "jobs": [
    {
      "name": "premarket-catalyst-scan",
      "schedule": "0 6 * * 1-5",
      "timezone": "America/New_York",
      "command": "Run EDGAR 8-K scan for overnight filings. Poll gog gmail search for EDGAR alerts and broker research emails. Check gog calendar events --today for scheduled catalysts. Screen for material events. Deliver 5-bullet briefing.",
      "deliverTo": "1496556746444112173"
    },
    {
      "name": "postmarket-pnl",
      "schedule": "0 17 * * 1-5",
      "timezone": "America/New_York",
      "command": "Review Alpaca paper positions. Calculate P&L. Update memory/YYYY-MM-DD.md with catalyst log. Update gog sheets portfolio tracker.",
      "deliverTo": "1496556746444112173"
    }
  ]
}
```

---

## Workspace File Gap Analysis (Day 41)

### Files Identical in Both Repos (Zero Customization)
| File | SHA | State |
|------|-----|-------|
| `SOUL.md` | 792306ac | Generic upstream template — byte-for-byte identical |
| `AGENTS.md` | 3faead97 | Generic template — no trading rules, no gog-cli ref, no emoji override |
| `TOOLS.md` | 917e2fa8 | Blank template — fake examples only |

### Josh Has, Noah Missing
- `IDENTITY.md` (populated — "Heather Schwartz")
- `USER.md` (populated — Josh Meyers, LA, Bliss/Oben, NO emoji reactions)
- iMessage channel (paused but configured)

### Noah Has, Josh Missing
- `workspace/reports/ae-target-companies-2026-04-22.md` (21KB, 37 days old, never referenced)
- `skills/gog-cli/` (Google Workspace CLI — full Gmail/Calendar/Drive/Sheets/Tasks/Contacts — UNUSED)
- `gogcli/state.json` (authenticated to Ngkatz.ai@gmail.com — gmailWatch: false)
- `gogcli/` CLI state directory
- Compaction config (misconfigured but present; Josh has nothing)
- memory-core in allow list (not in entries)
- Active Memory plugin eligibility (Josh version-gated)

### Missing in Both
- `MEMORY.md` — Day 42
- Functional `HEARTBEAT.md` (Josh: empty; Noah: broken code block)
- Populated `TOOLS.md`
- Customized `SOUL.md`

---

## Active Behavioral Gaps (Day 41)

**Josh — AGENTS.md Emoji Contradiction (Day 41):**
USER.md: `STRICT: DO NOT SEND EMOJI REACTIONS`. AGENTS.md: "React Like a Human!" Direct contradiction in every session.

**Noah — gog-cli Invisible to Agent (Day 44):**
TOOLS.md blank. AGENTS.md stock template. The agent cannot discover or use its own installed, authenticated Google Workspace tool. Gmail, Calendar, Sheets, Drive, Tasks — all available, all dark.

**Noah — gmailWatch Disabled (Day 44):**
Real-time email push not flowing. Trading email alerts (EDGAR, broker, news) are not received in real time. Agent must poll manually — but HEARTBEAT.md is broken, so polling never happens either.

**Noah — contextPruning 5m TTL (Day 15):**
Every pre-market session resets every 5 minutes. One-line fix. 15 days.

**Noah — No Trading Guardrails in AGENTS.md (Day 41):**
No paper-only rule. No audit trail requirement. Zero guardrails for a trading agent with order execution access.

---

## Zero-Config Backlog (Day 41)

| Action | Target | Effort | Days Documented | Status |
|--------|--------|--------|-----------------|--------|
| Fix contextPruning 5m → 30m | **Noah** | 60 sec | **15 days** | 🔴 DO IT NOW |
| Update fallback chain to Gemini 3.5 Flash | **Josh** | 1 min | **Day 41** | 🔴 DO IT |
| Add compaction config | **Josh** | 2 min | 5 days | 🔴 DO IT |
| Enable memory-core in entries | **Noah** | 3 min | **31 days** | 🔴 DO IT |
| Add Active Memory config | **Noah** | 3 min | **Day 41** | 🟢 DO IT (after memory-core) |
| Increase softThresholdTokens to 10000 | **Noah** | 1 min | 5 days | 🟢 DO IT |
| Create MEMORY.md | **Both** | 15 min each | **42 days** | 🔴 DO IT |
| Fill IDENTITY.md + USER.md | **Noah** | 15 min | **42 days** | 🔴 DO IT |
| Fix AGENTS.md emoji contradiction | **Josh** | 2 min | **42 days** | 🔴 DO IT |
| Add trading rules + gog-cli to AGENTS.md | **Noah** | 30 min | 5 days | 🟢 DO IT |
| Replace HEARTBEAT.md (broken code block) | **Noah** | 5 min | **42 days** | 🔴 DO IT |
| Populate HEARTBEAT.md | **Josh** | 5 min | **42 days** | 🟢 Easy |
| Populate TOOLS.md with gog-cli reference | **Noah** | 15 min | **Day 41** | 🔴 DO IT |
| Populate TOOLS.md with env data | **Josh** | 10 min | **42 days** | ✅ Easy |
| Add claude-opus-4-7 to Noah catalog | **Noah** | 1 min | 3 days | ✅ Easy |
| Delete BOOTSTRAP.md | **Both** | 30 sec | 2 days | ✅ Easy |

**Total: ~100 minutes. Zero upgrades required. 0 of this done in 41 days.**

---

## Platform Risk Summary (Active — Day 41)

| Risk | Instance | Severity | Day # |
|------|----------|----------|-------|
| contextPruning 5m TTL — market open | Noah | CRITICAL | **15** |
| MEMORY.md never created | Both | CRITICAL | **42** |
| IDENTITY.md + USER.md blank | Noah | CRITICAL | **42** |
| gog-cli invisible to agent | Noah | HIGH | **44** |
| gmailWatch disabled | Noah | MEDIUM | **44** |
| compaction config missing | Josh | HIGH | 5 |
| memory-core not in entries | Noah | HIGH | **31** |
| Active Memory not configured | Noah | MEDIUM | **Day 41** |
| Dead fallback (update to 3.5 Flash chain) | Josh | MEDIUM | **22** |
| AGENTS.md emoji contradiction | Josh | MEDIUM | **42** |
| AGENTS.md missing trading guardrails | Noah | MEDIUM | 5 |
| HEARTBEAT.md broken structure | Noah | HIGH | **42** |
| HEARTBEAT.md empty | Josh | HIGH | **42** |
| softThresholdTokens too low | Noah | MEDIUM | 5 |
| strictInlineEval: false in financial env | Noah | MEDIUM | 3 |
| iMessage bridge paused | Josh | MEDIUM | **33+** |

---

## Trend Analysis — Day 41

**Zero implementations across 41 days of documented research.**

Morning scan adds three new developments not in yesterday's evening scan:

1. **gog-cli confirmed as Google Workspace** (NOAH-80/81/82) — Noah has full Gmail/Calendar/Drive/Sheets/Tasks/Contacts access for Ngkatz.ai@gmail.com, all authenticated, all unused for 44 days. gmailWatch is disabled. This compounds the HEARTBEAT and MEMORY gaps: Noah's agent cannot poll for trading emails, cannot populate a Google Calendar with PDUFA/earnings dates, and cannot maintain portfolio tracking spreadsheets — all of which gog-cli could do right now.

2. **Active Memory plugin eligibility splits** (NOAH-83 / JOSH-73) — Noah at 2026.4.15 can enable Active Memory now. Josh at 2026.3.22 cannot. Once Noah gets MEMORY.md + memory-core working, Active Memory becomes the final piece: automatic context pre-loading at session start. The three configs apply together: `memory-core` entries → `MEMORY.md` → `active-memory`.

3. **Gemini 3.5 Flash updates Josh's fallback chain** (JOSH-71) — replaces the Gemini 3.1 Flash Lite as the recommended primary fallback for Josh. One GitHub line edit. The dead claude-3.5-haiku can finally be replaced with a modern 3-model Gemini chain.

**Day 41 additions: 3 new Josh findings (JOSH-71, 72, 73), 5 new Noah findings (NOAH-80, 81, 82, 83, 84).**
**Cumulative: ~132 Josh findings, ~161 Noah findings, 0 resolved.**

---

*Generated by AlphaClaw Apex Fleet Research Agent — Morning Scan — 2026-05-29 (Day 41)*
