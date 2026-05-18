# Cross-Customer Analysis — AlphaClaw Apex Fleet

**Last Updated:** 2026-05-18 (Morning Scan — Day 31)
**Instances:** Josh (Heather Schwartz, personal assistant) | Noah (Market Catalyst Agent, stock research)
**Scan cadence:** Morning + Evening daily

---

## Day 31 Morning — New Research (2026-05-18)

### Active Memory Plugin — Fleet-Wide Gap (HIGH for Noah, MEDIUM for Josh post-upgrade)

The Active Memory plugin (blocking memory sub-agent that runs before each main reply, sourced from `memory-core`) has been available since OpenClaw 2026.4.10. Neither instance has it configured.

| Instance | Eligible Now? | Blocker | Action |
|---|---|---|---|
| **Noah** (2026.4.15) | **YES — eligible right now** | No entries block configured | Add `active-memory` entries block + add to allow list |
| **Josh** (2026.3.22) | No — needs upgrade to 2026.4.10+ | OpenClaw version | Stage config now, activate post-upgrade |

**For Noah:** This is the highest-impact configuration change available without any upgrade. A trading agent with 31 sessions of zero memory would immediately benefit: Active Memory auto-surfaces prior catalyst research, risk rules, and company context before each reply.

**Active Memory config (Noah — add under `plugins.entries` NOW):**
```json
"active-memory": {
  "enabled": true,
  "config": {
    "agents": ["main"],
    "chatTypes": ["dm"],
    "inheritSessionModel": true,
    "timeout": 15000,
    "setupGraceTimeoutMs": 5000,
    "maxSummaryChars": 220
  }
}
```
Also add `"active-memory"` to `plugins.allow`.

**Active Memory config (Josh — stage for post-upgrade):**
Same block with `"timeout": 12000` (Gemini is faster than Anthropic for this sub-task).

---

### 2026.5.12 Upgrade: Three Critical Noah Bugs Fixed Simultaneously

Upgrading Noah from 2026.4.15 to 2026.5.12 resolves three distinct session reliability bugs in a single action:

| Bug | Current Impact on Noah | Fixed In |
|---|---|---|
| Context pruning split bug (tool_use/tool_result pairs) | Sessions silently fail mid-prune when tool pairs split → Anthropic API error | 2026.5.x |
| Session corruption bug #75235 (leading-assistant transcript) | 30-min sessions at risk of infinite-loop hang | 2026.5.7 |
| Memory flush bug #19488 (memoryFlush not running) | Noah's correctly-configured flush silently skips before compaction | 2026.5.x |

**All three fixed in 2026.5.12. One upgrade action. No individual workarounds.**

Josh (on 2026.3.22) is not affected by any of these three bugs — he predates the 2026.4.x range where they were introduced.

---

### 2026.5.12 Lazy Install Reduces Josh Upgrade Risk

OpenClaw 2026.5.12 moved WhatsApp, Slack, Amazon Bedrock, Anthropic Vertex, and OpenShell to lazy-install (not pulled unless the integration is enabled). Josh's upgrade from 2026.3.22 will be materially lighter than the 20-release gap suggests — Josh uses Discord, usage-tracker, Google API, and OpenRouter. None are in the lazy-install set.

**Practical impact:** Upgrade is faster, dependency footprint is smaller, recovery from failed upgrade is simpler.

---

### Streaming Gap: Josh Has It Off, Noah Not Set

Josh's config explicitly sets `"streaming": "off"` under `channels.discord`. Noah's config has no `streaming` key (defaults to off for Discord).

Discord block-streaming has been stable since 2026.2.15. For both instances, enabling `"streaming": "block"` would give users real-time feedback on longer tasks:
- **Josh/Heather:** Email drafts, calendar reviews, inbox summaries stream progressively
- **Noah:** Research summaries, catalyst analyses stream progressively rather than appearing silent during 30-second generation windows

