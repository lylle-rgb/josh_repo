# Cross-Customer Analysis — AlphaClaw Apex Fleet

**Last Updated:** 2026-05-14 (Morning Scan — Day 27)  
**Instances:** Josh (Heather Schwartz, personal assistant) | Noah (Market Catalyst Agent, stock research)  
**Scan cadence:** Morning + Evening daily

---

## Fleet Overview

| Dimension | Josh / Heather | Noah / Market Catalyst |
|---|---|---|
| OpenClaw version | **2026.3.22** (84 days old) | **2026.4.15** (22 days old) |
| Releases behind stable | **13 releases** | **9 releases** |
| Primary model | `google/gemini-3-flash-preview` | `anthropic/claude-sonnet-4-6` |
| Fallback model | OpenRouter (gemini-2.5-flash, claude-3.5-haiku ⚠️ retired) | ❌ **None — Anthropic SPOF** |
| Model provider | Google + OpenRouter | Anthropic direct |
| memory-core plugin | ❌ Not in allow list | ⚠️ In allow list, entries block missing |
| memory-lancedb-pro | ❌ Not installed | ❌ Not installed |
| Compaction config | ❌ None | ✅ Configured |
| contextPruning | ❌ None | ⚠️ 5m TTL (too aggressive for 30m runs) |
| threadBindings | ❌ Not configured | ❌ Not configured |
| Cron retry config | ❌ Not configured | ❌ Not configured |
| HEARTBEAT.md | ⚠️ Exists, empty | ⚠️ Exists, effectively empty |
| MEMORY.md | ❌ Never created | ❌ Never created |
| memory/ directory | ❌ None — 27 days | ❌ None — 27 days |
| SOUL.md | ⚠️ Base template, unevolved | ⚠️ Base template, wrong for trading |
| IDENTITY.md | ✅ Partially filled (no avatar) | ❌ **Completely blank — 27 days** |
| USER.md | ✅ Populated (Josh, LA, founder) | ❌ **Completely blank — 27 days** |
| TOOLS.md | ⚠️ Template only | ⚠️ Template only |
| AGENTS.md | ✅ Standard (both identical) | ✅ Standard (both identical) |
| Discord groupPolicy | `open` (anyone in guild) | `allowlist` (specific channel) |
| Discord dmPolicy | `open` | `pairing` |
| Discord streaming | `off` | Not set |
| workspace/reports/ | ❌ Missing | ✅ Exists (1 file, 22 days stale) |
| skills/ directory | ❌ None | ✅ `gog-cli` skill |
| Active intelligence gap | iMessage 19d, email ~16d dark | Catalyst 22d (Q2 Week 3/4 border) |
| Days with zero implementations | **27** | **27** |

---

## Workspace File Gap Analysis

### Files Present in Both ✅
- `SOUL.md` — identical content in both repos (same SHA: 792306ac) — unevolved in both
- `AGENTS.md` — identical content in both repos (same SHA: 3faead97)
- `TOOLS.md` — identical content in both repos (same SHA: 917e2fa8) — both template-only
- `BOOTSTRAP.md` — present in both
- `HEARTBEAT.md` — present in both, both effectively empty

### Files Josh Has, Noah Missing ⚠️
- **IDENTITY.md populated** — Josh has name/creature/vibe/emoji set. Noah's is blank template. **CRITICAL for Noah — Day 27.**
- **USER.md populated** — Josh's has name, timezone, employer, preferences. Noah's is blank template. **CRITICAL for Noah — Day 27.**

### Files Noah Has, Josh Missing ⚠️
- **`workspace/reports/`** — Noah has a reports directory. Josh lacks one (needed for email digests, calendar summaries, contact research).
- **`skills/gog-cli/`** — Noah has the Google OAuth CLI skill. Josh uses API key mode and doesn't have this skill directory.
- **`gogcli/`** — Noah-specific binary/tooling directory.

### Files Missing in Both ❌
- **`MEMORY.md`** — Neither instance has created a long-term memory file. AGENTS.md instructs both to create and maintain this. 27 days without it.
- **`memory/` directory** — Neither instance has any daily memory logs. Both have been operating completely stateless since deployment.

---

## Configuration Comparison

### Model Strategy

