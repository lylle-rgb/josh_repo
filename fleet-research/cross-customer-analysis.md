# Cross-Customer Analysis — AlphaClaw Apex Fleet

**Last Updated:** 2026-05-28 Morning (Day 40)
**Instances:** Josh (Heather Schwartz, personal assistant) | Noah (Market Catalyst Agent, stock research)
**Scan cadence:** Morning + Evening daily

---

## Day 40 Morning — New Research (2026-05-28)

### OpenClaw 2026.5.26 Confirmed Stable — No New Release This Morning

2026.5.26 graduated to stable on May 27. No new beta or patch released this morning. Upgrade target remains **2026.5.26** for both instances.

| Instance | Current | Upgrade Target | Days Behind |
|----------|---------|----------------|-------------|
| Josh | 2026.3.22 | 2026.5.26 | **67 days** |
| Noah | 2026.4.15 | 2026.5.26 | **44 days** |

---

### Gemini 3.1 Flash Lite — Josh-Specific Opportunity

Google's Gemini 3.1 Flash Lite (`openrouter/google/gemini-3-1-flash-lite-preview`) is now the recommended dead-fallback replacement for Josh's broken `claude-3.5-haiku` fallback (JOSH-50, Day 21).

| Spec | Gemini 3.1 Flash Lite | Dead claude-3.5-haiku fallback |
|------|----------------------|-------------------------------|
| Speed | 363 tokens/sec (45% faster than 2.5 Flash) | 30-second timeout |
| Cost | 1/8 of Gemini Pro | N/A (fails) |
| Availability | Active on OpenRouter | ❌ Broken |

**Updated Josh fallback chain:**
```json
"fallbacks": [
  "openrouter/google/gemini-3-1-flash-lite-preview",
  "openrouter/google/gemini-2.5-flash",
  "openrouter/anthropic/claude-haiku-4-5"
]
```

Noah uses Anthropic exclusively — no change needed for Noah.

---

### OTEL v2 Observability — Noah-Specific Post-Upgrade

OpenClaw's observability plugin requires ≥ 2026.4.21. Noah is on 2026.4.15 (6 patch versions short). Josh is on 2026.3.22 (much further away but also gets it on upgrade to 2026.5.26).

| Telemetry | Josh Use | Noah Use |
|-----------|----------|----------|
| Model usage traces | iMessage/calendar session tracking | Pre-market catalyst scan attribution |
| Token usage metrics | Cost per session type | EDGAR vs. Grok vs. synthesis cost breakdown |
| Run duration histograms | Session length patterns | Pre-market scan timing optimization |
| Context size tracking | Memory growth over time | Catalyst session context management |

**Primary beneficiary: Noah.** Trading sessions have measurable performance characteristics. OTEL telemetry gives Noah visibility into exactly where time and tokens go during 20-30 minute pre-market scans.

---

### defineToolPlugin — Available to Both Post-Upgrade

OpenClaw 2026.5.17+ ships the typed `defineToolPlugin` API. Both instances get it on upgrade to 2026.5.26.

| Instance | Plugin Opportunity | Priority |
|----------|--------------------|----------|
| Josh | Typed iMessage contact lookup, calendar approval flows | MEDIUM |
| Noah | Typed Alpaca order plugin with mandatory thesis parameter | HIGH |

Noah's Alpaca plugin opportunity is higher priority because:
1. Mandatory `thesis` field enforces audit trail at the tool level (not just AGENTS.md guidance)
2. Schema validation catches bad order parameters before they reach Alpaca API
3. CI-checkable manifests prevent silent parameter drift

---

### Memory Hybrid Search — Configuration Diverges by Use Case

Both instances need different hybrid search configs based on their memory patterns:

| Parameter | Josh (Personal Assistant) | Noah (Trading Agent) |
|-----------|--------------------------|---------------------|
| vectorWeight | 0.6 | 0.5 |
| textWeight | 0.4 | 0.5 |
| halfLifeDays | 60 | 14 |
| candidateMultiplier | 4 | 6 |
| MMR lambda | 0.7 | 0.6 |

