# Cross-Customer Analysis — AlphaClaw Apex Fleet

**Last Updated:** 2026-05-30 Morning (Day 42)
**Instances:** Josh (Heather Schwartz, personal assistant) | Noah (Market Catalyst Agent, stock research)
**Scan cadence:** Morning + Evening daily

---

## Day 42 Morning — New Research (2026-05-30)

### Overnight Release: OpenClaw 2026.5.28-beta.3

No new stable release. Current upgrade target for both instances remains **2026.5.27**.

| Instance | Current | Upgrade Target | Days Behind |
|----------|---------|----------------|-------------|
| Josh | 2026.3.22 | 2026.5.27 | **70 days** |
| Noah | 2026.4.15 | 2026.5.27 | **45 days** |

`2026.5.28-beta.3` released overnight. Expected stable promotion: ~2026-06-08. Key changes:
- **Session locks release on timeout abort** — directly relevant to Noah's 30-min pre-market sessions
- **iMessage reactions/approvals channel delivery fix** — relevant for Josh post-upgrade
- **Source-provider chunks in transcript infrastructure** — catalyst provenance for Noah
- **Startup scan deduplication + package optimization** — faster cold starts for both

Do not target beta. Upgrade to 2026.5.27 first, re-evaluate 2026.5.28 when stable.

---

### JOSH-78: memory-core Gemini Embeddings — No New API Keys Needed

A critical planning update for Josh's post-upgrade memory stack. `memory-core` supports Google as the embedding provider (`google/text-embedding-004`), and Josh already has `google:default` credentials configured in `openclaw.json`. This means the full post-upgrade memory stack runs entirely on existing credentials — no OpenAI API key required.

**Post-upgrade addition to `agents.defaults` in `openclaw.json`:**
```json
"memorySearch": {
  "provider": "google",
  "model": "text-embedding-004"
}
```

This changes the post-upgrade plan (previously assumed OpenAI embeddings as default). The complete memory stack becomes:
- Primary model: `google/gemini-3-flash-preview` ✅ existing
- Fallback chain: Gemini 3.5 Flash → Gemini 3.1 Flash Lite → Gemini 2.5 Flash ✅ planned
- Embedding provider: `google/text-embedding-004` ✅ no new keys

---

### NOAH-88: memory-core "Dreaming" — Updated Config Block

The `memory-core` plugin includes a dreaming subsystem for autonomous off-hours memory consolidation. The updated Noah config block (supersedes Day 41 version in the shared config library below):

