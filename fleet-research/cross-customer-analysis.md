# Cross-Customer Analysis — AlphaClaw Apex Fleet

**Last Updated:** 2026-05-15 (Morning Scan — Day 28)  
**Instances:** Josh (Heather Schwartz, personal assistant) | Noah (Market Catalyst Agent, stock research)  
**Scan cadence:** Morning + Evening daily

---

## Fleet Overview

| Dimension | Josh / Heather | Noah / Market Catalyst |
|---|---|---|
| OpenClaw version | **2026.3.22** (85 days old) | **2026.4.15** (23 days old) |
| Releases behind stable | **13 releases** | **9 releases** |
| Primary model | `google/gemini-3-flash-preview` | `anthropic/claude-sonnet-4-6` |
| Fallback model | OpenRouter (gemini-2.5-flash, claude-3.5-haiku ⚠️ retired) | ❌ **None — Anthropic SPOF** |
| Model provider | Google + OpenRouter | Anthropic direct |
| Anthropic auth risk | N/A | ⚠️ **`mode: token` — verify API key vs subscription** |
| memory-core plugin | ❌ Not in allow list | ⚠️ In allow list, entries block missing |
| memory-lancedb-pro | ❌ Not installed | ❌ Not installed |
| Compaction config | ❌ None | ✅ Configured |
| contextPruning | ❌ None | ⚠️ 5m TTL (too aggressive for 30m runs) |
| threadBindings | ❌ Not configured | ❌ Not configured |
| Cron retry config | ❌ Not configured | ❌ Not configured |
| HEARTBEAT.md | ⚠️ Exists, 168 bytes (empty) | ⚠️ Exists, 193 bytes (empty) |
| MEMORY.md | ❌ Never created | ❌ Never created |
| memory/ directory | ❌ None — 28 days | ❌ None — 28 days |
| SOUL.md | ⚠️ Base template, unevolved | ⚠️ Base template, wrong for trading |
| IDENTITY.md | ✅ Partially filled (no avatar) | ❌ **Completely blank — 28 days** |
| USER.md | ✅ Populated (Josh, LA, founder) | ❌ **Completely blank — 28 days** |
| TOOLS.md | ⚠️ Template only | ⚠️ Template only |
| AGENTS.md | ✅ Standard (both identical) | ✅ Standard (both identical) |
| Discord groupPolicy | `open` (anyone in guild) | `allowlist` (specific channel) |
| Discord dmPolicy | `open` | `pairing` |
| Discord streaming | `off` | Not set |
| workspace/reports/ | ❌ Missing | ✅ Exists (1 file, 23 days stale) |
| skills/ directory | ❌ None | ✅ Unaudited — **security risk (ClawHub malware context)** |
| Active intelligence gap | iMessage 20d, email ~17d dark | Catalyst 23d (Q2 Week 4 active) |
| Days with zero implementations | **28** | **28** |

---

## NEW — Day 28: Platform Risk Additions

### Anthropic Auth Disruption Risk (Noah Only — HIGH)

In April 2026, Anthropic banned subscription-based auth for third-party agents including OpenClaw, then reversed with "Agent SDK credits" requiring direct API key authentication. Noah's `openclaw.json` uses `mode: "token"` for both `anthropic:default` and `anthropic:manual` profiles. This is ambiguous — it can mean either a direct API key or a subscription session token.

**Verification required:**
- `sk-ant-api...` key → direct API key, safe
- Subscription session token → ongoing policy disruption risk

If subscription auth is in use, the Market Catalyst Agent is one Anthropic policy change away from going dark during market hours. Migrate to `mode: "api_key"` with a direct API key.

**Josh:** Not affected — uses Google/OpenRouter, no Anthropic dependency.

---

### Config-Wipe Bug During Updates (Both Instances — HIGH)

GitHub issue #65105 confirmed: updating through certain version ranges silently wipes the entire `channels.discord` block and `agents.list` array from `openclaw.json`.

**Both instances must back up `openclaw.json` before updating.** Josh's `requireMention: false` guild config and Noah's allowlist channel config are both at risk.

**Zero-cost pre-update action:**
1. Download `openclaw.json` from the AlphaClaw Apex dashboard or repo
2. Save timestamped backup: `openclaw-backup-2026-05-15.json`
3. Post-update: verify Discord channel config is intact before assuming update succeeded

---

### Session Corruption Bug #75235 (Noah Priority — MEDIUM)

