# Cross-Customer Analysis — AlphaClaw Apex Fleet

**Last Updated:** 2026-05-25 (Morning Scan — Day 37)
**Instances:** Josh (Heather Schwartz, personal assistant) | Noah (Market Catalyst Agent, stock research)
**Scan cadence:** Morning + Evening daily

---

## Day 37 Morning — New Research (2026-05-25)

### Upgrade Target Correction — 2026.5.22 Is Now Stable (Skip 2026.5.20)

The evening scan (Day 37 evening) tracked 2026.5.22-beta.1 as in-train. It graduated to **stable on May 24, 2026**. Both instances should target 2026.5.22 directly, not 2026.5.20.

| Instance | Current | **Corrected Stable Target** | Gap |
|----------|---------|----------------------------|-----|
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

Version gap analysis this morning confirms: **Active Memory plugin shipped in OpenClaw 2026.4.10. Noah is on 2026.4.15.** This plugin is available to Noah right now.

This is the most significant actionable finding of Day 37 morning:
- Noah's agent has run 34+ sessions with zero memory
- Active Memory would begin accumulating trading context immediately
- It reads from MEMORY.md (which also needs to be created — zero risk, 15 min)
- No upgrade required. No restart required.

Contrast with Josh: Josh is on 2026.3.22, below the 2026.4.10 minimum. Active Memory is **not available for Josh until he upgrades.**

| Instance | Active Memory Available? | Blocker |
|----------|--------------------------|--------|
| **Noah** | ✅ **YES — today, no upgrade** | Must create MEMORY.md + enable memory-core entries |
| **Josh** | ❌ Not yet | Requires upgrade to 2026.5.22 first |

---

### iMessage Thumb-Approval Reactions — Josh-Specific Beta Feature

2026.5.24-beta.2 introduces 👍/👎 reactions on iMessage for approve/deny of pending agent actions. This is directly relevant to Josh because iMessage is his primary interface with Heather.

**What changes:**
- Current: Josh types approval responses in iMessage or Discord
- With reactions: Josh reacts 👍 to approve, 👎 to deny — no typing required
- Mobile-native. Zero friction.

**Timeline:** 2026.5.24-beta.2 is in train. Stable ETA ~June 1-4, 2026. Do NOT deploy beta.

**Pre-requisite for Josh:** iMessage bridge must be reconnected (currently `imessage_monitoring_paused: true`). This elevates the priority of resolving the iMessage pause.

