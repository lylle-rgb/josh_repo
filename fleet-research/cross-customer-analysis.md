# Cross-Customer Analysis — AlphaClaw Apex Fleet

**Last Updated:** 2026-05-26 (Morning Scan — Day 38)
**Instances:** Josh (Heather Schwartz, personal assistant) | Noah (Market Catalyst Agent, stock research)
**Scan cadence:** Morning + Evening daily

---

## Day 38 Morning — New Research (2026-05-26)

### Alpaca MCP V2 — 61 Actions Confirmed, New Screening + Option Chain Tools

Alpaca's MCP Server V2 (April 2026) is now fully documented at 61 actions (vs 43 prior). The additions most significant for Noah:

| New V2 Action | Catalyst Agent Value |
|---------------|---------------------|
| Market screening tools | Built-in screener by volume, price move, sector — replaces manual screener |
| Option chain exploration | Unusual options activity = leading catalyst signal before price moves |
| Order replacements | Modify paper positions without cancel + re-enter |
| Account activity logs | Full audit trail — pairs with memory/daily log |

The screening tools are particularly significant: the Alpaca MCP V2 now handles work that previously required a separate tool or manual query. Noah's post-upgrade install path is now fully validated.

---

### xAI/Grok OAuth Profile Reuse — Pre-Market Signal Pipeline Confirmed for Noah

OpenClaw 2026.5.22 threads active-agent xAI auth profiles through `web_search` automatically. Authenticate once; every Grok web_search call reuses the profile. This makes the pre-market X/Twitter signal pipeline practical:

**X signals arrive 15-45 minutes before EDGAR 8-K filings appear in conventional tools.** Combined with EdgarTools MCP (free), Noah's catalyst pipeline would be:
- 5:45 AM ET: Grok X scan (breaking catalyst signals on Twitter)
- 6:00 AM ET: EDGAR overnight 8-K scan (EdgarTools MCP)
- 6:15 AM ET: Combine → catalyst thesis → Alpaca paper order

Auth config to add post-upgrade: `"xai:default": { "provider": "xai", "mode": "device-code" }`

---

### Memory Architecture Research — Josh Has No Compaction Config

Community research confirms MEMORY.md should stay **under 800 words**, and the pre-compaction memory flush is "the single most impactful configuration change" for memory reliability.

**Critical gap discovered this morning:** Josh has no `compaction` or `contextPruning` config in openclaw.json. Noah has both (but `softThresholdTokens: 4000` is too low for trading sessions — recommend 10000).

| Dimension | Josh | Noah |
|-----------|------|------|
| compaction.memoryFlush | ❌ **MISSING** | ✅ Configured |
| contextPruning.ttl | ❌ **MISSING** | 🔴 **5m — Day 11 bug** |
| softThresholdTokens | ❌ Missing | ⚠️ 4000 (too low, rec: 10000) |

Without compaction config, even if Josh creates MEMORY.md today (Day 38), information accumulated in longer sessions may be silently lost when context fills.

**Fix for Josh (add to openclaw.json, no restart):**
```json
"agents": { "defaults": {
  "compaction": {
    "reserveTokensFloor": 30000,
    "memoryFlush": { "enabled": true, "softThresholdTokens": 6000 }
  },
  "contextPruning": { "mode": "cache-ttl", "ttl": "30m" }
}}
```

**Fix for Noah (softThresholdTokens only, no restart):**
```json
"softThresholdTokens": 10000
```

---

### Subagents — Parallel Task Opportunity for Both Instances

OpenClaw's subagent system (`maxSpawnDepth: 1` default) enables spawning parallel workers from a main agent. Workers announce results back to the calling channel.

| Instance | Subagent Application | Value |
|----------|---------------------|-------|
| **Josh** | Parallel heartbeat: email + calendar + contacts simultaneously | ~3x faster check latency |
| **Noah** | Parallel sector scans: Tech 8-Ks + Health 8-Ks + Grok X signals | ~3x faster pre-market briefing |

