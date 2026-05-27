# Cross-Customer Analysis — AlphaClaw Apex Fleet

**Last Updated:** 2026-05-27 Morning (Day 39)
**Instances:** Josh (Heather Schwartz, personal assistant) | Noah (Market Catalyst Agent, stock research)
**Scan cadence:** Morning + Evening daily

---

## Day 39 Morning — New Research (2026-05-27)

### OpenClaw 2026.5.26-beta.2 Released This Morning

OpenClaw shipped `2026.5.26-beta.2` at 05:46 UTC today. **Pre-release — do not deploy.** This establishes what the next stable will contain, ~2 days out.

| Change | Josh/Heather | Noah/Trading |
|--------|-------------|--------------|
| Gateway startup avoids repeated scans | Faster Discord first-reply | **HIGH** — pre-market cron cold-starts faster |
| Visible replies separated from follow-up | Less perceived lag | Briefing in Discord while EDGAR processes |
| Approvals fix (trusted approval runtime) | Email send confirmations reliable | **HIGH** — trade confirmations won't expire |
| Agents fail closed on provider ambiguity | Gemini routing deterministic | **HIGH** — Anthropic-only routing stays correct |
| @openclaw/fs-safe 0.2.7 | More reliable MEMORY.md writes | More reliable trade log writes |

**Upgrade path for both stays:** target 2026.5.22 stable now. Monitor 2026.5.26 graduation.

---

### AI Memory Is Now a Baseline Expectation — Day 39

The mem0.ai State of AI Agent Memory 2026 report confirms: **memory is no longer a differentiator, it is a baseline expectation.** The market reached $6.27B in 2026.

Both instances remain completely stateless at Day 39. Neither has MEMORY.md. Neither retains context across sessions.

| Dimension | Josh | Noah |
|-----------|------|------|
| Sessions completed | ~39 | ~39 |
| MEMORY.md | ❌ Never created | ❌ Never created |
| memory/ directory | ❌ Empty | ❌ Empty |
| memory-core active | ❌ Pre-upgrade | ❌ Allowlisted, not in entries |
| Session statelessness | Complete | Complete + disrupted by 5m TTL |

The gap between what these agents could retain vs. what they actually retain has grown for 39 consecutive days.

---

### AlphaClaw exec-approval Security — Both Instances

AlphaClaw (recent release) seeds permissive exec-approval defaults after OpenClaw 2026.4.1 tightened exec security policies.

| Dimension | Josh | Noah |
|-----------|------|------|
| `tools.exec.security` | Not set explicitly | `"full"` |
| `tools.exec.strictInlineEval` | Not set | `false` |
| Seeded defaults impact | Unknown — check post-upgrade | Known — "full" but strictInlineEval off |

**Noah's `strictInlineEval: false` should be reviewed post-upgrade** — a trading agent with Alpaca order execution access should have stricter inline eval controls.

---

### Discord Community Best Practices — Both Instances

Community guidance from the OpenClaw Discord channel research:

| Practice | Josh Status | Noah Status |
|----------|------------|------------|
| `openclaw doctor` post-upgrade | Add to checklist | Add to checklist |
| `openclaw channels status --probe` | Run after upgrade | Run, verify `1496556746444112173` |
| Channel scope (start narrow) | `requireMention: false` — monitor rate limits | Single channel allowlisted — correct |
| Rate limit awareness | ⚠️ requireMention: false in active guild | ⚠️ High-volume EDGAR sessions |
| Privileged Gateway Intents check | Verify Message Content Intent | Verify Message Content Intent |

---

## Day 39 Fleet Comparison (Full)