**Why Josh gets higher vectorWeight:** Personal assistant context is more semantic — "Josh's work situation" requires semantic understanding, not just keyword match.

**Why Noah gets higher textWeight and shorter halfLifeDays:** Ticker symbols, catalyst types (FDA PDUFA, Form 4, 8-K), and sector terms require exact keyword matching. Market patterns decay in 2 weeks, not 2 months.

---

### Transcript Core Infrastructure — Both Benefit Differently

OpenClaw 2026.5.26 elevates transcripts to core infrastructure with **source-provider provenance** (each segment knows which tool produced it).

| Instance | Key Benefit |
|----------|-------------|
| Josh | iMessage conversation history + media provenance after re-enabling bridge (post JOSH-62 fix) |
| Noah | EDGAR catalyst attribution — "Form 4 surfaced by EdgarTools, verbally confirmed by Noah" — builds a proper decision trail |

Noah's benefit is higher-signal: transcript source provenance transforms voice trade thesis capture from "Noah mentioned NVDA" to "EDGAR 8-K filing surfaced NVDA catalyst; Noah verbal thesis confirms" — a materially better audit record.

---

## Day 40 Fleet Comparison (Full)

| Dimension | Josh / Heather | Noah / Market Catalyst |
|---|---|---|
| OpenClaw version | 2026.3.22 | 2026.4.15 |
| Latest stable | **2026.5.26** | **2026.5.26** |
| Gap (days behind) | **67 days** | **44 days** |
| compaction config | 🔴 **MISSING — Day 40** | ✅ Configured (threshold too low) |
| contextPruning TTL | ❌ Not set | 🔴 **5m — DAY 14 CRITICAL** |
| softThresholdTokens | N/A | ⚠️ 4000 (rec: 10000) |
| memory-core | ❌ Not eligible (pre-upgrade) | 🔴 Allowlisted, **no entries — Day 30** |
| MEMORY.md | ❌ **Day 41** | ❌ **Day 41** |
| HEARTBEAT.md | ⚠️ Empty | 🔴 **Structurally broken — Day 41** |
| IDENTITY.md | ✅ Heather (populated) | ❌ **Blank template — Day 41** |
| USER.md | ✅ Josh (populated) | ❌ **Blank template — Day 41** |
| SOUL.md | ⚠️ Generic template | ⚠️ Generic template |
| AGENTS.md | ⚠️ Emoji contradiction | ⚠️ No trading guardrails |
| TOOLS.md | ⚠️ Blank template | ⚠️ Blank template |
| Dead fallback model | 🔴 claude-3.5-haiku — Day 21 | N/A |
| Gemini 3.1 Flash Lite fallback | 🟢 Recommended replacement | N/A |
| iMessage bridge | 🔴 Paused — fix in 2026.5.26 | N/A |
| OTEL v2 observability | 🟡 Available post-upgrade | 🟢 Available post-upgrade (high value) |
| defineToolPlugin | 🟡 Available post-upgrade | 🟢 Available post-upgrade (Alpaca plugin) |
| Memory hybrid search | 📋 Pre-planned (halfLife: 60d) | 📋 Pre-planned (halfLife: 14d) |
| Transcript infrastructure | 🟡 Available post-upgrade | 🟢 Available post-upgrade (source provenance) |
| xAI/Grok pipeline | N/A | 🟢 Available post-upgrade |
| Alpaca MCP V2 | N/A | 🟢 Available post-upgrade |
| EdgarTools MCP | N/A | 🟢 Available post-upgrade |
| Cron jobs | ❌ None | ❌ **None — idle during market hours** |
| strictInlineEval | Not set | 🔴 `false` in financial env |
| Cumulative findings | **~129** | **~153** |
| Resolved findings | **0** | **0** |
| Days since last implementation | **40** | **40** |

---

## Shared Config Snippet Library (Current)

### contextPruning — Noah (apply immediately, Josh add on upgrade)
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

### compaction fix — Noah (update threshold)
```json
"softThresholdTokens": 10000
```

### memory-core entries — Noah (apply now)
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

