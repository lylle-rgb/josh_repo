# Cross-Customer Analysis — AlphaClaw Apex Fleet

**Last Updated:** 2026-05-13 (Morning Scan)  
**Instances:** Josh (Heather Schwartz, personal assistant) | Noah (Market Catalyst Agent, stock research)  
**Scan cadence:** Morning + Evening daily

---

## Fleet Overview

| Dimension | Josh / Heather | Noah / Market Catalyst |
|---|---|---|
| OpenClaw version | **2026.3.22** (83 days old) | **2026.4.15** (21 days old) |
| Releases behind stable | **13 releases** | **9 releases** |
| Primary model | `google/gemini-3-flash-preview` | `anthropic/claude-sonnet-4-6` |
| Fallback model | OpenRouter (gemini-2.5-flash, claude-3.5-haiku⚠️) | None configured |
| Model provider | Google + OpenRouter | Anthropic direct |
| memory-core plugin | ❌ Not installed | ⚠️ In allow list, half-configured |
| Compaction config | ❌ None | ✅ Configured |
| contextPruning | ❌ None | ⚠️ 5m TTL (too aggressive) |
| threadBindings | ❌ Not configured | ❌ Not configured |
| Cron retry config | ❌ Not configured | ❌ Not configured |
| HEARTBEAT.md | ⚠️ Exists, empty | ⚠️ Exists, effectively empty |
| MEMORY.md | ❌ Never created | ❌ Never created |
| memory/ directory | ❌ None | ❌ None |
| SOUL.md | ⚠️ Base template, unevolved | ⚠️ Base template, wrong for trading |
| IDENTITY.md | ✅ Partially filled (no avatar) | ❌ **Completely blank** |
| USER.md | ✅ Populated (Josh, LA, founder) | ❌ **Completely blank** |
| TOOLS.md | ⚠️ Template only | ⚠️ Template only |
| AGENTS.md | ✅ Standard (both identical) | ✅ Standard (both identical) |
| Discord groupPolicy | `open` (anyone in guild) | `allowlist` (specific channel) |
| Discord dmPolicy | `open` | `pairing` |
| Discord streaming | `off` | Not set |
| skills/ directory | ❌ None | ✅ `gog-cli` skill |
| workspace/reports/ | ❌ None | ✅ Exists |
| Active intelligence gap | iMessage 18d, email 15d | Catalyst 21d (Q2 Week 3 active) |
| Days with zero implementations | **26** | **26** |

---

## Workspace File Gap Analysis

### Files Present in Both ✅
- `SOUL.md` — identical content in both repos (same SHA: 792306ac)
- `AGENTS.md` — identical content in both repos (same SHA: 3faead97)
- `TOOLS.md` — identical content in both repos (same SHA: 917e2fa8) — both template-only
- `BOOTSTRAP.md` — present in both
- `HEARTBEAT.md` — present in both, both effectively empty

### Files Josh Has, Noah Missing ⚠️
- **IDENTITY.md populated** — Josh has name/creature/vibe/emoji set. Noah’s is blank template. **CRITICAL for Noah.**
- **USER.md populated** — Josh’s has name, timezone, employer, preferences. Noah’s is blank template. **CRITICAL for Noah.**

### Files Noah Has, Josh Missing ⚠️
- **`workspace/reports/`** — Noah has a reports directory for research output. Josh lacks one (would be useful for email summaries, brand reports).
- **`skills/gog-cli/`** — Noah has the Google OAuth CLI skill installed. Josh has Google auth via API key mode and doesn’t have this skill directory.
- **`gogcli/`** — Noah-specific binary/tooling directory.

### Files Missing in Both ❌
- **`MEMORY.md`** — Neither instance has created a long-term memory file. AGENTS.md instructs both to create and maintain this. 26 days without it.
- **`memory/` directory** — Neither instance has any daily memory logs. Both have been operating stateless since deployment.

---

## Configuration Comparison

### Model Strategy