| Dimension | Josh / Heather | Noah / Market Catalyst |
|---|---|---|
| OpenClaw version | 2026.3.22 | 2026.4.15 |
| Latest stable | **2026.5.22** | **2026.5.22** |
| Latest beta | 2026.5.26-beta.2 | 2026.5.26-beta.2 |
| Gap (days behind stable) | **~65 days** | **~42 days** |
| Upgrade overdue | **Day 6** | **Day 6** |
| compaction config | 🔴 **MISSING — Day 39** | ✅ Configured |
| contextPruning TTL | ❌ Not set | 🔴 **5m — Day 13 CRITICAL** |
| softThresholdTokens | N/A | ⚠️ 4000 (rec: 10000) |
| memory-core | ❌ Not eligible (pre-upgrade) | 🔴 Allowlisted, **no entries block — Day 28** |
| Active Memory plugin | ❌ Pre-upgrade | 🟢 Available (v2026.4.15 ≥ 2026.4.10) |
| MEMORY.md | ❌ **Day 39** | ❌ **Day 39** |
| HEARTBEAT.md | ⚠️ Empty | 🔴 **Structurally broken — Day 39** |
| IDENTITY.md | ✅ Heather (populated) | ❌ **Blank template — Day 39** |
| USER.md | ✅ Josh (populated) | ❌ **Blank template — Day 39** |
| SOUL.md | ⚠️ Generic template | ⚠️ Generic template |
| AGENTS.md | ⚠️ Emoji contradiction | ⚠️ No trading guardrails |
| TOOLS.md | ⚠️ Blank template | ⚠️ Blank template |
| Dead fallback model | 🔴 claude-3.5-haiku — Day 19 | N/A |
| iMessage bridge | 🔴 Paused | N/A |
| xAI/Grok pipeline | N/A | 🟢 Available post-upgrade |
| Alpaca MCP V2 (61 actions) | N/A | 🟢 Available post-upgrade |
| EdgarTools MCP | N/A | 🟢 Available post-upgrade |
| Discord streaming | ❌ `"off"` | ❌ Not set |
| Discord buttons/dropdowns | 🟡 Post-upgrade | 🟡 Post-upgrade |
| Discord rate limit risk | ⚠️ requireMention:false | ⚠️ High-volume sessions |
| Cron jobs | ❌ None | ❌ **None — idle during market hours** |
| strictInlineEval | Not set | 🔴 `false` in financial env |
| skills/gog-cli audit | N/A | ⚠️ Unaudited |
| Next beta | 2026.5.26-beta.2 (do not deploy) | 2026.5.26-beta.2 (do not deploy) |
| Cumulative findings | **~125** | **~145** |
| Resolved findings | **0** | **0** |
| Days since last implementation | **39** | **39** |

---

## Shared Config Snippet Library (Current)

### contextPruning — Both
```json
"contextPruning": {
  "mode": "cache-ttl",
  "ttl": "30m"
}
```

### compaction — Josh (add) / Noah (update threshold)
```json
// Josh: add to agents.defaults
"compaction": {
  "reserveTokensFloor": 30000,
  "memoryFlush": { "enabled": true, "softThresholdTokens": 6000 }
}

// Noah: update existing softThresholdTokens from 4000 to:
"softThresholdTokens": 10000
```

### memory-core entries — Noah (apply now, no upgrade)
```json
"memory-core": {
  "enabled": true,
  "config": { "deduplication": true, "temporalDecay": true }
}
```

### Active Memory — Noah (available now on 2026.4.15)
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

### Josh fallback fix (no restart)
```json
"fallbacks": [
  "openrouter/google/gemini-3.1-flash-lite-preview",
  "openrouter/google/gemini-2.5-flash",
  "openrouter/anthropic/claude-haiku-4-5"
]
```

### Noah model catalog additions (no upgrade needed)
```json
"models": {
  "anthropic/claude-opus-4-7": {},
  "anthropic/claude-opus-4-6": {},
  "anthropic/claude-sonnet-4-6": {},
  "xai/grok-3-mini": {}
}
```

### Cron jobs — Noah (post-upgrade to 2026.5.22)
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

### Subagents — Both (post setup)
```json
// Josh: parallel heartbeat workers
"subagents": {
  "runTimeoutSeconds": 60,
  "model": { "primary": "google/gemini-3-flash-preview" }
}

// Noah: parallel sector scan workers
"subagents": {
  "runTimeoutSeconds": 120,
  "model": { "primary": "anthropic/claude-haiku-4-5" }
}
```

### Upgrade checklist — Both
```bash
cp openclaw.json openclaw.json.bak-pre-5.22
node --version  # Must be >= v22.19.0
# Upgrade via AlphaClaw UI
openclaw doctor
openclaw channels status --probe
```

---

## Workspace File Gap Analysis (Persistent — Day 39)

### Files Identical in Both Repos (Zero Customization)
| File | State |
|------|-------|
| `SOUL.md` | Generic upstream template — byte-for-byte identical |
| `AGENTS.md` | Generic template — no trading rules, no emoji override |
| `TOOLS.md` | Blank template — fake examples only |
| `HEARTBEAT.md` | Both effectively non-functional (one empty, one structurally broken) |

### Josh Has, Noah Missing
- `IDENTITY.md` (populated — "Heather")
- `USER.md` (populated — Josh Meyers, LA, Bliss/Oben HiFi, NO emoji reactions)

### Noah Has, Josh Missing
- `workspace/reports/` (AE report with USER.md/IDENTITY.md source material — unused for 39 days)
- `skills/gog-cli/` (unaudited in financial environment)
- `gogcli/`
- Compaction config (misconfigured but present)
- memory-core in allow list (not in entries — Day 28)

### Missing in Both
- `MEMORY.md` — Day 39
- `memory/` populated directory

### Active Behavioral Gaps

**Josh — AGENTS.md Emoji Contradiction (Day 39):**
USER.md rule: `STRICT: DO NOT SEND EMOJI REACTIONS`. AGENTS.md "React Like a Human!" section explicitly instructs Heather to use emoji reactions. Direct contradiction producing behavioral violations in every Discord session.