Cheaper model for sub-agents (haiku-level), main agent handles synthesis. Config: `agents.defaults.subagents.model`.

**Blocker for both:** Josh needs HEARTBEAT.md populated first; Noah needs upgrade + cron first.

---

### Node.js 22.19 Minimum — Pre-Upgrade Check for Both VPS Hosts

OpenClaw has raised the minimum Node.js 22 line to **22.19** (Pi packages updated to 0.75.1). Both instances should verify Node version before any upgrade attempt:

```bash
node --version  # Must be >= v22.19.0
```

---

### Day 38 Morning Fleet State

| Dimension | Josh / Heather | Noah / Market Catalyst |
|---|---|---|
| OpenClaw version | **2026.3.22** | **2026.4.15** |
| Latest stable | **2026.5.22** | **2026.5.22** |
| Alpha train | 2026.5.25-alpha.1 (do not deploy) | 2026.5.25-alpha.1 (do not deploy) |
| Gap (releases) | **~25** | **~18** |
| Upgrade overdue | **Day 5** | **Day 5** |
| compaction config | 🔴 **MISSING — Day 38** | ✅ Configured (but see below) |
| contextPruning TTL | ❌ Not set | 🔴 **5m — Day 11 CRITICAL** |
| softThresholdTokens | N/A | ⚠️ 4000 (rec: 10000) |
| memory-core | ❌ Not eligible (needs upgrade) | 🔴 Allowlisted, **no entries block — Day 27** |
| Active Memory plugin | ❌ Below min version | 🟢 Available now (v2026.4.15 ≥ 2026.4.10) |
| MEMORY.md | ❌ **Never created — Day 38** | ❌ **Never created — Day 38** |
| HEARTBEAT.md | ⚠️ Empty | 🔴 **Structurally broken (code-block bug)** |
| IDENTITY.md | ✅ Heather (populated) | ❌ **Blank template — Day 38** |
| USER.md | ✅ Josh (populated) | ❌ **Blank template — Day 38** |
| SOUL.md | ⚠️ Generic template | ⚠️ Generic template |
| AGENTS.md | ⚠️ Generic (emoji rule contradiction) | ⚠️ Generic (no trading guardrails) |
| TOOLS.md | ⚠️ Blank template | ⚠️ Blank template |
| iMessage bridge | 🔴 Paused — blocks 👍👎 reactions | N/A |
| Dead fallback model | 🔴 **claude-3.5-haiku — Day 18** | N/A |
| xAI/Grok | N/A | 🟢 Available on upgrade to 2026.5.22 |
| Alpaca MCP V2 | N/A | 🟢 **61 actions on upgrade** |
| Discord streaming | ❌ `"off"` | ❌ Not set |
| Discord buttons/dropdowns | 🟡 Post-upgrade | 🟡 Post-upgrade |
| Subagents | 🟡 After HEARTBEAT.md + upgrade | 🟡 After cron + upgrade |
| Cron jobs | ❌ None | ❌ **None — idle during market hours** |
| skills/gog-cli audit | N/A | ⚠️ **Unaudited — HIGH security in financial env** |
| iMessage reactions (beta) | 🟡 Coming (2026.5.24 stable ≈June 1-4) | N/A |
| Node.js 22.19 check | ⚠️ Verify before upgrade | ⚠️ Verify before upgrade |
| Cumulative findings | **~120** | **~140** |
| Resolved findings | **0** | **0** |
| Days since last implementation | **38** | **38** |

---

### Day 38 Zero-Config Backlog