```json
"memory-core": {
  "enabled": true,
  "config": {
    "dreaming": {
      "enabled": true,
      "frequencyHours": 6,
      "model": "anthropic/claude-haiku-4-5-20251001",
      "maxEntriesPerRun": 20
    },
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

Using Haiku as the dreaming model keeps off-hours consolidation cost minimal. `halfLifeDays: 14` is appropriate for a trading agent — catalyst signals decay quickly.

---

### Day 41 Evening Recap — Key Additions (Not in Prior Morning Scan)

**Josh (JOSH-71 through JOSH-76):**
- Beta 2026.5.28-beta.1/.2 detected (now superseded by beta.3)
- Active Memory Plugin confirmed available post-upgrade (new in 2026.4.12)
- `workspace/memory/inbox-state.json` read: iMessage paused confirmed, email active
- Google connectivity is API key mode (not gog/OAuth) — TOOLS.md should document this
- 70 days of email activity with zero persistent memory (CRITICAL escalation)
- SEC AI monitoring validates heartbeat urgency

**Noah (NOAH-80 through NOAH-86):**
- **gog-cli confirmed as Google Workspace CLI** (Gmail/Calendar/Drive/Sheets/Tasks/Contacts) authenticated to `Ngkatz.ai@gmail.com` — full capability, zero use for 44 days
- `gmailWatch.enabled: false` — no live email push, EDGAR alerts not reaching agent
- `sec-filing-watcher` skill confirmed in OpenClaw marketplace (upgrade dependency)
- USER.md identity now confirmed from gogcli/state.json: Noah Katz, Ngkatz.ai@gmail.com
- Active Memory plugin available since day 1 of Noah's current version (2026.4.12 ≤ 2026.4.15)
- contextPruning TTL escalated to Day 15 (now Day 16 as of this morning)

---

## Day 42 Fleet Comparison (Full)

| Dimension | Josh / Heather | Noah / Market Catalyst |
|---|---|---|
| OpenClaw version | 2026.3.22 | 2026.4.15 |
| Latest stable | **2026.5.27** | **2026.5.27** |
| Gap (days behind) | **70 days** | **45 days** |
| Latest beta | 2026.5.28-beta.3 | 2026.5.28-beta.3 |
| compaction config | 🔴 **MISSING — Day 42** | ✅ Configured (softThresholdTokens too low at 4000) |
| contextPruning TTL | ❌ Not set | 🔴 **5m — DAY 16 CRITICAL** |
| memory-core | ❌ Not in allow list (version-gated until upgrade) | 🔴 Allowlisted, **no entries — Day 45** |
| Active Memory plugin | ❌ Version-gated (< 2026.4.10) | 🟢 **Eligible now — not configured** |
| MEMORY.md | ❌ **Day 70** | ❌ **Day 45** |
| HEARTBEAT.md | ⚠️ Empty (3 comment lines) | 🔴 **Structurally broken (fenced code block) — Day 45** |
| IDENTITY.md | ✅ Heather (name set, template artifacts remain) | ❌ **Blank template — Day 45** |
| USER.md | ✅ Josh (well-populated, best file in repo) | ❌ **Blank template — identity confirmed via gogcli** |
| SOUL.md | ⚠️ Generic template (SHA: 792306ac — identical) | ⚠️ Generic template (SHA: 792306ac — identical) |
| AGENTS.md | ⚠️ Emoji contradiction vs USER.md | ⚠️ No trading rules, no gog-cli reference |
| TOOLS.md | ⚠️ Blank examples only | 🔴 Blank — gog-cli invisible — Day 45 |
| Dead fallback model | 🔴 claude-3.5-haiku → replace with Gemini 3.5 Flash chain | N/A |
| Gemini 3.5 Flash fallback | 🟢 Update needed | N/A |
| Gemini embeddings for memory-core | 🟢 **Confirmed: google/text-embedding-004 (no new keys)** | N/A (uses Anthropic) |
| memory-core dreaming | 🟡 Available post-upgrade | 🟢 **Config ready (NOAH-88 — Haiku model)** |
| Google Workspace (gog-cli) | ❌ Not installed | 🔴 **Installed + authed (Ngkatz.ai@gmail.com), unused Day 45** |
| gmailWatch | N/A | 🔴 **Disabled — no live EDGAR alert delivery** |
| iMessage bridge | 🔴 Paused — fix in 2026.5.27 | N/A |
| sec-filing-watcher | N/A | 🟢 In marketplace (upgrade dependency) |
| Session lock fix | 🟡 In 2026.5.28-beta.3 (stable ~2026-06-08) | 🔴 **Relevant NOW (30-min sessions held by stale locks)** |
| iMessage reactions fix | 🟢 In 2026.5.28-beta.3 (post-upgrade) | N/A |
| Source provenance transcripts | 🟡 Available post-2026.5.28 | 🟢 Audit trail value — post-2026.5.28 |
| OTEL v2 observability | 🟡 Available post-2026.5.27 upgrade | 🟢 High value post-upgrade |
| Alpaca MCP V2 | N/A | 🟢 Available post-upgrade (V1 compat BROKEN) |
| EdgarTools MCP | N/A | 🟢 Available post-upgrade |
| Cron jobs | ❌ None | ❌ **None — idle during market hours** |
| strictInlineEval | Not set | 🔴 `false` in financial env |
| Cumulative findings | **~135** | **~167** |
| Resolved findings | **0** | **0** |
| Days since last implementation | **42** | **42** |

---

## Shared Config Snippet Library (Current — Day 42)

### contextPruning — Noah (CRITICAL — apply now, Day 16)
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

### memory-core — Noah (updated Day 42 — includes dreaming)
```json
"memory-core": {
  "enabled": true,
  "config": {
    "dreaming": {
      "enabled": true,
      "frequencyHours": 6,
      "model": "anthropic/claude-haiku-4-5-20251001",
      "maxEntriesPerRun": 20
    },
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

### memory-core — Josh (apply post-upgrade, with Gemini embeddings — Day 42 update)
```json
"memory-core": {
  "enabled": true,
  "config": {
    "dreaming": {
      "enabled": true,
      "frequencyHours": 12,
      "model": "google/gemini-3-flash-preview",
      "maxEntriesPerRun": 15
    },
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

### memorySearch (Gemini embeddings) — Josh (add to agents.defaults post-upgrade)
```json
"memorySearch": {
  "provider": "google",
  "model": "text-embedding-004"
}
```

### Josh fallback chain — Day 41 (Gemini 3.5 Flash leads)
```json
"fallbacks": [
  "openrouter/google/gemini-3-5-flash",
  "openrouter/google/gemini-3-1-flash-lite-preview",
  "openrouter/google/gemini-2.5-flash"
]
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

## Workspace File Gap Analysis (Day 42)

### Files Identical in Both Repos (Zero Customization)
| File | SHA | State |
|------|-----|-------|
| `SOUL.md` | 792306ac | Generic upstream template — byte-for-byte identical in both repos |
| `AGENTS.md` | 3faead97 | Generic template — no trading rules, no gog-cli ref, no emoji override |
| `TOOLS.md` | 917e2fa8 | Blank example template only |

### Josh Has, Noah Missing
- `IDENTITY.md` (populated — "Heather Schwartz")
- `USER.md` (populated — Josh Meyers, LA, Bliss/Oben, NO emoji reactions)
- iMessage channel (paused but configured — fix in 2026.5.27)

### Noah Has, Josh Missing
- `workspace/reports/ae-target-companies-2026-04-22.md` (21KB, 38 days old, never referenced in memory)
- `skills/gog-cli/` (Google Workspace CLI — full Gmail/Calendar/Drive/Sheets/Tasks/Contacts — unused Day 45)
- `gogcli/state.json` (authenticated to Ngkatz.ai@gmail.com — gmailWatch: false)
- Compaction config (present but softThresholdTokens too low; Josh has nothing)
- memory-core in allow list (not in entries)
- Active Memory plugin eligibility (Josh version-gated until upgrade)
- `workspace/reports/` directory

### Missing in Both
- `MEMORY.md` — Josh: Day 70, Noah: Day 45
- Functional `HEARTBEAT.md` (Josh: empty; Noah: broken fenced code block)
- Populated `TOOLS.md`
- Customized `SOUL.md`
- Cron job automation

---

## Active Behavioral Gaps (Day 42)

**Josh — AGENTS.md Emoji Contradiction (Day 42):**
`USER.md`: `STRICT: DO NOT SEND EMOJI REACTIONS TO MESSAGES.`
`AGENTS.md` (stock template): "React Like a Human!" with detailed emoji reaction instructions.
Direct contradiction active in every Discord session. One-line GitHub fix.

**Josh — Zero Memory Retention (Day 70):**
`workspace/memory/` has only `inbox-state.json` and `onboarding-google.md`. Zero daily notes. Zero MEMORY.md. 70 days of email + calendar interactions have produced no persistent memory. Every session starts cold.

**Noah — gog-cli Invisible (Day 45):**
`TOOLS.md` is blank examples. `AGENTS.md` is stock template. The agent cannot discover or use gog-cli — its installed, authenticated Google Workspace tool providing Gmail, Calendar, Sheets, Drive, Tasks access. All dark.

**Noah — gmailWatch Disabled (Day 45):**
Real-time email push off. EDGAR filing alerts (if subscribed at SEC.gov), Alpaca confirmations, and broker research are not received in real time. Polling workaround possible via HEARTBEAT.md — but HEARTBEAT.md is structurally broken.

**Noah — contextPruning 5m TTL (Day 16):**
Every pre-market session resets every 5 minutes. One-line JSON edit. 16 days unresolved.

**Noah — No Trading Guardrails in AGENTS.md (Day 42):**
No paper-only rule. No audit trail requirement. No ET timezone. No gog-cli reference. The trading agent has zero custom instructions for its core function.

---

## Zero-Config Backlog (Day 42)

| Action | Target | Effort | Days Documented | Status |
|--------|--------|--------|-----------------|--------|
| Fix contextPruning 5m → 30m | **Noah** | 60 sec | **16 days** | 🔴 DO IT NOW |
| Update fallback chain to Gemini 3.5 Flash | **Josh** | 1 min | Day 42 | 🔴 DO IT |
| Add compaction config | **Josh** | 2 min | 6 days | 🔴 DO IT |
| Enable memory-core in entries (with dreaming) | **Noah** | 3 min | **45 days** | 🔴 DO IT |
| Add Active Memory config | **Noah** | 3 min | **Day 42** | 🟢 DO IT (after memory-core) |
| Increase softThresholdTokens to 10000 | **Noah** | 1 min | 6 days | 🟢 DO IT |
| Create MEMORY.md | **Both** | 15 min each | Josh: **70 days** / Noah: **45 days** | 🔴 DO IT |
| Fill IDENTITY.md + USER.md | **Noah** | 15 min | **45 days** | 🔴 DO IT |
| Fix AGENTS.md emoji contradiction | **Josh** | 2 min | **42 days** | 🔴 DO IT |
| Add trading rules + gog-cli to AGENTS.md | **Noah** | 30 min | 6 days | 🟢 DO IT |
| Replace HEARTBEAT.md (broken code block) | **Noah** | 5 min | **45 days** | 🔴 DO IT |
| Populate HEARTBEAT.md | **Josh** | 5 min | **42 days** | 🟢 Easy |
| Populate TOOLS.md with gog-cli reference | **Noah** | 15 min | **Day 42** | 🔴 DO IT |
| Populate TOOLS.md with env data (incl. API key note) | **Josh** | 10 min | **42 days** | ✅ Easy |
| Add memorySearch Gemini embeddings to agents.defaults | **Josh** | 1 min | **Day 42 NEW** | ✅ Easy (post-upgrade) |
| Delete BOOTSTRAP.md | **Both** | 30 sec | 3 days | ✅ Easy |

**Total: ~100 minutes of GitHub edits. Zero implementations in 42 days.**

---

## Platform Risk Summary (Day 42)

| Risk | Instance | Severity | Day # |
|------|----------|----------|-------|
| contextPruning 5m TTL — destroys pre-market sessions | Noah | CRITICAL | **16** |
| MEMORY.md never created | Both | CRITICAL | Josh: **70** / Noah: **45** |
| IDENTITY.md + USER.md blank | Noah | CRITICAL | **45** |
| gog-cli invisible to agent | Noah | CRITICAL | **45** |
| 70 days email activity, zero persistent memory | Josh | CRITICAL | **1** |
| gmailWatch disabled — EDGAR alerts lost | Noah | HIGH | **45** |
| compaction config missing | Josh | HIGH | 6 |
| memory-core not in entries | Noah | HIGH | **45** |
| HEARTBEAT.md broken structure | Noah | HIGH | **45** |
| HEARTBEAT.md empty | Josh | HIGH | **42** |
| Active Memory not configured | Noah | HIGH | **Day 42** |
| Dead fallback (update to Gemini 3.5 Flash chain) | Josh | MEDIUM | 23 |
| AGENTS.md emoji contradiction | Josh | MEDIUM | **42** |
| AGENTS.md missing trading guardrails | Noah | MEDIUM | 6 |
| softThresholdTokens too low | Noah | MEDIUM | 6 |
| strictInlineEval: false in financial env | Noah | MEDIUM | 4 |
| iMessage bridge paused | Josh | MEDIUM | **34+** |

---

## Trend Analysis — Day 42

**Zero implementations across 42 days of documented research.**

Day 42 morning adds three new findings per instance:

**Josh (JOSH-77, 78, 79):**
1. beta.3 overnight — iMessage reactions fix + faster cold starts (useful post-upgrade context)
2. Gemini embeddings for memory-core confirmed — entire post-upgrade memory stack runs on existing Google credentials, no new API keys
3. Package optimization in 2026.5.28 — faster cold starts when AlphaClaw restarts Heather

**Noah (NOAH-87, 88, 89):**
1. beta.3 overnight — session lock release directly solves the ghost-session problem for 30-min pre-market sessions
2. memory-core dreaming — autonomous off-hours memory consolidation; updated config block uses Haiku for cost efficiency
3. Package optimization — faster 6:30 AM cold starts once cron is configured

**Day 41 evening additions (JOSH-71–76, NOAH-80–86) recapped above.**

**Cumulative: ~135 Josh findings, ~167 Noah findings, 0 resolved.**

---

*Generated by AlphaClaw Apex Fleet Research Agent — Morning Scan — 2026-05-30 (Day 42)*