**Noah — No Trading Guardrails in AGENTS.md (Day 39):**
No "Paper trading only" rule. No audit trail requirement. No thesis-before-trade rule. A trading agent with order execution access operating on a generic personal assistant AGENTS.md is a behavioral risk that activates the moment Alpaca MCP is installed.

---

## Zero-Config Backlog (Day 39)

| Action | Target | Effort | Days Documented | Status |
|--------|--------|--------|-----------------|--------|
| Fix contextPruning 5m → 30m | **Noah** | 60 sec | **13 days** | 🔴 DO IT NOW |
| Add compaction + contextPruning config | **Josh** | 2 min | **3 days** | 🔴 DO IT |
| Add memory-core entries block | **Noah** | 3 min | **28 days** | 🔴 DO IT |
| Increase softThresholdTokens to 10000 | **Noah** | 1 min | 3 days | 🟢 DO IT |
| Enable Active Memory plugin | **Noah** | 3 min | 3 days | 🟢 DO IT |
| Create MEMORY.md | **Both** | 15 min each | **39 days** | 🔴 DO IT |
| Fill IDENTITY.md + USER.md | **Noah** | 15 min | **39 days** | 🔴 DO IT |
| Fix dead claude-3.5-haiku fallback | **Josh** | 1 min | **19 days** | 🔴 DO IT |
| Fix AGENTS.md emoji contradiction | **Josh** | 2 min | **39 days** | 🔴 DO IT |
| Add trading rules to AGENTS.md | **Noah** | 30 min | 3 days | 🟢 DO IT |
| Replace HEARTBEAT.md (broken) | **Noah** | 5 min | **39 days** | 🔴 DO IT |
| Populate HEARTBEAT.md | **Josh** | 5 min | **39 days** | 🟢 Easy |
| Populate TOOLS.md | **Both** | 10 min | **39 days** | ✅ Easy |
| Enable Discord streaming `progress` | **Both** | 1 min | 12+ days | ✅ Easy |
| Add claude-opus-4-7 to Noah catalog | **Noah** | 1 min | 7 days | ✅ Easy |

**Total: ~75 minutes. Zero upgrades required. 0 of this done in 39 days.**

---

## Platform Risk Summary (Active — Day 39)

| Risk | Instance | Severity | Day # |
|------|----------|----------|-------|
| contextPruning 5m TTL — market open | Noah | CRITICAL | **13** |
| MEMORY.md never created | Both | CRITICAL | **39** |
| IDENTITY.md + USER.md blank | Noah | CRITICAL | **39** |
| compaction config missing | Josh | HIGH | **3** |
| memory-core not in entries | Noah | HIGH | **28** |
| Dead fallback claude-3.5-haiku | Josh | MEDIUM | **19** |
| AGENTS.md emoji contradiction | Josh | MEDIUM | **39** |
| AGENTS.md missing trading guardrails | Noah | MEDIUM | **3** |
| HEARTBEAT.md broken structure | Noah | HIGH | **39** |
| softThresholdTokens too low | Noah | MEDIUM | **3** |
| strictInlineEval: false in financial env | Noah | MEDIUM | NEW |
| skills/gog-cli unaudited | Noah | HIGH | 5+ days |
| iMessage bridge paused | Josh | MEDIUM | **28+** |
| Discord rate limit awareness | Both | LOW | NEW |
| Node.js 22.19 pre-check | Both | LOW | 3 days |

---

## Trend Analysis — Day 39

**Zero implementations across 39 days of documented research.**

OpenClaw 2026.5.26-beta.2 shipped this morning — the next stable release is ~2 days out. Both instances need to upgrade to 2026.5.22 before that arrives, or risk being 3+ stable releases behind simultaneously.

AI memory became a $6.27B market in 2026. Memory is now a baseline expectation, not a feature. Both instances continue to operate in complete statelessness — 39 sessions with no persistent context.

Josh: 65 days behind latest stable. Missing compaction config (3 days). Dead fallback model (19 days). Emoji contradiction (39 days). ~125 cumulative findings, 0 resolved.

Noah: 42 days behind latest stable. contextPruning 5m TTL now Day 13 critical — every trading session is disrupted. memory-core whitelisted but dormant for 28 days. IDENTITY/USER blank for 39 days despite source material in workspace/reports. strictInlineEval: false is a security gap in a financial environment. ~145 cumulative findings, 0 resolved.

**New this morning (2026-05-27):** OpenClaw 2026.5.26-beta.2 released — Gateway startup improvements and fail-closed provider routing impact both instances. AI memory is now a baseline industry expectation. Discord rate limiting becomes relevant as both agents become more active. AlphaClaw exec-approval seeding raises security questions for Noah's financial environment.

---

*Generated by AlphaClaw Apex Fleet Research Agent — Morning Scan — 2026-05-27 (Day 39)*