**Josh (Gemini-first with OpenRouter fallback):**
```json
"primary": "google/gemini-3-flash-preview",
"fallbacks": [
  "openrouter/google/gemini-2.5-flash",
  "openrouter/anthropic/claude-3.5-haiku"  // ⚠️ STALE — retired, should be claude-haiku-4-5-20251001
]
```
- ⚠️ `claude-3.5-haiku` fallback is retired — update to `openrouter/anthropic/claude-haiku-4-5`
- Gemini 3 Flash Preview is current and appropriate for personal assistant workloads
- No compaction configured — long email+calendar+iMessage sessions may hit context limits silently

**Noah (Anthropic direct, no fallback):**
```json
"primary": "anthropic/claude-sonnet-4-6",
"models": {
  "anthropic/claude-opus-4-6": {},
  "anthropic/claude-sonnet-4-6": {}
}
```
- ❌ **No fallback configured** — if Anthropic is unavailable, the agent goes completely offline
- `claude-opus-4-7` is now available but not in the model catalog
- Compaction configured ✅: `reserveTokensFloor: 40000`, `memoryFlush.softThresholdTokens: 4000`
- contextPruning `ttl: "5m"` — too aggressive for 30-minute research sessions (Finding 49, 27 days)

**Recommended fix — Noah:**
```json
"model": {
  "primary": "anthropic/claude-sonnet-4-6",
  "fallbacks": [
    "openrouter/anthropic/claude-sonnet-4-6",
    "openrouter/google/gemini-2.5-flash"
  ]
}
```
Routes the same Claude Sonnet model through OpenRouter as the first fallback — minimal behavioral change during failover. Requires OpenRouter auth profile configuration.

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

**Key difference:** Noah's allowlist + pairing is significantly more locked down. Josh's open+open posture means any Discord user in the guild can DM Heather. Intentional for a personal assistant, but means per-agent tool overrides are more urgent for Josh once 2026.5.10 lands.

---

### Memory Architecture

**Josh:**
- No `memory-core` plugin — not even in allow list
- No `memory-lancedb-pro` — not installed
- Memory is entirely file-based in theory; in practice, no files exist after 27 days
- No compaction = sessions accumulate context without intelligent pruning or flush
- Upgrade path: memory-core (basic) → memory-lancedb-pro (hybrid retrieval) after baseline

**Noah:**
- `memory-core` in `plugins.allow` but **absent from `plugins.entries`** — never loads, 27 days
- `memory-lancedb-pro` not installed — but once memory-core is confirmed, this is the right upgrade for SEC filing precision recall
- `memoryFlush.enabled: true` with `softThresholdTokens: 4000` configured ✅ — will work once memory-core entries block is added
- `contextPruning ttl: "5m"` conflicts with `inboundWorker.runTimeoutMs: 1800000` (30 min) — mid-run cache invalidation risk

**Gap:** Noah has the infrastructure intent for active memory but hasn't crossed the admin-scope or entries-config threshold. Josh has neither the intent nor the configuration.

---

### memory-lancedb-pro — Fleet-Wide Upgrade Path (New — Day 27)

The community-enhanced `memory-lancedb-pro` plugin (CortexReach fork) provides significantly better retrieval than bundled `memory-core` for both use cases:

| Capability | memory-core (bundled) | memory-lancedb-pro |
|---|---|---|
| Retrieval | Vector similarity only | Hybrid: Vector + BM25 keyword |
| Reranking | None | Cross-encoder reranking |
| Scope isolation | Single scope | Per-channel, per-session, global |
| Management | None | CLI: prune, list, export |
| Precision for named entities | Low | High (keywords weighted) |

**For Josh (personal assistant):** BM25 keyword matching correctly weights contact names, project names, and subject lines — producing more precise recall when drafting emails or referencing prior conversations.

**For Noah (catalyst research):** Ticker symbols (`NVDA`, `MRNA`), filing types (`8-K`, `10-Q`), and company names benefit from BM25 keyword weighting. Cross-encoder reranking distinguishes "related to biotech" from "the specific PDUFA date researched last month." Multi-scope isolation separates biotech research from tech research.

**Implementation order (same for both instances):**
1. Fix/install `memory-core` first → build initial corpus
2. Evaluate `memory-lancedb-pro` upgrade once baseline is confirmed working