Noah: Not applicable (iMessage not in Noah's stack).

---

### Day 37 Morning Fleet State

| Dimension | Josh / Heather | Noah / Market Catalyst |
|---|---|---|
| OpenClaw version | **2026.3.22** | **2026.4.15** |
| Latest stable | **2026.5.22** (corrected) | **2026.5.22** (corrected) |
| Beta train | 2026.5.24-beta.2 | 2026.5.24-beta.2 |
| Gap (releases) | **~25** | **~18** |
| contextPruning TTL | ❌ None configured | 🔴 **5m — Day 11 CRITICAL** |
| memory-core | ❌ Not eligible (needs upgrade) | ⚠️ Allowlisted, **no entries block — Day 26** |
| Active Memory plugin | ❌ Below min version | 🟢 **AVAILABLE NOW — 2026.4.15 ≥ 2026.4.10** |
| MEMORY.md | ❌ Never created — **Day 37** | ❌ Never created — **Day 37** |
| HEARTBEAT.md | ⚠️ Empty | ⚠️ Empty |
| IDENTITY.md | ✅ Heather (partial) | ❌ **Blank template — Day 37** |
| USER.md | ✅ Josh (populated) | ❌ **Blank template — Day 37** |
| iMessage bridge | 🔴 **Paused — blocks 👍👎 reactions** | N/A |
| Dead fallback model | 🔴 **claude-3.5-haiku — Day 17** | N/A |
| xAI/Grok | N/A | 🟢 Available on upgrade to 2026.5.22 |
| Discord streaming | ❌ `"off"` | ❌ Not set |
| skills/gog-cli audit | N/A | ⚠️ **Unaudited — HIGH security in financial env** |
| Cron jobs | ❌ None | ❌ **None — idle during market hours** |
| iMessage reactions (beta) | 🟡 Coming (2026.5.24 stable) | N/A |
| Cumulative findings | **~113** | **~130** |
| Resolved findings | **0** | **0** |
| Days since last implementation | **37** | **37** |

---

### Day 37 Zero-Config Backlog

| Action | Target | Effort | Days Documented | Status |
|--------|--------|--------|-----------------|--------|
| Fix dead claude-3.5-haiku fallback | **Josh** | 1 min | **17 days** | 🔴 DO IT |
| Fix contextPruning 5m → 30m | **Noah** | 60 sec | **11 days** | 🔴 DO IT |
| Add memory-core entries block | **Noah** | 3 min | **26+ days** | 🔴 DO IT |
| Add active-memory plugin | **Noah** | 3 min | **NEW today** | 🟢 DO IT TODAY |
| Create MEMORY.md | **Both** | 15 min each | **37 days** | 🔴 DO IT |
| Fill IDENTITY.md + USER.md | **Noah** | 15 min | **34+ days** | 🔴 DO IT |
| Add opus-4-7 to Noah catalog | **Noah** | 1 min | 5 days | ✅ Easy |
| Enable Discord streaming `progress` | **Both** | 1 min each | 10+ days | ✅ Easy |
| Add HEARTBEAT.md content | **Both** | 5 min each | **37 days** | ✅ Easy |

**Total: ~55 minutes. Zero upgrades required. 0 of this done in 37 days.**

---

## Day 36 Morning — Research Summary (2026-05-24)

### OpenClaw 2026.5.22-beta.1 Released (Now Graduated to Stable)

*[Superseded by Day 37 morning: 2026.5.22 is now stable.]*

Key findings from Day 36 morning that remain relevant:

**Meeting Capture Plugin — Josh-Specific:**
Meeting Notes plugin (Discord voice → transcript → memory) ships in 2026.5.22 stable.
- Discord voice sessions auto-transcribed
- Transcripts feed into memory-core automatically
- Josh's voice calls become searchable by Heather for the first time
- Pre-requisite: upgrade + memory-core + meeting-notes external npm package

**Cron Reliability Hardening — Noah-Specific:**
Cron delivery now routed through modern resolver APIs in 2026.5.22. Pre-market pipeline hardening confirmed. Full cron config template (see Shared Config Snippet Library below).

**Policy/Approval Hardening — Noah Critical:**
Per-channel trading guardrails hardened in 2026.5.22. MUST be configured before Noah ever moves from paper to live trading keys.

---

## Workspace File Gap Analysis (Persistent)

### Files Identical in Both (SHA match)
- `SOUL.md` — SHA 792306ac — generic template in BOTH — a trading agent and a personal assistant using identical soul files is a signal that neither has been personalized
- `AGENTS.md` — SHA 3faead97 — generic template in both
- `TOOLS.md` — SHA 917e2fa8 — template only in both
- `HEARTBEAT.md` — both effectively empty

### Josh Has, Noah Missing
- IDENTITY.md (populated with "Heather") | USER.md (populated with Josh's info)

### Noah Has, Josh Missing
- `workspace/reports/` directory | `skills/gog-cli/` | `gogcli/`

### Missing in Both
- **MEMORY.md** — Day 37 | **memory/ populated directory** — 37 stateless sessions

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

### Noah model catalog (Day 37 morning target)
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

---

## Platform Risk Summary (Active — Day 37)

| Risk | Instance | Severity | Day # | Fix |
|------|----------|----------|-------|-----|
| contextPruning 5m TTL | Noah | CRITICAL | **11** | 60 sec, no restart |
| MEMORY.md never created | Both | CRITICAL | **37** | 15 min, zero risk |
| IDENTITY.md + USER.md blank | Noah | CRITICAL | **34+** | 15 min, zero risk |
| Dead fallback claude-3.5-haiku | Josh | MEDIUM | **17** | 1 min, no restart |
| memory-core not in entries | Noah | HIGH | **26** | 3 min, no restart |
| Active Memory not configured | Noah | HIGH | **NEW** | 5 min, available now |
| iMessage bridge paused | Josh | MEDIUM | **28+** | Investigation needed |
| skills/gog-cli unaudited | Noah | HIGH | **—** | Audit or remove |
| Config backup before upgrade | Both | HIGH | — | Before any change |
| SOUL.md identical (both generic) | Both | MEDIUM | **37** | 30 min total |
| HEARTBEAT.md empty | Both | HIGH | **37** | 10 min total |

---

## Priority Matrix — Fleet-Wide (Day 37 Morning)

### CRITICAL — Right Now (No Upgrade Needed)
| Item | Josh | Noah |
|---|---|---|
| Fix contextPruning 5m → 30m | N/A | 🔴 **60 sec — Day 11** |
| Create MEMORY.md | 🔴 **5 min — Day 37** | 🔴 **15 min — Day 37** |
| Enable memory-core in entries | N/A (upgrade first) | 🔴 **3 min — Day 26** |
| Enable Active Memory plugin | N/A (upgrade first) | 🟢 **5 min — AVAILABLE NOW** |
| Fill IDENTITY.md + USER.md | N/A (done) | 🔴 **15 min — Day 34** |
| Fix dead claude-3.5-haiku fallback | 🔴 **1 min — Day 17** | N/A |

### HIGH — This Week
| Item | Josh | Noah |
|---|---|---|
| Upgrade to OpenClaw 2026.5.22 | 3.22 → 5.22 | 4.15 → 5.22 |
| Add HEARTBEAT.md content | □ 10 min | □ 10 min |
| Enable Discord streaming `progress` | □ 1 min | □ 1 min |
| Audit skills/gog-cli | N/A | □ **HIGH security** |
| Reconnect iMessage bridge | 🟡 Needed for 👍👎 reactions | N/A |

### MEDIUM — Post-Upgrade
| Item | Josh | Noah |
|---|---|---|
| Personalize SOUL.md | □ 30 min | □ 30 min |
| Meeting Capture plugin (Discord voice) | □ Post-upgrade | N/A |
| Active Memory + allowedChatIds | □ Post-upgrade | ✅ Available now |
| Cron pre/post-market jobs | N/A | □ Post-upgrade |

### LOW / OPPORTUNITY — Post-Upgrade
| Item | Josh | Noah |
|---|---|---|
| iMessage 👍👎 reactions | □ 2026.5.24 stable (~June 1-4) | N/A |
| xAI/Grok — X/Twitter signals | N/A | □ **On upgrade (direct alpha edge)** |
| Alpaca MCP V2 (61 actions) | N/A | □ Post-upgrade |
| EdgarTools MCP (free EDGAR) | N/A | □ Post-upgrade |
| Policy plugin (before live keys) | N/A | □ Before any live trading |
| claude-opus-4-7 to Noah catalog | N/A | □ 1 min today |
| Discord voice status queries | □ 2026.5.24 stable | N/A |

---

## Trend Analysis — Day 37

**Zero implementations across 37 days of documented research.**

**Josh:** ~25 stable releases behind. Dead fallback model is a 1-minute fix sitting for 17 days. iMessage paused. MEMORY.md missing 37 days. SOUL.md is the byte-for-byte upstream template — identical to Noah's. ~113 cumulative findings, 0 resolved.

**Noah:** contextPruning 5m TTL is Day 11 critical against daily market sessions — 6 context resets per research session, every day, for 11 days. Active Memory is now confirmed available today without any upgrade. IDENTITY.md and USER.md blank for 34+ days despite Noah Katz's full profile available in workspace/reports. ~130 cumulative findings, 0 resolved.

**Both:** 37 sessions of complete statelessness. SOUL.md, AGENTS.md, TOOLS.md remain byte-for-byte identical between a personal assistant and a trading agent. The divide between documented capability and implemented capability is now approaching 130 findings across 37 days with zero implementations.

**New this morning (2026-05-25):** 2026.5.22 is confirmed stable — upgrade target corrects for both. Active Memory plugin is available to Noah today (no upgrade). iMessage 👍👎 reactions confirmed in beta (Josh-specific). contextPruning Day 11 — market opens in hours.

---

*Generated by AlphaClaw Apex Fleet Research Agent — Morning Scan — 2026-05-25 (Day 37)*