### Josh fallback fix (apply now, no restart)
```json
"fallbacks": [
  "openrouter/google/gemini-3-1-flash-lite-preview",
  "openrouter/google/gemini-2.5-flash",
  "openrouter/anthropic/claude-haiku-4-5"
]
```

### OTEL v2 — Noah (apply post-upgrade)
```json
"diagnostics": {
  "otel": {
    "endpoint": "http://localhost:4318",
    "serviceName": "market-catalyst-agent",
    "traces": true,
    "metrics": true,
    "logs": false
  }
}
```

### Noah model catalog (apply now)
```json
"models": {
  "anthropic/claude-opus-4-7": {},
  "anthropic/claude-opus-4-6": {},
  "anthropic/claude-sonnet-4-6": {}
}
```

### Cron jobs — Noah (post-upgrade to 2026.5.26)
```json
"cron": {
  "jobs": [
    {
      "name": "premarket-catalyst-scan",
      "schedule": "0 6 * * 1-5",
      "timezone": "America/New_York",
      "command": "Run EDGAR 8-K scan for overnight filings. Check Grok for X catalyst signals. Screen for material events. Deliver 5-bullet briefing.",
      "deliverTo": "1496556746444112173"
    },
    {
      "name": "postmarket-pnl",
      "schedule": "0 17 * * 1-5",
      "timezone": "America/New_York",
      "command": "Review Alpaca paper positions. Calculate P&L. Update memory/YYYY-MM-DD.md with catalyst log.",
      "deliverTo": "1496556746444112173"
    }
  ]
}
```

### Upgrade checklist — Both
```bash
cp openclaw.json openclaw.json.bak-pre-5.26
node --version  # Must be >= v22.19.0
# Upgrade via AlphaClaw UI
openclaw doctor
openclaw channels status --probe
```

---

## Workspace File Gap Analysis (Day 40)

### Files Identical in Both Repos (Zero Customization)
| File | SHA | State |
|------|-----|-------|
| `SOUL.md` | 792306ac | Generic upstream template — byte-for-byte identical |
| `AGENTS.md` | 3faead97 | Generic template — no trading rules, no emoji override |
| `TOOLS.md` | 917e2fa8 | Blank template — fake examples only |

### Josh Has, Noah Missing
- `IDENTITY.md` (populated — "Heather Schwartz")
- `USER.md` (populated — Josh Meyers, LA, Bliss/Oben, NO emoji reactions)

### Noah Has, Josh Missing
- `workspace/reports/` (AE report with USER.md/IDENTITY.md source material — unused 40 days)
- `skills/gog-cli/` (unaudited in financial environment)
- `gogcli/`
- Compaction config (misconfigured but present; Josh has nothing)
- memory-core in allow list (not in entries — Day 30)

### Missing in Both
- `MEMORY.md` — Day 41
- `memory/` populated directory
- `HEARTBEAT.md` functional (Josh: empty; Noah: structurally broken code block)

---

## Active Behavioral Gaps (Day 40)

**Josh — AGENTS.md Emoji Contradiction (Day 40):**
USER.md: `STRICT: DO NOT SEND EMOJI REACTIONS`. AGENTS.md "React Like a Human!" instructs emoji use. Direct contradiction, active in every session.

**Noah — No Trading Guardrails in AGENTS.md (Day 40):**
No "paper trading only" rule. No audit trail requirement. No thesis-before-trade rule. A trading agent with order execution access operating on a generic personal assistant AGENTS.md. This becomes a live risk the moment Alpaca MCP is installed.

**Noah — contextPruning 5m TTL (Day 14):**
Every pre-market session resets every 5 minutes. Re-reads blank IDENTITY.md and USER.md on each reset. 14 days of disrupted sessions. One-line fix.

---

## Zero-Config Backlog (Day 40)