| Action | Target | Effort | Days Documented | Status |
|--------|--------|--------|-----------------|--------|
| Fix contextPruning 5m → 30m | **Noah** | 60 sec | **11 days** | 🔴 DO IT NOW |
| Add memory-core entries block | **Noah** | 3 min | **27 days** | 🔴 DO IT |
| Increase softThresholdTokens to 10000 | **Noah** | 1 min | NEW | 🟢 DO IT |
| Add compaction + contextPruning config | **Josh** | 2 min | **NEW** | 🟢 DO IT |
| Add Active Memory plugin | **Noah** | 3 min | 1 day | 🟢 DO IT |
| Create MEMORY.md | **Both** | 15 min each | **38 days** | 🔴 DO IT |
| Fill IDENTITY.md + USER.md | **Noah** | 15 min | **38 days** | 🔴 DO IT |
| Fix dead claude-3.5-haiku fallback | **Josh** | 1 min | **18 days** | 🔴 DO IT |
| Replace HEARTBEAT.md (broken structure) | **Noah** | 5 min | **38 days** | 🔴 DO IT |
| Add HEARTBEAT.md content | **Josh** | 5 min | **38 days** | 🟢 Easy |
| Populate TOOLS.md | **Both** | 10 min each | **38 days** | ✅ Easy |
| Add opus-4-7 to Noah catalog | **Noah** | 1 min | 6 days | ✅ Easy |
| Enable Discord streaming `progress` | **Both** | 1 min each | 11+ days | ✅ Easy |

**Total: ~60 minutes. Zero upgrades required. 0 of this done in 38 days.**

---

## Day 37 Morning — Research Summary (2026-05-25)

### Upgrade Target Correction — 2026.5.22 Is Stable (Skip 2026.5.20)

2026.5.22 graduated to **stable on May 24, 2026**. Both instances should target 2026.5.22 directly, not 2026.5.20.

| Instance | Current | **Stable Target** | Gap |
|----------|---------|-------------------|-----|
| **Josh** | 2026.3.22 | **2026.5.22** | ~25 stable releases |
| **Noah** | 2026.4.15 | **2026.5.22** | ~18 stable releases |
| **Beta (do not deploy)** | — | 2026.5.24-beta.2 | — |

**What 2026.5.22 adds vs 2026.5.20:**

| Feature | Josh Impact | Noah Impact |
|---------|------------|-------------|
| Model listing 4100× speedup (20s → 5ms) | Faster session startup | **HIGH — pre-market cold starts** |
| Cron delivery via modern resolver APIs | Low | **HIGH — pipeline hardening** |
| Sub-agent bootstrap context limited (AGENTS.md + TOOLS.md) | Aligns with current hooks | Reduces token waste in research chains |
| Package shrinkwrap security | Medium | **HIGH — financial environment** |
| xAI/Grok integration stable | Low | **HIGH — real-time X catalyst signals** |
| WebChat tool-source deduplication | Medium (AlphaClaw UI) | Low |

---

### 🟢 Active Memory Available for Noah TODAY (No Upgrade)

Active Memory plugin shipped in OpenClaw 2026.4.10. Noah is on 2026.4.15. **Available now.**

| Instance | Active Memory Available? | Blocker |
|----------|--------------------------|--------|
| **Noah** | ✅ **YES — today, no upgrade** | Must create MEMORY.md + enable memory-core entries |
| **Josh** | ❌ Not yet | Requires upgrade to 2026.5.22 first |

---

### iMessage Thumb-Approval Reactions — Josh-Specific Beta Feature

2026.5.24-beta.2 introduces 👍/👎 reactions on iMessage for approve/deny of pending agent actions.
- **Current:** Josh types approval responses
- **With reactions:** Josh reacts 👍 to approve, 👎 to deny — no typing required
- **Pre-requisite:** iMessage bridge must be reconnected (currently `imessage_monitoring_paused: true`)
- **ETA:** 2026.5.24 stable ≈ June 1-4, 2026. Do NOT deploy beta.

Noah: Not applicable (iMessage not in stack).

---

## Workspace File Gap Analysis (Persistent)

### Files Identical in Both Repos (SHA match — zero customization)

| File | SHA | State |
|------|-----|-------|
| `SOUL.md` | 792306ac | Generic upstream template in BOTH |
| `AGENTS.md` | 3faead97 | Generic template in BOTH — no trading rules, no emoji override |
| `TOOLS.md` | 917e2fa8 | Blank template in BOTH — fake examples only |
| `HEARTBEAT.md` | (different) | Both effectively non-functional |

