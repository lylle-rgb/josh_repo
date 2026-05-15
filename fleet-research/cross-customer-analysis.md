# Cross-Customer Analysis — AlphaClaw Apex Fleet

**Last Updated:** 2026-05-15 (Morning Scan Run 2 — Day 28)  
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
| skills/ directory | ❌ None | ✅ Unaudited — **security risk (ClawHub 20% flagged)** |
| File transfer plugin | ✅ Post-update (v2026.5.3) | ✅ Post-update (v2026.5.3) |
| X Search skill | ⚠️ Future (post-baseline) | ⚠️ Future (post-skills-audit) |
| Watchdog notifications | ❓ Verify via Apex dashboard | ❓ **Verify today — trading hours risk** |
| Heartbeat optimization | ❌ lightContext/isolatedSession not set | ❌ lightContext/isolatedSession not set |
| Active intelligence gap | iMessage 20d, email ~17d dark | Catalyst 23d (Q2 Week 4 active) |
| Days with zero implementations | **28** | **28** |

---

## NEW — Day 28 Run 2: Additional Platform Findings

### File Transfer Plugin — Both Instances (v2026.5.3)

v2026.5.3 (inside the update gap for both) ships four new agent tools as part of the file transfer plugin: `file_fetch`, `dir_list`, `dir_fetch`, `file_write`. Binary operations with 16 MB ceiling.

**Josh / Heather:** Attachment retrieval (`file_fetch`), workspace self-audit (`dir_list`), calendar/email export caching (`file_write`). No additional config — activates post-update.

**Noah / Market Catalyst:** **High-value for SEC workflow** — `file_fetch` downloads EDGAR PDF filings (10-K, 10-Q, 8-K) directly as binary files. `file_write` caches to `workspace/reports/`. This is the missing infrastructure link for `extractStructuredWithModel()` on full filing documents. 16MB ceiling accommodates most standard filings; large annual reports with graphics may still need text-extracted fallback.

**Risk:** LOW for both — post-update unlock, no config change.

---

### Heartbeat lightContext + isolatedSession — Both Instances

Community best practices confirm two flags for heartbeat runs not set by either instance:

```json
"heartbeat": {
  "lightContext": true,
  "isolatedSession": true
}
```

**`lightContext: true`:** Strips conversation history from heartbeat context window. Heartbeat only receives HEARTBEAT.md + current session state. Reduces token burn on every 30-minute check cycle.

**`isolatedSession: true`:** Runs heartbeat in an isolated session. Prevents heartbeat context from contaminating main conversation history.

**Why more urgent for Noah:** 30-minute research sessions accumulate significant context. A market-open heartbeat check that runs inside a live research session would load 30 minutes of SEC analysis context just to check if there are any alerts — wasteful and risks context pollution across sessions.

**Apply when activating HEARTBEAT.md for either instance — no OpenClaw update required.**

---

### AlphaClaw Watchdog Crash Notifications — Both Instances

AlphaClaw's platform-level watchdog includes Discord/Telegram crash notifications in addition to crash-loop detection and auto-restart.

**Josh:** Verify via AlphaClaw Apex dashboard. Most valuable during the pending update window when crash risk is elevated. If Heather goes dark post-update, Josh gets an immediate Discord ping rather than noticing hours later.

**Noah (ELEVATED URGENCY):** A trading agent crash during pre-market (4-9:30 AM ET), market open (9:30-10:30 AM ET), or earnings window (4-5 PM ET) is a meaningful operational failure. Given Finding 58 (Anthropic auth disruption risk), Noah could go offline due to an external policy change — watchdog notifications are the only way the operator knows immediately. **Verify watchdog Discord notifications before updating.**

**This is a fleet-operator setting in the AlphaClaw Apex dashboard, not an `openclaw.json` change.**

---

### X Search Skill — Noah Priority, Josh Future

**Noah:** X Search enables natural-language search over X content: executive commentary ahead of earnings, analyst reactions to 8-K filings, short-seller threads, pre-PDUFA biotech sentiment. High-alpha, real-time source for catalyst hunting. **Requires skills/ security audit first (Finding 61/67).** Only evaluate read-only X Search (no `write` permission).

**Josh:** X monitoring for Bliss and Oben HiFi brands. Lower urgency than Noah's use case. Future capability post-baseline.

**Both:** ~20% of ClawHub catalog was flagged in the Feb 2026 security audit. Any X skill must be verified against the official safe list before install.

---

### ClawHub Security Context Update — 20% Flagged (Noah Priority)

