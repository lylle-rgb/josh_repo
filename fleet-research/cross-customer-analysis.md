# Cross-Customer Analysis — AlphaClaw Apex Fleet

**Last Updated:** 2026-05-17 (Morning Scan — Day 30)
**Instances:** Josh (Heather Schwartz, personal assistant) | Noah (Market Catalyst Agent, stock research)
**Scan cadence:** Morning + Evening daily

---

## Day 29 Morning — New Research (2026-05-16)

### v2026.5.16-beta.1 and beta.2 — Two Releases Shipped Today

OpenClaw shipped two beta releases on May 16. Both instances are now further behind:
- **Josh:** 16+ behind beta, 14+ behind stable (2026.5.7)
- **Noah:** 16+ behind beta, 9+ behind stable (2026.5.7)

**Relevant new capabilities:**

| Feature | Shipped | Josh Impact | Noah Impact |
|---|---|---|---|
| Skill snapshot caching | 5.16-beta.1 | Faster restarts post-upgrade | Faster restarts post-upgrade |
| CLI cron wait timeout + exactRun | 5.16-beta.1 | Plan into heartbeat design | Plan into market-scan cron |
| MCP AbortSignal cancellation | 5.16-beta.2 | Safer email/calendar write tools | Safer Alpaca API + SEC EDGAR calls |
| Malformed data rejection (persistence) | 5.16-beta.2 | Prevents inbox-state.json recurrence | Prevents silent session state corruption |
| xAI Grok OAuth | 5.16-beta.1 | Not directly applicable | Not applicable (uses Anthropic direct) |

---

### Context Pruning Split Bug — 2026.4.x (Noah Only — CRITICAL)

OpenClaw documentation confirms a bug in the **2026.4.x version range**: the context pruner removes `tool_result` entries while keeping their paired `tool_use` entries. This creates an invalid conversation state that causes immediate Anthropic API errors.

**Noah is on 2026.4.15 — directly in the affected range.**

Combined with the existing `contextPruning.ttl: "5m"` issue (Finding 62), Noah is experiencing:
1. Context pruned every 5 minutes (too aggressive)
2. Each prune has a chance of splitting tool pairs → API error
3. The agent's 30-minute sessions may be silently failing mid-session

**Josh is on 2026.3.22 — before the bug was introduced. Not affected.**

**Resolution for Noah:** Upgrade to 2026.5.x (fixes the split bug). Interim mitigation: fix the TTL to `"35m"` to reduce prune frequency.

---

### Memory Flush Bug #19488 (Noah Only — HIGH)

GitHub issue #19488 confirms: `compaction.memoryFlush.enabled: true` does not reliably trigger the memory flush step before compaction runs in affected versions. Noah has this correctly configured but the bug may be preventing it from working.

**Practical impact:** Noah's 30-minute sessions compact without saving key catalyst research to memory files. The compaction configuration is correct; the runtime behavior is broken at the current version.

**Resolution:** Upgrade to 2026.5.x. No workaround for the current version.

**Josh:** No compaction configured at all — no memory flush either. Different failure mode, same outcome.

---

### Supermemory Plugin — Automated Memory for Both Instances

`@supermemory/openclaw-supermemory` provides auto-recall (queries memory before each AI turn) + auto-capture (stores conversation after each turn). Requires Supermemory Pro subscription.

**Fleet opportunity:** Both instances operate completely stateless after 29 sessions. The Supermemory plugin automates the memory capture discipline that neither instance has maintained. Evaluate as the memory bootstrap path for both:
- **Josh:** Install after upgrading to 2026.5.7. Provides automated recall of Josh's preferences, calendar patterns, contact context.
- **Noah:** Install after upgrading to 2026.5.7. Provides automated recall of catalyst research, company context, trading decisions. Use alongside `memory-core` (in-session context) for full coverage.

---

### Compaction Gap — Josh Has None, Noah Has It Right

Community recommendation: `reserveTokensFloor: 40000`. The default 20K floor is frequently insufficient for real-world tool output sizes.

- **Noah:** Correctly configured — `reserveTokensFloor: 40000`, `memoryFlush.enabled: true`, `softThresholdTokens: 4000`. However, bug #19488 may be preventing the flush from running.
- **Josh:** No compaction block at all. Long email/calendar sessions silently lose earlier context with no memory flush safety net.

**Josh action (no upgrade required):** Add the compaction block from Noah's config — it's already battle-tested. Takes effect on next session start.