A leading-assistant transcript triggers an infinite "messages: at least one message is required" loop. Long-running sessions (Noah's 30-minute research runs) are most at risk. A hung session produces no output and no error — just silent timeout. Fixed in 2026.5.7. Soft restart (`--soft`) resolves a hung session in the meantime.

---

### ClawHub Malware Context — Noah's skills/ Directory Upgraded to HIGH Risk

1,184 malicious skills were distributed via ClawHub in early 2026, specifically targeting financial integration skills (trading, wallet, market data). Noah's `skills/` directory contains unaudited skills installed at onboarding. This is now a **HIGH priority security audit**, not routine housekeeping.

---

## Workspace File Gap Analysis

### Files Present in Both ✅
- `SOUL.md` — identical content in both repos (same SHA: 792306ac) — unevolved in both
- `AGENTS.md` — identical content in both repos (same SHA: 3faead97)
- `TOOLS.md` — identical content in both repos (same SHA: 917e2fa8) — both template-only
- `BOOTSTRAP.md` — present in both
- `HEARTBEAT.md` — present in both, both effectively empty

### Files Josh Has, Noah Missing ⚠️
- **IDENTITY.md populated** — Josh has name/creature/vibe/emoji set. Noah's is blank template. **CRITICAL for Noah — Day 28.**
- **USER.md populated** — Josh's has name, timezone, employer, preferences. Noah's is blank template. **CRITICAL for Noah — Day 28.**

### Files Noah Has, Josh Missing ⚠️
- **`workspace/reports/`** — Noah has a reports directory (23 days stale). Josh lacks one (needed for email digests, calendar summaries, contact research).
- **`skills/gog-cli/`** — Noah has the Google OAuth CLI skill. Josh uses API key mode and doesn't have this skill directory.
- **`gogcli/`** — Noah-specific binary/tooling directory.

### Files Missing in Both ❌
- **`MEMORY.md`** — Neither instance has created a long-term memory file. AGENTS.md instructs both to create and maintain this. 28 days without it.
- **`memory/` directory** — Neither instance has any daily memory logs. Both have been operating completely stateless since deployment.

---

## Configuration Comparison

### Model Strategy

**Josh (Gemini-first with OpenRouter fallback):**
```json
"primary": "google/gemini-3-flash-preview",
"fallbacks": [
  "openrouter/google/gemini-2.5-flash",
  "openrouter/anthropic/claude-3.5-haiku"  // ⚠️ STALE — retired
]
```
- ⚠️ `claude-3.5-haiku` fallback is retired — update to `openrouter/anthropic/claude-haiku-4-5`
- **NEW Day 28:** `google/gemini-3.1-flash-lite-preview` available as a faster/cheaper first fallback (2.5x speed boost over gemini-3-flash-preview)
- Gemini 3 Flash Preview: 1M context window, 380 tok/s, 66K output, configurable reasoning — strong for personal assistant workloads
- No compaction configured — long email+calendar+iMessage sessions may hit context limits silently

**Recommended fallback update — Josh (Day 28):**
```json
"fallbacks": [
  "openrouter/google/gemini-3.1-flash-lite-preview",
  "openrouter/google/gemini-2.5-flash",
  "openrouter/anthropic/claude-haiku-4-5"
]
```

**Noah (Anthropic direct, no fallback):**
```json
"primary": "anthropic/claude-sonnet-4-6",
"models": {
  "anthropic/claude-opus-4-6": {},
  "anthropic/claude-sonnet-4-6": {}
}
```
- ❌ **No fallback configured** — if Anthropic is unavailable, the agent goes completely offline
- **⚠️ NEW Day 28:** `mode: "token"` auth — verify this is a direct API key, not subscription auth
- `claude-opus-4-7` is now available but not in the model catalog
- Compaction configured ✅: `reserveTokensFloor: 40000`, `memoryFlush.softThresholdTokens: 4000`
- contextPruning `ttl: "5m"` — too aggressive for 30-minute research sessions (28 days unresolved)

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

**Key difference:** Noah's allowlist + pairing is significantly more locked down. Josh's open+open posture means any Discord user in the guild can DM Heather. Per-agent tool overrides (2026.5.10) are more urgent for Josh.

**NEW Day 28:** Both instances should use soft restart (`--soft`, now default) when restarting agents — preserves channel bindings + auth profiles + workspace state.

---

### Memory Architecture

**Josh:**
- No `memory-core` plugin — not even in allow list
- No `memory-lancedb-pro` — not installed
- Memory is entirely file-based in theory; in practice, zero files exist after 28 days
- No compaction = sessions accumulate context without intelligent pruning or pre-compaction flush
- Upgrade path: memory-core (basic) → memory-lancedb-pro (hybrid retrieval) after baseline

**Noah:**
- `memory-core` in `plugins.allow` but **absent from `plugins.entries`** — never loads, 28 days
- `memory-lancedb-pro` not installed — correct upgrade path once memory-core baseline is active
- `memoryFlush.enabled: true` + `softThresholdTokens: 4000` ✅ — will activate once entries block is added
- contextPruning `ttl: "5m"` conflicts with 30-min inboundWorker timeout — mid-run cache invalidation risk

**Gap:** Noah has the infrastructure intent for active memory but hasn't crossed the entries-config threshold. Josh has neither the intent nor the config.

---

### memory-lancedb-pro — Fleet-Wide Upgrade Path

The community-enhanced `memory-lancedb-pro` plugin (CortexReach fork):

| Capability | memory-core (bundled) | memory-lancedb-pro |
|---|---|---|
| Retrieval | Vector similarity only | Hybrid: Vector + BM25 keyword |
| Reranking | None | Cross-encoder reranking |
| Scope isolation | Single scope | Per-channel, per-session, global |
| Management | None | CLI: prune, list, export |
| Precision for named entities | Low | High (keywords weighted in BM25) |

**For Josh:** BM25 weights contact names, project names, subject lines — precise recall when drafting emails or referencing prior conversations.

**For Noah:** Ticker symbols (`NVDA`, `MRNA`), filing types (`8-K`, `10-Q`), company names benefit from BM25. Cross-encoder reranking distinguishes "related to biotech" from "the specific PDUFA date from last month." Multi-scope isolation separates biotech from tech research domains.

**Implementation order (both):**
1. Fix/install `memory-core` → build initial corpus
2. Evaluate `memory-lancedb-pro` upgrade once baseline is confirmed working

---

## Priority Matrix — Fleet-Wide (Day 28)

### CRITICAL (Zero Config — Do Today)
| Item | Josh | Noah |
|---|---|---|
| Memory bootstrap message in Discord | ⬜ **Do today — Day 28** | ⬜ **Do today — Day 28** |
| Fill IDENTITY.md | ⚠️ Partial (avatar) | ❌ **Blank — Day 28** |
| Fill USER.md | ✅ Done | ❌ **Blank — Day 28** |
| Resume intelligence monitoring | iMessage/email dark | Q2 Week 4 playbook (this morning) |
| **NEW:** Verify Anthropic auth mode | N/A | ⬜ **Check today — auth risk** |
| **NEW:** Skills security audit | N/A | ⬜ **Do today — malware risk** |

### HIGH (Pre-Update and Update)
| Item | Josh | Noah |
|---|---|---|
| **NEW:** Backup openclaw.json before update | ⬜ Before update | ⬜ Before update |
| Update OpenClaw | 2026.3.22 → 2026.5.7 (13 releases) | 2026.4.15 → 2026.5.7 (9 releases) |
| Populate HEARTBEAT.md | Empty — 28 days | Empty — 28 days |
| Create MEMORY.md | Never created | Never created |
| Create memory/ directory | Never created | Never created |

### MEDIUM (Can Apply Today — No Update Required)
| Item | Josh | Noah |
|---|---|---|
| **NEW:** Cron retry config | Add to openclaw.json | Add to openclaw.json |
| contextPruning TTL fix | N/A | 5m → 35m (apply today) |

### MEDIUM (Post-Update)
| Item | Josh | Noah |
|---|---|---|
| memory-core | Add to allow + entries | Fix entries block (missing) |
| Compaction config | Add to agents.defaults | Already configured ✅ |
| threadBindings config | Add to session | Add to session (longer TTL) |
| Fallback model fix | Fix retired haiku + add flash-lite | Add OpenRouter fallback (SPOF) |
| SOUL.md evolution | Never evolved | Wrong persona for trading |
| Active memory admin scope | N/A | Verify post-update |

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

## Implementation Sequence — Fleet-Wide (Day 28)

1. **TODAY — Noah:** Verify Anthropic auth mode — API key vs subscription token (Finding 58)
2. **TODAY — Zero config:** Send memory bootstrap message in Discord for both instances
3. **TODAY — Noah (time-sensitive):** Send Q2 Week 4 active-week playbook prompt
4. **TODAY — Noah:** Send skills security audit prompt (ClawHub malware context)
5. **TODAY — Noah (no update required):** Apply contextPruning TTL fix: `"ttl": "35m"`
6. **TODAY — Both (no update required):** Add `cron.retry` block to `openclaw.json`
7. **TODAY — Noah:** Fill IDENTITY.md and USER.md via Discord (28 days critical)
8. **Before update — Both:** Backup `openclaw.json` (config-wipe bug protection)
9. Update OpenClaw to 2026.5.7 — fixes session corruption, active memory admin scope
10. Verify Discord channel config intact post-update (check for config-wipe regression)
11. Fix memory-core (Josh: add to allow+entries; Noah: add entries block) + compaction configs
12. Add cron retry config to both if not already applied
13. Enable threadBindings in both (Josh: 24h/168h; Noah: 48h/336h)
14. Populate HEARTBEAT.md for both instances
15. Create MEMORY.md + memory/ scaffold for both
16. Fix fallback models (Josh: haiku+flash-lite; Noah: add OpenRouter fallback)
17. Monitor for 2026.5.10 stable → tool overrides, A2A, /context map
18. After memory baseline confirmed: evaluate memory-lancedb-pro for both

---

## Trend Analysis — 28 Days

This fleet has received **zero implementations** across 28 days of documented research.

- Josh's version gap was 7 releases on Day 1. It is now **13 releases** — a second target (5.10 stable) arrives within days.
- Noah's catalyst intelligence gap was 0 on Day 1. It is now **23 days** into Q2 earnings season. Q2 Week 4 (the heaviest earnings week) is 4 days away.
- Neither instance has a single memory file. Both wake completely stateless every session.
- Both HEARTBEAT.md files are empty. Neither instance does any proactive work.
- **NEW Day 28:** Two HIGH-risk issues identified that require verification today before any other work: Noah's Anthropic auth mode and Noah's unaudited skills directory (ClawHub malware context).
- **NEW Day 28:** Two config changes can be applied today with zero risk and zero update dependency: Noah's contextPruning TTL fix and cron retry config for both.

**The blocker is execution, not research.** New config-only actions that can be applied TODAY:

| Action | Target | Time |
|---|---|---|
| Verify Anthropic auth mode | Noah | 5 min |
| Skills security audit prompt | Noah | 2 min |
| Q2 Week 4 playbook prompt | Noah | 2 min |
| Memory bootstrap prompt | Both | 2 min each |
| contextPruning `"ttl": "35m"` | Noah | 1 min |
| Add `cron.retry` block | Both | 5 min |

**Estimated total time for all zero-config actions: ~20 minutes.** None of these require an OpenClaw update.

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

### cron retry — Both instances (exact backoff values — Day 28 confirmed)
```json
"cron": {
  "retry": {
    "maxAttempts": 3,
    "backoffMs": [30000, 60000, 300000, 900000, 3600000],
    "retryOn": ["rate_limit", "overloaded", "network", "server_error"]
  }
}
```
_Backoff progression: 30s → 1m → 5m → 15m → 60m. Resets after successful run._

### memory-core — Josh (full add)
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

### memory-core — Noah (entries block only, allow already set)
```json
"memory-core": {
  "enabled": true,
  "config": {
    "deduplication": true,
    "temporalDecay": true
  }
}
```

### Josh fallback fix + Gemini 3.1 Flash-Lite (Day 28 update)
```json
"fallbacks": [
  "openrouter/google/gemini-3.1-flash-lite-preview",
  "openrouter/google/gemini-2.5-flash",
  "openrouter/anthropic/claude-haiku-4-5"
]
```

### Noah fallback add (SPOF fix)
```json
"model": {
  "primary": "anthropic/claude-sonnet-4-6",
  "fallbacks": [
    "openrouter/anthropic/claude-sonnet-4-6",
    "openrouter/google/gemini-2.5-flash"
  ]
}
```

### Noah contextPruning fix (apply today — no update required)
```json
"contextPruning": {
  "mode": "cache-ttl",
  "ttl": "35m"
}
```

---
*Generated by AlphaClaw Apex Fleet Research Agent — Morning Scan — 2026-05-15 (Day 28)*