**Josh (Gemini-first with OpenRouter fallback):**
```json
"primary": "google/gemini-3-flash-preview",
"fallbacks": [
  "openrouter/google/gemini-2.5-flash",
  "openrouter/anthropic/claude-3.5-haiku"  // ⚠️ STALE — claude-3.5-haiku is retired
]
```
- ⚠️ `claude-3.5-haiku` fallback should be updated to `openrouter/anthropic/claude-haiku-4-5`
- Gemini 3 Flash Preview is current and appropriate for personal assistant workloads
- No compaction configured — long email+calendar sessions may hit context limits

**Noah (Anthropic direct, no fallback):**
```json
"primary": "anthropic/claude-sonnet-4-6",
"models": {
  "anthropic/claude-opus-4-6": {},
  "anthropic/claude-sonnet-4-6": {}
}
```
- No fallback configured — if Anthropic is down, the agent is completely offline
- `claude-opus-4-7` is now available but not in catalog
- Compaction configured ✅ with `reserveTokensFloor: 40000` and `memoryFlush.softThresholdTokens: 4000`
- contextPruning `ttl: "5m"` — too aggressive for 30-minute research sessions (Finding 8, 26 days)

**Recommendation:** Noah should add an OpenRouter fallback for Anthropic outage resilience. Josh should fix the stale haiku fallback.

---

### Discord Security Posture

| Setting | Josh | Noah | Assessment |
|---|---|---|---|
| `groupPolicy` | `open` | `allowlist` | Noah more secure |
| `dmPolicy` | `open` | `pairing` | Noah more secure |
| `requireMention` | `false` (guild-level) | `false` (channel-level) | Both respond without mention |
| `streaming` | `off` | Not set | Minor difference |
| `inboundWorker.runTimeoutMs` | Not set | `1800000` (30 min) | Noah optimized for long runs |
| `eventQueue.listenerTimeout` | Not set | `120000` (2 min) | Noah has explicit timeout |

**Key difference:** Noah’s allowlist + pairing policy is significantly more locked down. Josh’s open+open posture means any Discord user in the guild can DM Heather and get a response. This is intentional for a personal assistant but means tool override restrictions (Finding 30) are more urgent for Josh.

---

### Memory Architecture

**Josh:**
- No `memory-core` plugin — memory is entirely file-based (MEMORY.md + daily logs)
- File-based memory is functional but requires Heather to manually write/curate
- No compaction = sessions accumulate context without intelligent pruning

**Noah:**
- `memory-core` in `plugins.allow` and `plugins.entries` — but half-configured
- `admin` scope required to fully activate `memory-core` (since 2026.5.7 change)
- `memoryFlush.enabled: true` with `softThresholdTokens: 4000` is configured
- LanceDB vector search not confirmed as provisioned on the VPS
- Neither the dreaming system nor the promotion pipeline is active without `memory-core` fully initialized

**Gap:** Noah has the infrastructure intent for active memory but hasn’t crossed the admin-scope threshold. Josh doesn’t even have the intent — `memory-core` isn’t in the allow list.

---

### New Features Available to Both (Morning Scan Additions)

#### threadBindings (available now in 2026.5.x)
Neither instance has this configured. Both would benefit:

| Instance | threadBindings Value |
|---|---|
| Josh / Heather | Sub-agent delegation for research tasks without polluting the main channel |
| Noah / Market Catalyst | Parallel catalyst pipelines in isolated threads; long A2A runs don’t block the channel |

**Config delta between instances:**
- Josh: `idleHours: 24, maxAgeHours: 168` (threads expire after 1 week)
- Noah: `idleHours: 48, maxAgeHours: 336` (threads persist 2 weeks — catalyst research spans days)

#### Retry-Aware Cron (available now in 2026.5.x)
Neither instance has `cron.retry` configured. Both need it before HEARTBEAT.md can be relied on.

The retry config is identical for both:
```json
"cron": {
  "retry": {
    "maxAttempts": 3,
    "backoffMs": [60000, 120000, 300000],
    "retryOn": ["rate_limit", "overloaded", "network", "server_error"]
  }
}
```