| Action | Target | Effort | Days Documented | Status |
|--------|--------|--------|-----------------|--------|
| Fix contextPruning 5m → 30m | **Noah** | 60 sec | **14 days** | 🔴 DO IT NOW |
| Update dead fallback → Gemini 3.1 Flash Lite | **Josh** | 1 min | **1 day** | 🔴 DO IT |
| Add compaction config | **Josh** | 2 min | 4 days | 🔴 DO IT |
| Add memory-core entries (with hybrid search) | **Noah** | 5 min | **30 days** | 🔴 DO IT |
| Increase softThresholdTokens to 10000 | **Noah** | 1 min | 4 days | 🟢 DO IT |
| Create MEMORY.md | **Both** | 15 min each | **41 days** | 🔴 DO IT |
| Fill IDENTITY.md + USER.md | **Noah** | 15 min | **41 days** | 🔴 DO IT |
| Fix AGENTS.md emoji contradiction | **Josh** | 2 min | **41 days** | 🔴 DO IT |
| Add trading rules to AGENTS.md | **Noah** | 30 min | 4 days | 🟢 DO IT |
| Replace HEARTBEAT.md (broken) | **Noah** | 5 min | **41 days** | 🔴 DO IT |
| Populate HEARTBEAT.md | **Josh** | 5 min | **41 days** | 🟢 Easy |
| Populate TOOLS.md | **Both** | 10 min | **41 days** | ✅ Easy |
| Add claude-opus-4-7 to Noah catalog | **Noah** | 1 min | 2 days | ✅ Easy |
| Delete BOOTSTRAP.md | **Both** | 30 sec | 1 day | ✅ Easy |

**Total: ~90 minutes. Zero upgrades required. 0 of this done in 40 days.**

---

## Platform Risk Summary (Active — Day 40)

| Risk | Instance | Severity | Day # |
|------|----------|----------|-------|
| contextPruning 5m TTL — market open | Noah | CRITICAL | **14** |
| MEMORY.md never created | Both | CRITICAL | **41** |
| IDENTITY.md + USER.md blank | Noah | CRITICAL | **41** |
| compaction config missing | Josh | HIGH | 4 |
| memory-core not in entries | Noah | HIGH | **30** |
| Dead fallback claude-3.5-haiku | Josh | MEDIUM | **21** |
| AGENTS.md emoji contradiction | Josh | MEDIUM | **41** |
| AGENTS.md missing trading guardrails | Noah | MEDIUM | 4 |
| HEARTBEAT.md broken structure | Noah | HIGH | **41** |
| softThresholdTokens too low | Noah | MEDIUM | 4 |
| strictInlineEval: false in financial env | Noah | MEDIUM | 2 |
| skills/gog-cli unaudited | Noah | HIGH | 6 days |
| iMessage bridge paused | Josh | MEDIUM | **32+** |
| Discord rate limit awareness | Both | LOW | 2 days |

---

## Trend Analysis — Day 40

**Zero implementations across 40 days of documented research.**

OpenClaw 2026.5.26 is now stable. Both instances need to upgrade to reach a version that has been shipping since May 27, 2026. Josh is 67 days behind. Noah is 44 days behind.

This morning's research adds three operational improvements not previously documented: Gemini 3.1 Flash Lite as Josh's dead-fallback replacement (immediate, no restart), OTEL v2 observability for Noah post-upgrade (high-value trading telemetry), and divergent memory hybrid search configurations tuned per use case.

The defineToolPlugin system (2026.5.17+) opens typed skill development for both: Josh for iMessage/calendar typed tools, Noah for a typed Alpaca order plugin with mandatory thesis enforcement. Both require upgrade to access.

Transcript core infrastructure in 2026.5.26 is better than previously documented — source-provider provenance tracking means Noah's voice trade thesis capture will attribute catalyst signals to their source tools (EDGAR, Grok), not just to ambient speech. This makes the decision trail materially more useful for reviewing trading sessions.

**Day 40 additions: 4 new Josh findings (JOSH-66 through JOSH-69), 5 new Noah findings (NOAH-73 through NOAH-77).**
**Cumulative: ~129 Josh findings, ~153 Noah findings, 0 resolved.**

---

*Generated by AlphaClaw Apex Fleet Research Agent — Morning Scan — 2026-05-28 (Day 40)*