Updated intelligence: ClawHub's Feb 2026 audit flagged **~20% of all skills** on the marketplace:
- 1,184 confirmed malicious (credential-stealing, wallet-draining)
- 2,419 suspicious (insecure patterns, unexpected network calls)
- ~20% of remaining catalog flagged for review

Skills installed before Feb 2026 should be treated as potentially unreviewed. The malicious skills specifically targeted financial integration categories — Noah's `skills/` directory is the highest-risk surface on this fleet.

---

## Platform Risk Summary — Day 28 (Updated Run 2)

### Anthropic Auth Disruption Risk (Noah Only — HIGH)

In April 2026, Anthropic banned subscription-based auth for third-party agents including OpenClaw, then reversed with "Agent SDK credits" requiring direct API key authentication. Noah's `openclaw.json` uses `mode: "token"` for both `anthropic:default` and `anthropic:manual` profiles. This is ambiguous.

**Verification:**
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

A leading-assistant transcript triggers an infinite loop, hanging the session permanently. Long-running sessions (Noah's 30-minute research runs) are most at risk. Fixed in 2026.5.7. Soft restart (`--soft`) resolves a hung session.

---

### ClawHub Malware — Noah's skills/ Directory (HIGH)

1,184 malicious skills + 2,419 suspicious skills purged in early 2026; ~20% of remaining catalog flagged. Noah's trading agent with Alpaca access is the exact target profile. Skills audit is a security priority, not routine housekeeping.

---

## Workspace File Gap Analysis

### Files Present in Both ✅
- `SOUL.md` — **identical content in both repos (same SHA: 792306ac)** — unevolved in both
- `AGENTS.md` — **identical content in both repos (same SHA: 3faead97)** — generic template in both
- `TOOLS.md` — **identical content in both repos (same SHA: 917e2fa8)** — both template-only, nothing filled in
- `BOOTSTRAP.md` — present in both
- `HEARTBEAT.md` — present in both, both effectively empty

**Note:** Three workspace files are byte-for-byte identical between the two instances. This is a gap: Heather's SOUL.md should reflect a personal assistant persona; Market Catalyst Agent's SOUL.md should reflect a disciplined trading analyst persona. Neither has evolved.

### Files Josh Has, Noah Missing ⚠️
- **IDENTITY.md populated** — Josh has name/creature/vibe set. Noah's is blank template. **CRITICAL for Noah — 28 days.**
- **USER.md populated** — Josh's has name, timezone, employer, preferences. Noah's is blank. **CRITICAL for Noah — 28 days.**

### Files Noah Has, Josh Missing ⚠️
- **`workspace/reports/`** — Noah has a reports directory (23 days stale). Josh lacks one (needed for email digests, calendar summaries, contact research).
- **`skills/` directory** — Noah has the skills directory. Josh has none. Josh's simpler use case (personal assistant) may not require many skills, but the X Search skill is worth evaluating post-baseline.
- **`gogcli/`** — Noah-specific tooling.

### Files Missing in Both ❌
- **`MEMORY.md`** — Neither has created a long-term memory file. AGENTS.md instructs both to create and maintain this. 28 days without it.
- **`memory/` directory** — Neither has any daily memory logs. Both operate completely stateless each session.

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
- ⚠️ `claude-3.5-haiku` fallback is retired
- `google/gemini-3.1-flash-lite-preview` available as 2.5x faster/cheaper first fallback
- Gemini 3 Flash Preview: 1M context window, 380 tok/s, 66K output — strong for personal assistant
- No compaction configured — long email+calendar sessions may hit context limits silently

**Recommended fallback update (post-update):**
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
- ❌ **No fallback configured** — Anthropic unavailability = complete agent offline
- ⚠️ `mode: "token"` auth — verify this is a direct API key, not subscription auth
- `claude-opus-4-7` is now available but not in the model catalog
- Compaction: `reserveTokensFloor: 40000`, `memoryFlush.softThresholdTokens: 4000` ✅
- contextPruning `ttl: "5m"` — too aggressive for 30-minute research sessions

**Recommended (apply today — no update required for contextPruning):**
```json
"contextPruning": {
  "mode": "cache-ttl",
  "ttl": "35m"
}
```

**Recommended fallback (post-update):**
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

---

### Memory Architecture

**Josh:**
- No `memory-core` plugin — not even in allow list
- Memory is entirely file-based in theory; zero files exist after 28 days
- No compaction = sessions accumulate context without pruning
- Upgrade path: memory-core (basic) → memory-lancedb-pro (hybrid retrieval) after baseline

**Noah:**
- `memory-core` in `plugins.allow` but **absent from `plugins.entries`** — never loads, 28 days
- `memoryFlush.enabled: true` + `softThresholdTokens: 4000` ✅ — will activate once entries block is added
- contextPruning `ttl: "5m"` conflicts with 30-min inboundWorker timeout

**Gap:** Noah has the infrastructure intent for active memory but hasn't crossed the entries-config threshold. Josh has neither the intent nor the config.

---

### memory-lancedb-pro — Fleet-Wide Upgrade Path

| Capability | memory-core (bundled) | memory-lancedb-pro |
|---|---|---|
| Retrieval | Vector similarity only | Hybrid: Vector + BM25 keyword |
| Reranking | None | Cross-encoder reranking |
| Scope isolation | Single scope | Per-channel, per-session, global |
| Management | None | CLI: prune, list, export |

**For Josh:** BM25 weights contact names, project names, subject lines — precise recall when drafting emails or referencing prior conversations.

**For Noah:** Ticker symbols (`NVDA`, `MRNA`), filing types (`8-K`, `10-Q`), company names benefit from BM25. Multi-scope isolation separates biotech from tech research domains.

**Implementation order (both):**
1. Fix/install `memory-core` → build initial corpus
2. Evaluate `memory-lancedb-pro` upgrade once baseline is confirmed working

---

## Priority Matrix — Fleet-Wide (Day 28, All Runs)

### CRITICAL / HIGH (Do Today or Before Update)
| Item | Josh | Noah |
|---|---|---|
| Verify Anthropic auth mode | N/A | ⬜ **Today (Finding 58)** |
| Verify watchdog crash notifications | ⬜ Via Apex dashboard | ⬜ **Today — trading hours risk (Finding 66)** |
| Memory bootstrap message in Discord | ⬜ **Do today — 28 days** | ⬜ **Do today — 28 days** |
| Q2 Week 4 playbook prompt | N/A | ⬜ **This morning — time-sensitive** |
| Skills security audit | N/A | ⬜ **Today — ClawHub 20% flagged (Findings 61+67)** |
| Fill IDENTITY.md | ⚠️ Partial (avatar) | ❌ **Blank — 28 days** |
| Fill USER.md | ✅ Done | ❌ **Blank — 28 days** |
| Backup openclaw.json before update | ⬜ Before update | ⬜ Before update |
| Update OpenClaw | 3.22 → 5.7 (13 releases) | 4.15 → 5.7 (9 releases) |

### MEDIUM (Apply Today — No Update Required)
| Item | Josh | Noah |
|---|---|---|
| Cron retry config | ⬜ Add to openclaw.json | ⬜ Add to openclaw.json |
| contextPruning TTL fix | N/A | ⬜ 5m → 35m (today) |

### MEDIUM / LOW (Post-Update)
| Item | Josh | Noah |
|---|---|---|
| memory-core | Add to allow + entries | Fix entries block |
| Compaction config | Add to agents.defaults | Already configured ✅ |
| threadBindings config | Add to session | Add to session |
| Fallback model fix | Fix retired haiku + add flash-lite | Add OpenRouter fallback (SPOF) |
| File transfer plugin | Post-update (Finding 48) | Post-update (Finding 63) |
| OpenRouter caching | Post-update (Finding 49) | N/A (no OpenRouter) |
| SOUL.md evolution | Never evolved | Wrong persona for trading |
| Heartbeat lightContext/isolatedSession | On HEARTBEAT activation | On HEARTBEAT activation |
| Populate HEARTBEAT.md | Empty — 28 days | Empty — 28 days |
| Create MEMORY.md + memory/ | Never created | Never created |
| X Search skill | Future (Finding 52) | Post-skills-audit (Finding 64) |

### LOW / OPPORTUNITY (Post-2026.5.10-stable)
| Item | Josh | Noah |
|---|---|---|
| memory-lancedb-pro | After memory-core baseline | After memory-core baseline |
| workspace/reports/ | Create directory | Already exists |
| Per-agent tool overrides | Discord group security | Alpaca DM-only execution |
| /context map | Token visibility | Validate TTL fix |
| A2A 20-turn pipelines | Sub-agent delegation | Full catalyst pipeline |
| extractStructuredWithModel() + file_fetch | N/A | SEC PDF extraction |
| claude-opus-4-7 in catalog | N/A | Add to models config |

---

## Implementation Sequence — Fleet-Wide (Day 28, All Runs)

1. **TODAY — Noah:** Verify Anthropic auth mode (Finding 58)
2. **TODAY — Noah:** Verify AlphaClaw watchdog Discord notifications (Finding 66)
3. **TODAY — Zero config:** Memory bootstrap message in Discord (both instances)
4. **TODAY — Noah (time-sensitive):** Q2 Week 4 active-week playbook prompt
5. **TODAY — Noah:** Updated skills security audit prompt (Findings 61+67, ClawHub 20% context)
6. **TODAY — Noah (no update required):** Apply contextPruning TTL fix: `"ttl": "35m"`
7. **TODAY — Both (no update required):** Add `cron.retry` block to `openclaw.json`
8. **TODAY — Josh:** Verify watchdog Discord notifications before update (Finding 51)
9. **TODAY — Noah:** Fill IDENTITY.md and USER.md via Discord (28 days critical)
10. **Before update — Both:** Backup `openclaw.json` (config-wipe bug protection)
11. Update OpenClaw to 2026.5.7 (both) — fixes session corruption, enables file transfer plugin
12. Verify Discord channel config intact post-update
13. Fix memory-core (Josh: add to allow+entries; Noah: add entries block) + compaction
14. Enable threadBindings in both
15. Fix fallback models (Josh: haiku+flash-lite; Noah: add OpenRouter fallback)
16. Populate HEARTBEAT.md (add lightContext + isolatedSession flags)
17. Create MEMORY.md + memory/ scaffold for both
18. Noah: Install X Search skill post-skills-audit (Finding 64)
19. Monitor for 2026.5.10 stable → tool overrides, A2A, /context map
20. After memory baseline: evaluate memory-lancedb-pro for both

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

### Cron retry — Both instances
```json
"cron": {
  "retry": {
    "maxAttempts": 3,
    "backoffMs": [30000, 60000, 300000, 900000, 3600000],
    "retryOn": ["rate_limit", "overloaded", "network", "server_error"]
  }
}
```
_Backoff: 30s → 1m → 5m → 15m → 60m. Resets after successful run._

### Heartbeat optimization — Both instances (add when activating HEARTBEAT.md)
```json
"heartbeat": {
  "lightContext": true,
  "isolatedSession": true,
  "schedule": "*/30 * * * *"
}
```

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

### Josh fallback fix + Gemini 3.1 Flash-Lite
```json
"fallbacks": [
  "openrouter/google/gemini-3.1-flash-lite-preview",
  "openrouter/google/gemini-2.5-flash",
  "openrouter/anthropic/claude-haiku-4-5"
]
```

### Josh OpenRouter caching (post v2026.5.4 update)
```json
"openrouter": {
  "cacheControl": true
}
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

## Trend Analysis — 28 Days

This fleet has received **zero implementations** across 28 days of documented research.

- Josh's version gap was 7 releases on Day 1. It is now **13 releases**. v2026.5.10 arrives within days.
- Noah's catalyst intelligence gap was 0 on Day 1. It is now **23 days** into Q2 earnings season. Q2 Week 4 (heaviest earnings week) is 4 days away.
- Neither instance has a single memory file. Both wake completely stateless every session.
- Both HEARTBEAT.md files are empty. Neither instance does any proactive work.
- SOUL.md, AGENTS.md, and TOOLS.md are **byte-for-byte identical** between both instances (same SHA in both repos). These critical workspace files have never been customized for the actual use case.
- **Day 28 Run 1:** Two HIGH-risk verifications identified for today (Noah auth mode, Noah skills audit).
- **Day 28 Run 2:** File transfer plugin, heartbeat optimization flags, watchdog notifications, and X Search skill intelligence surfaced. Zero-cost actions available: verify watchdog notifications for both.

**The blocker is execution, not research.** All zero-config actions available today:

| Action | Target | Effort |
|---|---|---|
| Verify Anthropic auth mode | Noah | 5 min |
| Verify watchdog notifications | Both | 5 min each |
| Skills security audit prompt | Noah | 2 min |
| Q2 Week 4 playbook prompt | Noah | 2 min |
| Memory bootstrap prompt | Both | 2 min each |
| contextPruning `"ttl": "35m"` | Noah | 1 min |
| Add `cron.retry` block | Both | 5 min each |

**Estimated total: ~30 minutes for all zero-config actions across both instances.**

---
*Generated by AlphaClaw Apex Fleet Research Agent — Morning Scan Run 2 — 2026-05-15 (Day 28)*