A personal assistant and a trading agent sharing identical SOUL.md and AGENTS.md is the clearest signal that neither instance has been personalized. 38 days of operation on identical generic templates.

### Josh Has, Noah Missing
- `IDENTITY.md` (populated with “Heather”) | `USER.md` (populated with Josh’s info)

### Noah Has, Josh Missing
- `workspace/reports/` directory | `skills/gog-cli/` | `gogcli/`
- Compaction config (but misconfigured) | memory-core in allow list (but not in entries)

### Missing in Both
- **MEMORY.md** — Day 38 | **memory/ populated directory** — 38 stateless sessions

### Critical Behavioral Gaps

**Josh — AGENTS.md Emoji Contradiction:**
USER.md says `STRICT: DO NOT SEND EMOJI REACTIONS`. AGENTS.md “React Like a Human!” section explicitly instructs Heather to use emoji reactions on Discord. These two files directly contradict. This causes behavioral violations in every Discord session.

**Noah — AGENTS.md Missing Trading Guardrails:**
No rule: “Paper trading only.” No rule: “Always state catalyst thesis before placing any order.” No rule: “Log every trade decision.” A generic assistant AGENTS.md is fine for a personal assistant; for a trading agent with order execution access, the absence of explicit guardrails is a behavioral risk that becomes active the moment Alpaca MCP is installed.

---

## Shared Config Snippet Library

### contextPruning — Both Instances
```json
"contextPruning": {
  "mode": "cache-ttl",
  "ttl": "30m",
  "keepLastAssistants": 3
}
```

### compaction (Josh — add immediately; Noah — update softThresholdTokens)
```json
// Josh: add to agents.defaults
"compaction": {
  "reserveTokensFloor": 30000,
  "memoryFlush": { "enabled": true, "softThresholdTokens": 6000 }
}

// Noah: update existing softThresholdTokens from 4000 to:
"softThresholdTokens": 10000
```

### memory-core entries block — Noah (apply now, no upgrade)
```json
"memory-core": {
  "enabled": true,
  "config": {
    "deduplication": true,
    "temporalDecay": true
  }
}
```

### Active Memory — Noah (available NOW on 2026.4.15)
```json
"active-memory": {
  "enabled": true,
  "config": {
    "agents": ["main"],
    "chatTypes": ["dm", "group"],
    "allowedChatIds": ["1496556746444112173"],
    "inheritSessionModel": true,
    "timeout": 15000,
    "setupGraceTimeoutMs": 5000,
    "maxSummaryChars": 220
  }
}
```

### memory-core + Active Memory — Josh (post-upgrade to 2026.5.22)
```json
"plugins": {
  "allow": ["discord", "usage-tracker", "memory-core", "active-memory"],
  "entries": {
    "discord": {"enabled": true},
    "usage-tracker": {"enabled": true},
    "memory-core": {"enabled": true, "config": {"deduplication": true, "temporalDecay": true}},
    "active-memory": {
      "enabled": true,
      "config": {
        "agents": ["main"], "chatTypes": ["dm"],
        "allowedChatIds": ["JOSH_DISCORD_DM_CHANNEL_ID"],
        "inheritSessionModel": true, "timeout": 12000,
        "setupGraceTimeoutMs": 5000, "maxSummaryChars": 220
      }
    }
  }
}
```

### Josh fallback fix (no restart, no upgrade)
```json
"fallbacks": [
  "openrouter/google/gemini-3.1-flash-lite-preview",
  "openrouter/google/gemini-2.5-flash",
  "openrouter/anthropic/claude-haiku-4-5"
]
```

### Noah model catalog (add today — no upgrade needed)
```json
"models": {
  "anthropic/claude-opus-4-7": {},
  "anthropic/claude-opus-4-6": {},
  "anthropic/claude-sonnet-4-6": {},
  "xai/grok-3-mini": {}
}
```

### Discord streaming — Both
```json
"streaming": "progress"
```

### Noah xAI/Grok auth (post-upgrade)
```json
"xai:default": { "provider": "xai", "mode": "device-code" }
```