For Josh: protects email/calendar/iMessage cron checks from Gemini rate limits.  
For Noah: protects pre-market catalyst scans from EDGAR and Alpaca rate limits.

---

## Priority Matrix — Fleet-Wide

### CRITICAL (Fix Now — Zero Config Required)
| Item | Josh | Noah |
|---|---|---|
| Fill IDENTITY.md | ⚠️ Partial (avatar missing) | ❌ **Blank — Day 26** |
| Fill USER.md | ✅ Done | ❌ **Blank — Day 26** |
| Resume intelligence monitoring | iMessage/email (18d/15d dark) | Catalyst scan (21d gap) |

### HIGH (Unblock Everything Else)
| Item | Josh | Noah |
|---|---|---|
| Update OpenClaw | 2026.3.22 → 2026.5.7 (13 releases) | 2026.4.15 → 2026.5.7 (9 releases) |
| Populate HEARTBEAT.md | Empty — 26 days | Empty — 26 days |
| Create MEMORY.md | Never created | Never created |
| Create memory/ directory | Never created | Never created |

### MEDIUM (Post-Update)
| Item | Josh | Noah |
|---|---|---|
| threadBindings config | Add to session | Add to session (longer TTL) |
| Cron retry config | Add to openclaw.json | Add to openclaw.json |
| memory-core | Install plugin | Fix admin scope |
| Stale fallback | Fix claude-3.5-haiku | Add OpenRouter fallback |
| contextPruning TTL | N/A | 5m → 30m |
| SOUL.md evolution | Never evolved | Wrong persona for trading |

### LOW / OPPORTUNITY (Post-2026.5.10-stable)
| Item | Josh | Noah |
|---|---|---|
| Per-agent tool overrides | Discord group security | Alpaca DM-only trade execution |
| /context map | Token visibility in long sessions | Validate TTL fix |
| A2A 20-turn pipelines | Sub-agent delegation | Full catalyst autonomous pipeline |
| extractStructuredWithModel() | N/A | SEC PDF structured extraction |

---

## Implementation Sequence Recommendation — Fleet-Wide

**For both instances, the correct order is:**

1. **Resume intelligence monitoring** (Finding 38 for Noah, email/iMessage for Josh) — zero config, maximum immediate value
2. **Fill missing identity files** (IDENTITY + USER for Noah — 10 min of Discord conversation)
3. **Update OpenClaw to 2026.5.7** — unlocks all 5.x features for both instances
4. **Add cron retry config** to both openclaw.json files
5. **Enable threadBindings** in both openclaw.json files
6. **Populate HEARTBEAT.md** for both instances
7. **Create MEMORY.md + memory/ scaffold** for both instances
8. **Fix memory-core** (Josh: install; Noah: resolve admin scope)
9. **Post-2026.5.10-stable:** Apply tool overrides, A2A pipelines, /context map

---

## Trend Analysis — 26 Days

This fleet has received **zero implementations** across 26 days of documented research. The gap between what both instances are capable of and what they’re actually doing widens daily:

- Josh’s version gap was 7 releases on Day 1. It is now **13 releases**.
- Noah’s catalyst intelligence gap was 0 on Day 1. It is now **21 days** into an active Q2 earnings season.
- Neither instance has a single memory file — both wake up completely stateless every session.
- Both HEARTBEAT.md files are empty — neither instance does any proactive work.

The research is thorough. The blocker is execution. No new research finding changes the priority order: update → heartbeat → memory. Everything else follows from those three steps.

---

## Shared Config Snippet Library

Config blocks that should be applied to **both** instances:

### threadBindings (session level)
```json
"session": {
  "threadBindings": {
    "enabled": true,
    "spawnSessions": true
  }
}
```

### cron retry
```json
"cron": {
  "retry": {
    "maxAttempts": 3,
    "backoffMs": [60000, 120000, 300000],
    "retryOn": ["rate_limit", "overloaded", "network", "server_error"]
  }
}
```

---
*Generated by AlphaClaw Apex Fleet Research Agent — Morning Scan — 2026-05-13*