---

### X/Twitter Community Intelligence (New — Day 27)

Community posts from X surface patterns directly relevant to both instances:

**@tipheret — "Config Loop" failure mode:**
> "My bot kept forgetting our conversations. Losing skills. Forgetting keys. Felt like we were stuck in the same config loop every single day."

This is exactly the failure mode both Josh and Noah exhibit after 27 days. The community resolution is documented and confirmed:
1. Manual memory bootstrap (write a first memory file via live Discord session)
2. Activate HEARTBEAT.md with a daily memory checkpoint
3. Only then configure memory plugins — empty plugin + empty corpus = no benefit

**@chrysb (AlphaClaw creator):** AlphaClaw Apex provides a single-pane fleet dashboard — deploy to Hetzner VPS in one click, monitor all instances, manage configs, updates, spend, and health. Both Josh and Noah are already running under AlphaClaw management. The dashboard's health view would surface version gaps and plugin configuration issues without manual research scans like these.

**@rolznz — Autonomous agent bootstrap:** A bot that spun up its own VPS and purchased AI credits autonomously via Bitcoin wallet. Directionally relevant as A2A capabilities land in 2026.5.10 — both instances have sub-agent capabilities that are completely unused.

---

### New Features Available to Both (Day 27 Update)

#### threadBindings (2026.5.x — pending update)

| Instance | Config (idleHours / maxAgeHours) |
|---|---|
| Josh / Heather | 24h / 168h — personal assistant threads expire weekly |
| Noah / Market Catalyst | 48h / 336h — catalyst research threads persist 2 weeks |

#### Cron Retry — Identical for Both
```json
"cron": {
  "retry": {
    "maxAttempts": 3,
    "backoffMs": [60000, 120000, 300000],
    "retryOn": ["rate_limit", "overloaded", "network", "server_error"]
  }
}
```

#### 2026.5.10 Upcoming Stable (Monitor)
| Feature | Josh Value | Noah Value |
|---|---|---|
| `/context map` treemap | Token visibility in long email/iMessage sessions | Validate contextPruning TTL fix |
| A2A 20-turn pipelines | Sub-agent delegation for research tasks | Full autonomous catalyst pipeline |
| Per-agent tool overrides | Discord group security boundaries | Alpaca execution DM-only |
| `extractStructuredWithModel()` | N/A | SEC PDF structured extraction |
| ACPX startup probe fix | Faster watchdog recovery | Reliable startup on Noah's VPS |

---

## Priority Matrix — Fleet-Wide (Day 27)

### CRITICAL (Zero Config — Do Today)
| Item | Josh | Noah |
|---|---|---|
| Memory bootstrap message in Discord | ⬜ **Do today — Day 27** | ⬜ **Do today — Day 27** |
| Fill IDENTITY.md | ⚠️ Partial (avatar missing) | ❌ **Blank — Day 27** |
| Fill USER.md | ✅ Done | ❌ **Blank — Day 27** |
| Resume intelligence monitoring | iMessage/email (19d/16d dark) | Q2 week 4 watchlist (pre-position today) |

### HIGH (Unblock Everything Else)
| Item | Josh | Noah |
|---|---|---|
| Update OpenClaw | 2026.3.22 → 2026.5.7 (13 releases) | 2026.4.15 → 2026.5.7 (9 releases) |
| Populate HEARTBEAT.md | Empty — 27 days | Empty — 27 days |
| Create MEMORY.md | Never created | Never created |
| Create memory/ directory | Never created | Never created |

### MEDIUM (Post-Update)
| Item | Josh | Noah |
|---|---|---|
| memory-core | Add to allow + entries | Fix entries block (missing) |
| Compaction config | Add to agents.defaults | Already configured ✅ |
| threadBindings config | Add to session | Add to session (longer TTL) |
| Cron retry config | Add to openclaw.json | Add to openclaw.json |
| Stale fallback | Fix claude-3.5-haiku → haiku-4-5 | Add OpenRouter fallback (SPOF) |
| contextPruning TTL | N/A | 5m → 35m (match inboundWorker) |
| SOUL.md evolution | Never evolved | Wrong persona for trading |