---

## Fleet Overview

| Dimension | Josh / Heather | Noah / Market Catalyst |
|---|---|---|
| OpenClaw version | **2026.3.22** (85+ days old) | **2026.4.15** (23+ days old) |
| Releases behind stable (2026.5.7) | **14+ releases** | **9 releases** |
| Releases behind beta (2026.5.16) | **16+ releases** | **16+ releases** |
| Primary model | `google/gemini-3-flash-preview` | `anthropic/claude-sonnet-4-6` |
| Fallback model | OpenRouter (gemini-2.5-flash, claude-3.5-haiku ⚠️ retired) | ❌ **None — Anthropic SPOF** |
| Model provider | Google + OpenRouter | Anthropic direct |
| Anthropic auth risk | N/A | ⚠️ **`mode: token` — verify API key vs subscription** |
| memory-core plugin | ❌ Not in allow list | ⚠️ In allow list, entries block missing |
| memory-lancedb-pro | ❌ Not installed | ❌ Not installed |
| Supermemory plugin | ❌ Not installed | ❌ Not installed |
| Compaction config | ❌ **None** | ✅ Configured (but bug #19488 may prevent flush) |
| contextPruning | ❌ None | ⚠️ 5m TTL + **split bug in 2026.4.x** |
| Context pruning split bug | ✅ Not affected (pre-2026.4) | 🔴 **CRITICAL — 2026.4.15 in affected range** |
| Memory flush bug #19488 | ✅ Not affected (no flush configured) | 🔴 **HIGH — flush may be silently failing** |
| threadBindings | ❌ Not configured | ❌ Not configured |
| Cron retry config | ❌ Not configured | ❌ Not configured |
| HEARTBEAT.md | ⚠️ Exists, 168 bytes (empty) | ⚠️ Exists, 193 bytes (empty) |
| MEMORY.md | ❌ Never created | ❌ Never created |
| memory/ directory | ❌ None — 29 days | ❌ None — 29 days |
| SOUL.md | ⚠️ Base template, unevolved | ⚠️ Base template, wrong for trading |
| IDENTITY.md | ✅ Partially filled (no avatar) | ❌ **Completely blank — 29 days** |
| USER.md | ✅ Populated (Josh, LA, founder) | ❌ **Completely blank — 29 days** |
| TOOLS.md | ⚠️ Template only | ⚠️ Template only |
| AGENTS.md | ✅ Standard (both identical) | ✅ Standard (both identical) |
| Google account connected | 🔴 **CRITICAL — never connected** | N/A |
| Discord groupPolicy | `open` (anyone in guild) | `allowlist` (specific channel) |
| Discord dmPolicy | `open` | `pairing` |
| workspace/reports/ | ❌ Missing | ✅ Exists (stale 24+ days) |
| skills/ directory | ❌ None | ✅ Unaudited — security risk (ClawHub 20% flagged) |
| Week 21 playbook (May 19–23) | N/A | 🔴 **CRITICAL — missing, 72hr window** |
| Days with zero implementations | **29** | **29** |

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

**Noah (ELEVATED URGENCY):** A trading agent crash during pre-market (4-9:30 AM ET), market open (9:30-10:30 AM ET), or earnings window (4-5 PM ET) is a meaningful operational failure. Given the Anthropic auth disruption risk (Finding 58 from prior scans), Noah could go offline due to an external policy change — watchdog notifications are the only way the operator knows immediately. **Verify watchdog Discord notifications before Week 21 market open (May 19).**

**This is a fleet-operator setting in the AlphaClaw Apex dashboard, not an `openclaw.json` change.**

---

### X Search Skill — Noah Priority, Josh Future

**Noah:** X Search enables natural-language search over X content: executive commentary ahead of earnings, analyst reactions to 8-K filings, short-seller threads, pre-PDUFA biotech sentiment. High-alpha, real-time source for catalyst hunting. **Requires skills/ security audit first.** Only evaluate read-only X Search (no `write` permission).

**Josh:** X monitoring for brands (future). Lower urgency. Post-baseline.

**Both:** ~20% of ClawHub catalog was flagged in the Feb 2026 security audit. Any X skill must be verified against the official safe list before install.

---

### ClawHub Security Context Update — 20% Flagged (Noah Priority)

Updated intelligence: ClawHub's Feb 2026 audit flagged **~20% of all skills** on the marketplace:
- 1,184 confirmed malicious (credential-stealing, wallet-draining)
- 2,419 suspicious (insecure patterns, unexpected network calls)
- ~20% of remaining catalog flagged for review

Skills installed before Feb 2026 should be treated as potentially unreviewed. The malicious skills specifically targeted financial integration categories — Noah's `skills/` directory is the highest-risk surface on this fleet.

---

## Platform Risk Summary — Day 29 (Updated Morning Scan)

### Context Pruning Split Bug — 2026.4.x (Noah Only — CRITICAL)

In the 2026.4.x version range, the context pruner can split `tool_use`/`tool_result` pairs, causing immediate Anthropic API errors. Noah on 2026.4.15 is directly affected. Combined with the 5m TTL, Noah's sessions may be silently failing mid-session during market hours.

**Resolution:** Upgrade to 2026.5.7. Interim: fix TTL to `"35m"` to reduce prune frequency.

---

### Memory Flush Bug #19488 (Noah Only — HIGH)

Noah's correctly configured memory flush (`memoryFlush.enabled: true`) may not be running before compaction due to this known bug in affected OpenClaw versions. Session state is compacted without being saved to memory files.

**Resolution:** Upgrade to 2026.5.7. No workaround for current version.

---

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
```bash
cp openclaw.json openclaw.json.bak-2026-05-16
```

---

### Session Corruption Bug #75235 (Noah Priority — HIGH)

A leading-assistant transcript triggers an infinite loop, hanging the session permanently. Long-running sessions (Noah's 30-minute research runs) are most at risk. Fixed in 2026.5.7. Soft restart (`--soft`) resolves a hung session.

---

### ClawHub Malware — Noah's skills/ Directory (HIGH)

1,184 malicious skills + 2,419 suspicious skills purged in early 2026; ~20% of remaining catalog flagged. Noah's trading agent with Alpaca access is the exact target profile. Skills audit is a security priority, not routine housekeeping.

---

## Workspace File Gap Analysis

### Files Present in Both ✅
- `SOUL.md` — **identical content in both repos (same SHA: 792306ac)** — unevolved in both after 29 days
- `AGENTS.md` — **identical content in both repos (same SHA: 3faead97)** — generic personal-assistant template in both
- `TOOLS.md` — **identical content in both repos (same SHA: 917e2fa8)** — both template-only, nothing filled in
- `BOOTSTRAP.md` — present in both
- `HEARTBEAT.md` — present in both, both effectively empty

**Note:** Three workspace files are byte-for-byte identical between the two instances after 29 days. This is a gap: Heather's SOUL.md should reflect a personal assistant persona; Market Catalyst Agent's SOUL.md should reflect a disciplined trading analyst persona. Neither has evolved.

### Files Josh Has, Noah Missing ⚠️
- **IDENTITY.md populated** — Josh has name/creature/vibe set. Noah's is blank template. **CRITICAL for Noah — 29 days.**
- **USER.md populated** — Josh's has name, timezone, employer, preferences. Noah's is blank. **CRITICAL for Noah — 29 days.**

### Files Noah Has, Josh Missing ⚠️
- **`workspace/reports/`** — Noah has a reports directory (24+ days stale). Josh lacks one.
- **`skills/` directory** — Noah has the skills directory (unaudited, security risk). Josh has none.
- **`gogcli/`** — Noah-specific tooling.

### Files Missing in Both ❌
- **`MEMORY.md`** — Neither has created a long-term memory file. AGENTS.md instructs both to create and maintain this. 29 days without it.
- **`memory/` directory** — Neither has daily memory logs. Both operate completely stateless each session.
  - **Exception:** Josh has `workspace/memory/` with two files: `inbox-state.json` (malformed — duplicate key) and `onboarding-google.md`. Neither counts as functional memory.

---

## Configuration Comparison

### Model Strategy

**Josh (Gemini-first with OpenRouter fallback):**
```json
"primary": "google/gemini-3-flash-preview",
"fallbacks": [
  "openrouter/google/gemini-2.5-flash",
  "openrouter/anthropic/claude-3.5-haiku"  // ⚠️ RETIRED
]
```
- ⚠️ `claude-3.5-haiku` fallback is retired — final fallback tier non-functional
- `google/gemini-3.1-flash-lite-preview` available as faster/cheaper option
- Gemini 3 Flash Preview: 1M context, 380 tok/s, 66K output — strong for personal assistant tasks
- **No compaction configured** — long email+calendar sessions may silently truncate context

**Recommended fixes (post-update):**
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
- `claude-opus-4-7` is available but not in the model catalog
- Compaction: ✅ correctly configured but possibly affected by bug #19488
- contextPruning `ttl: "5m"` — too aggressive + 2026.4.x split bug

**Recommended (apply today — no update required):**
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

**Key difference:** Noah's allowlist + pairing is significantly more locked down. Josh's open+open posture means any Discord user in the guild can DM Heather.

---

### Memory Architecture

**Josh:**
- No `memory-core` plugin — not even in allow list
- `workspace/memory/` exists with 2 files — inbox-state.json (malformed), onboarding-google.md
- No compaction = sessions accumulate context without memory preservation
- Upgrade path: compaction config now (Finding 58) → memory-core → Supermemory plugin → memory-lancedb-pro

**Noah:**
- `memory-core` in `plugins.allow` but absent from `plugins.entries` — never loads, 29 days
- `memoryFlush.enabled: true` ✅ — correctly configured but bug #19488 may prevent it from running
- contextPruning `ttl: "5m"` + split bug (2026.4.x) = dual failure mode
- Upgrade path: fix TTL now → upgrade to 2026.5.7 → add entries block for memory-core → create memory/ directory → Supermemory plugin

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

---

## Priority Matrix — Fleet-Wide (Day 29 Morning)

### CRITICAL — Do Within 24 Hours
| Item | Josh | Noah |
|---|---|---|
| Connect Google account (29 days!) | 🔴 **Browser visit to AlphaClaw UI** | N/A |
| Fix contextPruning TTL | N/A | 🔴 **`"5m"` → `"35m"` — 2 minutes** |
| Confirm openclaw.json backup | ⬜ Before update | 🔴 **Before update — 30 seconds** |
| Build Week 21 playbook | N/A | 🔴 **Before May 19 market open — 72hr window** |

### HIGH — Do This Weekend
| Item | Josh | Noah |
|---|---|---|
| Fix retired fallback model | ⬜ `claude-3.5-haiku` → `claude-haiku-4-5` | N/A |
| Fix inbox-state.json | ⬜ Remove duplicate key + unpause iMessage | N/A |
| Add no-emoji rule to SOUL.md | ⬜ One sentence | N/A |
| Add compaction config | ⬜ Paste 5-line JSON block | Already configured ✅ |
| Fill IDENTITY.md | ⚠️ Partial (missing avatar) | ❌ **Blank — 29 days** |
| Fill USER.md | ✅ Done | ❌ **Blank — 29 days** |
| Fill TOOLS.md | ❌ Template only | ❌ Template only |
| Update SOUL.md | ❌ Generic template | ❌ Wrong persona for trading |
| Verify Anthropic auth mode | N/A | ⬜ Verify API key vs subscription |
| Verify watchdog notifications | ⬜ Via Apex dashboard | ⬜ **Before May 19 market open** |

### MEDIUM — Before Next Week
| Item | Josh | Noah |
|---|---|---|
| Update OpenClaw to 2026.5.7 | 3.22 → 5.7 (14+ releases) | 4.15 → 5.7 (9 releases) — **fixes split bug + flush bug** |
| Create MEMORY.md | ❌ Never created | ❌ Never created |
| Create memory/ directory | Partial (malformed) | ❌ Missing |
| Evaluate Supermemory plugin | Post-upgrade | Post-upgrade |
| Skills security audit | N/A | ⬜ ClawHub 20% flagged |

### LOW / OPPORTUNITY
| Item | Josh | Noah |
|---|---|---|
| memory-core plugin | Add to allow + entries | Fix entries block |
| memory-lancedb-pro | After memory-core baseline | After memory-core baseline |
| Fallback model fix | Fix retired haiku + add flash-lite | Add OpenRouter fallback (SPOF) |
| File transfer plugin | Post-update | Post-update — SEC PDF pipeline |
| Heartbeat lightContext/isolatedSession | On HEARTBEAT activation | On HEARTBEAT activation |
| Populate HEARTBEAT.md | Empty — 29 days | Empty — 29 days |
| Add cron retry config | Post-heartbeat | Post-heartbeat |
| X Search skill | Future | Post-skills-audit |
| A2A 20-turn pipelines | Future | Full catalyst pipeline |
| claude-opus-4-7 in catalog | N/A | Add to models config |

---

## Shared Config Snippet Library

### Compaction — Josh (add this — Noah already has it)
```json
"compaction": {
  "reserveTokensFloor": 40000,
  "memoryFlush": {
    "enabled": true,
    "softThresholdTokens": 4000
  }
}
```

### contextPruning fix — Noah (apply today, no restart required)
```json
"contextPruning": {
  "mode": "cache-ttl",
  "ttl": "35m"
}
```

### threadBindings (session level — both instances)
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

### Josh fallback fix (retire haiku + add flash-lite)
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

---

## Trend Analysis — 29 Days

This fleet has received **zero implementations** across 29 days of documented research.

- Josh's version gap was 7 releases on Day 1. It is now **14+ releases behind stable, 16+ behind beta**. Each new release adds capabilities that cannot be used and bugs that cannot be fixed.
- Noah's catalyst intelligence gap is now **29 days into Q2 earnings season**. Week 21 (May 19–23) market open is 72 hours away with no playbook, no memory system, and 6 critical open findings.
- Neither instance has a single memory file that persists across sessions. Both wake completely stateless every session.
- Both HEARTBEAT.md files are empty. Neither instance does any proactive work.
- SOUL.md, AGENTS.md, and TOOLS.md are **byte-for-byte identical** between both instances (same SHA in both repos). 29 sessions have started with generic personal-assistant personas for both a personal assistant AND a trading bot.
- **Day 29 adds two new CRITICAL findings for Noah:** context pruning split bug (2026.4.x) and memory flush bug (#19488). Noah's correctly-configured memory infrastructure may be silently failing to work at the current version.

**The blocker is execution, not research.** Zero-config actions available right now:

| Action | Target | Effort |
|---|---|---|
| Fix contextPruning TTL `"5m"` → `"35m"` | Noah | 2 min |
| Confirm openclaw.json backup | Noah | 30 sec |
| Fix retired fallback `claude-3.5-haiku` | Josh | 3 min |
| Fix inbox-state.json | Josh | 5 min |
| Add no-emoji rule to SOUL.md | Josh | 2 min |
| Add compaction config | Josh | 3 min |
| Verify watchdog notifications | Both | 5 min each |
| Verify Anthropic auth mode | Noah | 5 min |
| Start Week 21 playbook | Noah | This weekend — time-boxed |

**Estimated total: ~35 minutes for all zero-config actions across both instances.**

---

## Day 30 Morning — New Research (2026-05-17)

### OpenClaw 2026.5.8–2026.5.12 — Five Releases Shipped Overnight

Both instances are now further behind than they were 24 hours ago:
- **Josh:** 19 releases behind stable (2026.3.22 vs 2026.5.12), 22 behind beta (2026.5.14-beta.2)
- **Noah:** 12 releases behind stable (2026.4.15 vs 2026.5.12), 14 behind beta

**Fleet-wide impact of 2026.5.12 (most impactful release):**

| Feature | Josh Impact | Noah Impact |
|---|---|---|
| Heartbeat multi-agent repair | HEARTBEAT.md prose now reaches model reliably — design tasks now, activate post-upgrade | Same — but HEARTBEAT.md has a code-block wrapper artifact that must be cleaned up first (Finding 85) |
| OAuth stale lock reclamation | May silently unblock Finding 56 (Google account, 30 days blocked) | Not directly applicable (Anthropic direct, not OAuth) |
| Monotonic transcript sequence | Stable session history across Discord stream reconnects | Critical for 30-min sessions — transcript won't corrupt through context prunes post-upgrade |
| Auth persistence | Token held for full session (applies to OpenRouter fallbacks) | Anthropic direct token held for full 30-min inboundWorker session — no per-call re-resolution |
| Plugin peer link preservation | memory-core peer links survive updates once configured | Same — memory-core (currently half-configured) will not need re-pairing after plugin updates |
| SecretRef credential resolution | ${DISCORD_BOT_TOKEN} continues to work; SecretRef registration is post-upgrade best practice | Not currently applicable (auth via direct token, not env-var interpolation) |

---

### Heartbeat Reliability — Both Instances Benefit Equally From 2026.5.12

The 2026.5.12 heartbeat multi-agent repair is the single most impactful improvement for both instances, for different reasons:

- **Josh (empty HEARTBEAT.md):** Prior hesitation to configure heartbeat was justified by reliability gaps. Those gaps are now fixed. The window to design HEARTBEAT.md is now. Post-upgrade + post-Google-connection activation can be immediate.
- **Noah (template code block wrapper in HEARTBEAT.md):** The HEARTBEAT.md contains a Markdown code block wrapping the template instructions rather than plain comments. This is a structural artifact that must be removed before any heartbeat tasks are added. Corrected clean HEARTBEAT.md content documented in Finding 85 (Day 30 Morning).

Neither instance has any active heartbeat tasks after 30 days of operation. Both are completely reactive — no proactive work happens without a direct Discord message.

---

### Week 21 Market Timing — Noah Existential Deadline

Week 21 (May 19–23, 2026) markets open in approximately 36 hours from this morning scan. The Noah instance's core mission — catalyst-hunting and paper trading — cannot function correctly for Week 21 without:

1. **contextPruning TTL fix** (2-minute change, not applied for 3 days) — every session context resets every 5 minutes
2. **A Week 21 playbook** — no binary event schedule, no position sizing rules, no daily protocol
3. **A verified watchlist** — ae-target-companies.md is 25 days stale, some catalysts may be resolved

Today (Sunday May 17) is the last low-noise prep window. This is a Noah-specific deadline with no parallel in Josh's deployment.

---

### Shared Gap: SOUL.md / AGENTS.md / TOOLS.md Still Identical

After 30 days and 60+ daily scan files, the three foundational workspace documents remain byte-for-byte identical between both instances:

| File | Josh SHA | Noah SHA | Identical? |
|---|---|---|---|
| SOUL.md | (personal assistant template) | 792306ac (same) | YES — 30 days |
| AGENTS.md | (personal assistant template) | 3faead97 (same) | YES — 30 days |
| TOOLS.md | (blank template) | 917e2fa8 (same) | YES — 30 days |

A personal assistant bot and a stock catalyst trading agent have started every session for 30 days with the same soul, the same workspace rules, and the same empty tool notes. The divergence work (SOUL.md trading rewrite, AGENTS.md trading protocols, TOOLS.md actual tool inventory) is documented in soul-improvements files for both repos — but has not been applied.

---

### New Zero-Config Actions Available This Morning

| Action | Target | Effort | Urgency |
|---|---|---|---|
| Fix contextPruning TTL `"5m"` → `"35m"` | Noah | 2 min | NOW — Day 3 CRITICAL |
| Confirm openclaw.json backup | Noah | 30 sec | NOW |
| Send watchlist refresh Discord message | Noah | 1 min | TODAY — last low-noise window |
| Write Week 21 playbook (minimal) | Noah | 20 min | TODAY — 36h to market open |
| Fix HEARTBEAT.md template wrapper | Noah | 2 min | TODAY — before heartbeat config |
| Fix retired fallback `claude-3.5-haiku` | Josh | 3 min | NOW |
| Design HEARTBEAT.md task list | Josh | 15 min | This weekend |

**Cumulative fleet zero-config backlog: ~45 minutes across both instances.**

---

## Fleet Snapshot — Day 30 Morning

| Metric | Josh | Noah |
|---|---|---|
| OpenClaw version | 2026.3.22 | 2026.4.15 |
| Releases behind stable | 19 | 12 |
| Releases behind beta | 22 | 14 |
| Open findings | 70 | 85 |
| Resolved findings | 0 | 0 |
| IDENTITY.md | ✅ Filled (Heather) | ❌ Blank template |
| USER.md | ✅ Filled (Josh) | ❌ Blank template |
| TOOLS.md | ❌ Template only | ❌ Template only |
| SOUL.md | ❌ Generic PA template | ❌ Generic PA template |
| HEARTBEAT.md | ❌ Empty | ❌ Code-block wrapper artifact |
| Daily memory logs | ❌ None | ❌ None |
| Memory system | ❌ Not configured | ❌ Half-configured (entries block missing) |
| Google account | ❌ Not connected (30 days) | ✅ Connected (Ngkatz.ai@gmail.com) |
| Primary model | google/gemini-3-flash-preview | anthropic/claude-sonnet-4-6 |
| Fallback chain | ⚠️ Includes retired claude-3.5-haiku | ❌ No fallbacks (SPOF) |
| Streaming | ❌ Off | N/A |
| Trading skills | N/A | ❌ None installed |
| Week 21 playbook | N/A | ❌ Missing — 36h to open |
| contextPruning TTL | Not configured | ❌ 5m (CRITICAL — 3 days) |

---

*Generated by AlphaClaw Apex Fleet Research Agent — Morning Scan — 2026-05-17 (Day 30)*