**Apply to both:** Change (or add) `"streaming": "block"` under `channels.discord`. No restart required.

---

### Temporal Memory Design — Applies Fleet-Wide

Mem0 April 2026 research: **+29.6 pts on temporal queries, +23.1 pts on multi-hop reasoning** when memory entries include timestamps and relational context.

Neither instance has any memory files. When memory logging begins (critical for both), the format matters:

**Avoid (flat fact):**
```
- Josh dislikes emojis
```

**Use (temporal + contextual):**
```
## 2026-04-15 — Communication Preferences
- Josh explicitly requested no emojis (2026-04-15). STRICT. Applies to all platforms.
- Context: Heather used 🎉 in a Discord group; Josh corrected immediately.
```

This costs zero extra tokens at write time but dramatically improves recall. Applies identically to both instances — Noah's catalyst research entries should use the same format:
```
## 2026-04-22 — $MRNA Catalyst Research
- Q1 2026 earnings beat consensus. Stock +8% next session. Thesis confirmed.
- PDUFA for $MRNA mRNA-4157 originally May 2026 — moved to Q3. Removed from Week 18 consideration.
```

---

### Noah eventQueue.listenerTimeout: Market-Hours Risk

Noah's Discord config: `"eventQueue": {"listenerTimeout": 120000}` (2 minutes). The inboundWorker timeout is 30 minutes.

**Problem:** During pre-market monitoring (4:00–9:30 AM ET), the Discord channel can legitimately go quiet for 10–20 minutes while Noah waits for market-moving news. A 2-minute listener timeout means the event queue resets during these quiet periods, potentially dropping the session mid-monitor.

**Fix:**
```json
"eventQueue": {
  "listenerTimeout": 600000
}
```
(10 minutes — gives room for quiet periods while still having a hard timeout.)

**Josh:** No `eventQueue` configured — default behavior. Not a risk for a personal assistant use case where conversations are bursty but not continuous monitors.

---

## Fleet Overview — Day 31 Morning