### Cron (Noah post-upgrade to 2026.5.22)
```json
"cron": {
  "jobs": [
    {
      "name": "premarket-catalyst-scan",
      "schedule": "0 6 * * 1-5",
      "timezone": "America/New_York",
      "command": "Run EDGAR 8-K scan for overnight filings. Check Grok for X catalyst signals. Screen for material events. Deliver 5-bullet briefing to channel.",
      "deliverTo": "1496556746444112173"
    },
    {
      "name": "postmarket-pnl",
      "schedule": "0 17 * * 1-5",
      "timezone": "America/New_York",
      "command": "Review Alpaca paper positions. Calculate P&L. Review day's catalysts vs. actual moves. Update MEMORY.md watchlist.",
      "deliverTo": "1496556746444112173"
    }
  ],
  "retry": {
    "maxAttempts": 3,
    "backoffMs": [30000, 60000, 300000],
    "retryOn": ["rate_limit", "overloaded", "network", "server_error"]
  }
}
```

### Backup before any upgrade (both instances)
```bash
cp openclaw.json openclaw.json.bak-pre-5.22
```

### Subagents — Both (post setup)
```json
// Josh: parallel heartbeat workers
"subagents": {
  "runTimeoutSeconds": 60,
  "model": { "primary": "google/gemini-3-flash-preview" }
}

// Noah: parallel sector scan workers (cheaper model)
"subagents": {
  "runTimeoutSeconds": 120,
  "model": { "primary": "anthropic/claude-haiku-4-5" }
}
```

---

## Platform Risk Summary (Active — Day 38)

| Risk | Instance | Severity | Day # | Fix |
|------|----------|----------|-------|-----|
| contextPruning 5m TTL | Noah | CRITICAL | **11** | 60 sec, no restart |
| MEMORY.md never created | Both | CRITICAL | **38** | 15 min, zero risk |
| IDENTITY.md + USER.md blank | Noah | CRITICAL | **38** | 15 min, zero risk |
| compaction config missing | Josh | HIGH | **NEW** | 2 min, no restart |
| memory-core not in entries | Noah | HIGH | **27** | 3 min, no restart |
| softThresholdTokens too low | Noah | MEDIUM | **NEW** | 1 line, no restart |
| Dead fallback claude-3.5-haiku | Josh | MEDIUM | **18** | 1 min, no restart |
| Active Memory not configured | Noah | HIGH | 2 days | 5 min, available now |
| iMessage bridge paused | Josh | MEDIUM | **28+** | Investigation needed |
| skills/gog-cli unaudited | Noah | HIGH | — | Audit or remove |
| AGENTS.md emoji contradiction | Josh | MEDIUM | **38** | 2 min, edit file |
| AGENTS.md missing trading guardrails | Noah | MEDIUM | **38** | 30 min (write rules) |
| HEARTBEAT.md broken structure | Noah | HIGH | **38** | Replace file, 5 min |
| Node.js 22.19 check | Both | LOW | NEW | `node --version` |
| Config backup before upgrade | Both | HIGH | — | Before any change |

---

## Priority Matrix — Fleet-Wide (Day 38 Morning)

### CRITICAL — Right Now (No Upgrade Needed)
| Item | Josh | Noah |
|---|---|---|
| Fix contextPruning 5m → 30m | Add it (missing entirely) | 🔴 **60 sec — Day 11** |
| Add compaction config | 🔴 **2 min — prerequisite for memory** | N/A (has it, update threshold) |
| Create MEMORY.md | 🔴 **15 min — Day 38** | 🔴 **15 min — Day 38** |
| Enable memory-core in entries | N/A (upgrade first) | 🔴 **3 min — Day 27** |
| Increase softThresholdTokens to 10000 | Set 6000 (above) | 🟢 **1 min — NEW** |
| Enable Active Memory plugin | N/A (upgrade first) | 🟢 **5 min — available now** |
| Fill IDENTITY.md + USER.md | N/A (done) | 🔴 **15 min — Day 38** |
| Fix dead claude-3.5-haiku fallback | 🔴 **1 min — Day 18** | N/A |
| Fix AGENTS.md emoji contradiction | 🔴 **2 min — Day 38** | N/A |