### LOW / OPPORTUNITY (Post-2026.5.10-stable)
| Item | Josh | Noah |
|---|---|---|
| memory-lancedb-pro upgrade | After memory-core baseline | After memory-core baseline |
| workspace/reports/ | Create directory | Already exists |
| Per-agent tool overrides | Discord group security | Alpaca DM-only execution |
| /context map | Token visibility | Validate TTL fix |
| A2A 20-turn pipelines | Sub-agent delegation | Full catalyst pipeline |
| extractStructuredWithModel() | N/A | SEC PDF extraction |
| claude-opus-4-7 in catalog | N/A | Add to models config |

---

## Implementation Sequence — Fleet-Wide (Day 27)

**For both instances, the correct order is unchanged from Day 26:**

1. **TODAY — Zero config:** Send memory bootstrap message in Discord for both instances
2. **TODAY — Noah only:** Send Q2 Week 4 watchlist prompt (pre-positioning window closes tonight)
3. **TODAY — Noah only:** Fill IDENTITY.md and USER.md (27 days critical)
4. Update OpenClaw to 2026.5.7 — unlocks all 5.x features for both
5. Fix memory-core (Josh: add to allow+entries; Noah: add entries block) + compaction configs
6. Add cron retry config to both openclaw.json files
7. Enable threadBindings in both (different TTLs)
8. Populate HEARTBEAT.md for both instances
9. Create MEMORY.md + memory/ scaffold for both
10. Fix fallback models (Josh: stale haiku; Noah: add OpenRouter fallback)
11. Fix contextPruning TTL on Noah (5m → 35m)
12. Monitor for 2026.5.10 stable → apply tool overrides, A2A, /context map
13. After memory baseline confirmed: evaluate memory-lancedb-pro for both

---

## Trend Analysis — 27 Days

This fleet has received **zero implementations** across 27 days of documented research. The gap between what both instances are capable of and what they're actually doing widens daily:

- Josh's version gap was 7 releases on Day 1. It is now **13 releases** — a second update target (5.10) arrives within days.
- Noah's catalyst intelligence gap was 0 on Day 1. It is now **22 days** into an active Q2 earnings season. Q2 Week 4 — the heaviest earnings week — begins Monday May 19.
- Neither instance has a single memory file — both wake up completely stateless every session.
- Both HEARTBEAT.md files are empty — neither instance does any proactive work.
- The community-documented fix for this exact failure mode requires one Discord message, not a config change.

The research is thorough. The blocker is execution. No new research finding changes the priority order:

**Bootstrap memory (Discord message today) → update OpenClaw → activate HEARTBEAT.md.**

Everything else — memory-core, compaction, threadBindings, cron retry, memory-lancedb-pro — delivers maximum value on top of that foundation.

---

## Shared Config Snippet Library

### threadBindings (session level)
```json
"session": {
  "threadBindings": {
    "enabled": true,
    "spawnSessions": true
  }
}
```

### cron retry (both instances)
```json
"cron": {
  "retry": {
    "maxAttempts": 3,
    "backoffMs": [60000, 120000, 300000],
    "retryOn": ["rate_limit", "overloaded", "network", "server_error"]
  }
}
```

### memory-core (Josh — full add)
```json
"plugins": {
  "allow": ["discord", "usage-tracker", "memory-core"],
  "entries": {
    "discord": {"enabled": true},
    "usage-tracker": {"enabled": true},
    "memory-core": {"enabled": true}
  }
}
```

### memory-core (Noah — entries block only, allow already set)
```json
"memory-core": {
  "enabled": true,
  "config": {
    "deduplication": true,
    "temporalDecay": true
  }
}
```

### Josh fallback fix
```json
"fallbacks": [
  "openrouter/google/gemini-2.5-flash",
  "openrouter/anthropic/claude-haiku-4-5"
]
```

### Noah fallback add
```json
"model": {
  "primary": "anthropic/claude-sonnet-4-6",
  "fallbacks": [
    "openrouter/anthropic/claude-sonnet-4-6",
    "openrouter/google/gemini-2.5-flash"
  ]
}
```

### Noah contextPruning fix
```json
"contextPruning": {
  "mode": "cache-ttl",
  "ttl": "35m"
}
```

---
*Generated by AlphaClaw Apex Fleet Research Agent — Morning Scan — 2026-05-14 (Day 27)*