| Dimension | Josh / Heather | Noah / Market Catalyst |
|---|---|---|
| OpenClaw version | **2026.3.22** (85+ days old) | **2026.4.15** (26+ days old) |
| Releases behind stable (2026.5.12) | **20 releases** | **13 releases** |
| Releases behind beta (2026.5.16-beta.6) | **22+ releases** | **15+ releases** |
| Primary model | `google/gemini-3-flash-preview` | `anthropic/claude-sonnet-4-6` |
| Fallback model | OpenRouter (gemini-2.5-flash, claude-3.5-haiku ⚠️ retired) | ❌ **None — Anthropic SPOF** |
| Model provider | Google + OpenRouter | Anthropic direct |
| Anthropic auth risk | N/A | ⚠️ `mode: token` — verify API key vs subscription |
| Active Memory plugin | ❌ Not eligible (needs upgrade) | ⚠️ **Eligible NOW — not configured** |
| memory-core plugin | ❌ Not in allow list | ⚠️ In allow list, entries block missing |
| Compaction config | ❌ **None — sessions silently truncate** | ✅ Configured (but bug #19488 blocks flush pre-upgrade) |
| contextPruning | ❌ None | ⚠️ **5m TTL + split bug (2026.4.x) — CRITICAL Day 5** |
| Context pruning split bug | ✅ Not affected (pre-2026.4) | 🔴 **CRITICAL — in affected range** |
| Memory flush bug #19488 | ✅ Not affected | 🔴 **HIGH — flush silently skipping** |
| Session corruption bug #75235 | ✅ Not affected | 🔴 **HIGH — 30-min sessions at risk** |
| Discord streaming | ❌ Explicitly `"off"` | ❌ Not set (default off) |
| eventQueue.listenerTimeout | Not set (default) | ⚠️ **120000ms (2 min) — market-hours risk** |
| HEARTBEAT.md | ⚠️ Empty (168 bytes) | ⚠️ Empty (193 bytes, code-block artifact) |
| MEMORY.md | ❌ Never created | ❌ Never created |
| memory/ directory | ❌ None — 31 sessions | ❌ None — 31 sessions |
| SOUL.md | ⚠️ Base template, unevolved | ⚠️ Base template, wrong persona for trading |
| IDENTITY.md | ✅ Heather (partially filled) | ❌ **Completely blank — Day 31** |
| USER.md | ✅ Josh (populated) | ❌ **Completely blank — Day 31** |
| TOOLS.md | ⚠️ Template only | ⚠️ Template only |
| AGENTS.md | ✅ Standard (identical in both) | ✅ Standard (identical in both) |
| Google account connected | 🔴 **CRITICAL — never connected (31 days)** | ✅ Connected |
| Discord groupPolicy | `open` | `allowlist` (more secure) |
| Discord dmPolicy | `open` | `pairing` (more secure) |
| workspace/reports/ | ❌ Missing | ✅ Exists (stale 27+ days) |
| skills/ directory | ❌ None | ✅ Unaudited — security risk |
| Week 21 playbook | N/A | 🔴 **CRITICAL — Day 1 elapsed, 4 days remain** |
| Days with zero implementations | **31** | **31** |

---

## Platform Risk Summary — Day 31 Morning (All Active)

### Context Pruning Split Bug — 2026.4.x (Noah Only — CRITICAL)
Fix: change `"ttl": "5m"` to `"ttl": "35m"` in `openclaw.json`. **2 minutes. No restart.**

### Session Corruption Bug #75235 (Noah — HIGH)
Fixed in 2026.5.7. Noah must upgrade to 2026.5.12 for the complete fix.

### Memory Flush Bug #19488 (Noah — HIGH)
Noah's `memoryFlush.enabled: true` is silently skipping. Fixed in 2026.5.12.

### Anthropic Auth Disruption Risk (Noah — HIGH)
`mode: "token"` could be subscription auth. Verify: `sk-ant-api...` key = safe. Session token = one policy change from going offline during market hours.

### Config-Wipe Bug During Updates (Both — HIGH)
GitHub issue #65105: updating through certain version ranges can wipe `channels.discord` block. Back up before any upgrade: `cp openclaw.json openclaw.json.bak-2026-05-18`.

### ClawHub Malware (Noah — HIGH)
20% of remaining catalog flagged post-Feb 2026 audit. Noah's `skills/` directory is unaudited. Financial integration categories are the primary target profile.

---

## Workspace File Gap Analysis

### Files Present in Both ✅
- `SOUL.md` — **identical SHA (792306ac)** in both — unevolved after 31 days
- `AGENTS.md` — **identical SHA (3faead97)** in both — generic personal-assistant template in both
- `TOOLS.md` — **identical SHA (917e2fa8)** in both — template-only in both
- `BOOTSTRAP.md` — still present in both (should be deleted post-onboarding)
- `HEARTBEAT.md` — both effectively empty

### Files Josh Has, Noah Missing ⚠️
- **IDENTITY.md populated** — Josh: Heather. Noah: blank template (31 days)
- **USER.md populated** — Josh: name, timezone, employer. Noah: blank (31 days)

### Files Noah Has, Josh Missing ⚠️
- **`workspace/reports/`** — Noah has reports directory (27+ days stale). Josh lacks one.
- **`skills/` directory** — Noah has it (unaudited, security risk). Josh has none.
- **`gogcli/`** — Noah-specific tooling.

### Files Missing in Both ❌
- **`MEMORY.md`** — Day 31 without it in either instance
- **`memory/` directory** — Neither has daily memory logs. Both completely stateless each session.

---

## Configuration Comparison

### Model Strategy

**Josh — Gemini-first with OpenRouter fallback:**
```json
"primary": "google/gemini-3-flash-preview",
"fallbacks": [
  "openrouter/google/gemini-2.5-flash",
  "openrouter/anthropic/claude-3.5-haiku"  // ← RETIRED
]
```
Fix fallback (apply now, no restart):
```json
"fallbacks": [
  "openrouter/google/gemini-3.1-flash-lite-preview",
  "openrouter/google/gemini-2.5-flash",
  "openrouter/anthropic/claude-haiku-4-5"
]
```

**Noah — Anthropic direct, no fallback:**
```json
"primary": "anthropic/claude-sonnet-4-6"
// No fallbacks — SPOF
```
Fix contextPruning (apply now, no restart):
```json
"contextPruning": {
  "mode": "cache-ttl",
  "ttl": "35m"
}
```
Add fallback (post-upgrade recommended):
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

## Shared Config Snippet Library

### Compaction — Josh (add to agents.defaults; Noah already has it)
```json
"compaction": {
  "reserveTokensFloor": 40000,
  "memoryFlush": {
    "enabled": true,
    "softThresholdTokens": 4000
  }
}
```

### contextPruning fix — Noah (apply today, no restart)
```json
"contextPruning": {
  "mode": "cache-ttl",
  "ttl": "35m"
}
```

### Discord streaming — Both instances
```json
"streaming": "block"
```
Add/change under `channels.discord`.

### Active Memory — Noah (add to plugins.entries NOW)
```json
"active-memory": {
  "enabled": true,
  "config": {
    "agents": ["main"],
    "chatTypes": ["dm"],
    "inheritSessionModel": true,
    "timeout": 15000,
    "setupGraceTimeoutMs": 5000,
    "maxSummaryChars": 220
  }
}
```
Also add `"active-memory"` to `plugins.allow`.

### Active Memory — Josh (stage for post-upgrade)
```json
"active-memory": {
  "enabled": true,
  "config": {
    "agents": ["main"],
    "chatTypes": ["dm"],
    "inheritSessionModel": true,
    "timeout": 12000,
    "setupGraceTimeoutMs": 5000,
    "maxSummaryChars": 220
  }
}
```

### memory-core entries block — Noah (apply now, no upgrade needed)
```json
"memory-core": {
  "enabled": true,
  "config": {
    "deduplication": true,
    "temporalDecay": true
  }
}
```

### memory-core full add — Josh (add after upgrade)
```json
"plugins": {
  "allow": ["discord", "usage-tracker", "memory-core", "active-memory"],
  "entries": {
    "discord": {"enabled": true},
    "usage-tracker": {"enabled": true},
    "memory-core": {"enabled": true},
    "active-memory": { /* config above */ }
  }
}
```

### eventQueue timeout fix — Noah
```json
"eventQueue": {
  "listenerTimeout": 600000
}
```
Under `channels.discord`.

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

### Heartbeat optimization — Both instances (add when activating HEARTBEAT.md)
```json
"heartbeat": {
  "lightContext": true,
  "isolatedSession": true,
  "schedule": "*/30 * * * *"
}
```

---

## Priority Matrix — Fleet-Wide (Day 31 Morning)

### CRITICAL — Do Within The Hour
| Item | Josh | Noah |
|---|---|---|
| Connect Google account (31 days) | 🔴 Browser visit to AlphaClaw UI | N/A |
| Fix contextPruning TTL | N/A | 🔴 **`"5m"` → `"35m"` — 2 minutes — Day 5 CRITICAL** |
| Confirm openclaw.json backup | ⬜ Before update | 🔴 **Before any change — 30 seconds** |
| Build Week 21 playbook | N/A | 🔴 **4 days remain — playbook for tonight** |

### HIGH — Today
| Item | Josh | Noah |
|---|---|---|
| Add compaction block | ⬜ 2 min — Finding 77 | ✅ Already configured |
| Enable Discord streaming | ⬜ 1 min — Finding 72 | ⬜ 1 min (add key) |
| Add Active Memory config | N/A (needs upgrade) | ⬜ **Configure NOW — eligible at 2026.4.15** |
| Fix memory-core entries block | N/A | ⬜ Paste entries block — Finding 86 |
| Fix eventQueue listenerTimeout | N/A | ⬜ 120s → 600s — Finding 87 |
| Fix retired fallback model | ⬜ 3 min | N/A |
| Delete BOOTSTRAP.md | ⬜ 30 sec — Day 31 | ⬜ 30 sec — Day 31 |
| Write IDENTITY.md | ⚠️ Partial | ❌ **Blank — Day 31** |
| Write USER.md | ✅ Done | ❌ **Blank — Day 31** |

### MEDIUM — Before End of Week
| Item | Josh | Noah |
|---|---|---|
| Upgrade OpenClaw to 2026.5.12 | 3.22 → 5.12 (lighter than feared) | 4.15 → 5.12 — **fixes 3 bugs** |
| Verify Anthropic auth mode | N/A | ⬜ Verify API key vs subscription |
| Verify watchdog notifications | ⬜ Via Apex dashboard | ⬜ **Before Week 21 market close** |
| Verify ae-target-companies watchlist | N/A | ⬜ 26+ days stale — Week 21 active |
| Check AlphaClaw version | ⬜ `alphaclaw --version` → 0.9.16 | ⬜ Same |

### LOW / OPPORTUNITY
| Item | Josh | Noah |
|---|---|---|
| Stage Active Memory config | ⬜ Design now, activate post-upgrade | ✅ Apply now |
| memory-lancedb-pro | After memory-core baseline | After memory-core baseline |
| Gemini 3.1 Flash Lite fallback | Add (363 tok/s, 1/8 cost) | N/A |
| File transfer plugin | Post-update | Post-update — SEC PDF pipeline |
| Grok OAuth (beta) | Social monitoring — brand awareness | **High value** — real-time X/Twitter catalyst feed |
| defineToolPlugin (beta.6) | Google Workspace as native tools | Alpaca paper trading as native tools |
| claude-opus-4-7 in catalog | N/A | Add `"anthropic/claude-opus-4-7": {}` |
| Populate HEARTBEAT.md | Empty — Day 31 | Empty — Day 31 |
| BlueBubbles Private API iMessage | Evaluate if iMessage remains dark | N/A |

---

## Trend Analysis — Day 31

This fleet has received **zero implementations** across 31 days of documented research.

- **Josh:** Version gap is now 20 stable + 6+ beta releases. Google account has never been connected — zero primary functionality (Gmail, Calendar, Contacts) for 31 consecutive days. Each day adds one more day of lost capability.
- **Noah:** 4 CRITICAL findings. contextPruning TTL fix has been paste-ready for 5 days. Week 21 Day 1 elapsed without a playbook. Tuesday pre-market opens in approximately 11 hours (from morning scan time). The fix backlog is growing faster than it can be worked at the current zero-implementation pace.
- **Both:** 31 sessions of complete statelessness. SOUL.md, AGENTS.md, and TOOLS.md remain byte-for-byte identical between a personal assistant and a trading agent.

**New morning scan additions to the zero-config backlog:**

| Action | Target | Effort |
|---|---|---|
| Fix contextPruning TTL `"5m"` → `"35m"` | Noah | 2 min |
| Add Active Memory config | Noah | 5 min |
| Fix memory-core entries block | Noah | 3 min |
| Fix eventQueue listenerTimeout (2min → 10min) | Noah | 2 min |
| Add compaction block | Josh | 2 min |
| Enable Discord streaming | Both | 1 min each |
| Fix retired fallback | Josh | 3 min |
| Confirm openclaw.json backup | Both | 30 sec each |

**Revised fleet zero-config backlog: ~22 minutes across both instances.** The most impactful items (TTL fix, Active Memory, compaction) take less than 10 minutes combined.

---

*Generated by AlphaClaw Apex Fleet Research Agent — Morning Scan — 2026-05-18 (Day 31)*