### HIGH — This Week
| Item | Josh | Noah |
|---|---|---|
| Upgrade to OpenClaw 2026.5.22 | 3.22 → 5.22 | 4.15 → 5.22 |
| Replace HEARTBEAT.md (broken) | Add tasks | Replace structure entirely |
| Enable Discord streaming `progress` | □ 1 min | □ 1 min |
| Populate TOOLS.md | □ 10 min | □ 10 min |
| Personalize SOUL.md + AGENTS.md | □ 30 min | □ 30 min (+ add trading rules) |
| Audit skills/gog-cli | N/A | □ **HIGH security** |
| Node.js 22.19 check | □ Pre-upgrade | □ Pre-upgrade |
| Reconnect iMessage bridge | 🟡 Needed for 👍👎 reactions | N/A |

### MEDIUM — Post-Upgrade
| Item | Josh | Noah |
|---|---|---|
| Discord button interactions | □ Post-upgrade (zero config) | □ Post-upgrade |
| Meeting Capture plugin (Discord voice) | □ Post-upgrade | N/A |
| Active Memory + allowedChatIds | □ Post-upgrade | ✅ Available now |
| Cron pre/post-market jobs | N/A | □ Post-upgrade |
| Subagents parallel tasks | □ After HEARTBEAT.md | □ Post-upgrade + cron |

### LOW / OPPORTUNITY — Post-Upgrade
| Item | Josh | Noah |
|---|---|---|
| iMessage 👍👎 reactions | □ 2026.5.24 stable (≈June 1-4) | N/A |
| xAI/Grok — X/Twitter signals | N/A | □ **On upgrade (direct alpha edge)** |
| Alpaca MCP V2 (61 actions) | N/A | □ **Post-upgrade (screening + option chains)** |
| EdgarTools MCP (free EDGAR) | N/A | □ Post-upgrade |
| Policy plugin (before live keys) | N/A | □ Before any live trading |
| claude-opus-4-7 to Noah catalog | N/A | □ 1 min today |
| xai/grok-3-mini to Noah catalog | N/A | □ 1 min today |
| Discord voice status queries | □ 2026.5.24 stable | N/A |

---

## Trend Analysis — Day 38

**Zero implementations across 38 days of documented research.**

**Josh:** ~25 stable releases behind. No compaction config — even if MEMORY.md is created today, long sessions risk silent data loss. Dead fallback model is an 18-day 1-minute fix. Emoji contradiction in AGENTS.md creates behavioral violations every Discord session. ~120 cumulative findings, 0 resolved.

**Noah:** contextPruning 5m TTL is Day 11 critical against daily market sessions — a 60-second fix undone for 11 consecutive trading days. memory-core is whitelisted but never instantiated (Day 27). IDENTITY.md and USER.md blank for 38 days despite Noah Katz’s full profile in workspace/reports. Alpaca MCP V2 now confirmed at 61 actions with new market screening tools. xAI/Grok pipeline confirmed practical via OAuth profile reuse. ~140 cumulative findings, 0 resolved.

**Both:** 38 sessions of complete statelessness. SOUL.md, AGENTS.md, TOOLS.md remain byte-for-byte identical between a personal assistant and a trading agent. The divide between documented capability and implemented capability now spans 38 days with zero implementations and ~260 cumulative findings across both instances.

**New this morning (2026-05-26):** Josh has no compaction config — MEMORY.md creation alone is insufficient. Alpaca MCP V2 confirmed at 61 actions with market screening. xAI/Grok OAuth reuse makes pre-market X pipeline practical. Subagents enable parallel task execution for both. Node.js 22.19 minimum — check before upgrade. softThresholdTokens 4000 is too low for Noah’s tool-heavy trading sessions.

---

*Generated by AlphaClaw Apex Fleet Research Agent — Morning Scan — 2026-05-26 (Day 38)*
