# Cross-Customer Analysis — AlphaClaw Apex Fleet

**Last Updated:** 2026-05-20 (Morning Scan 2 — Day 33)
**Instances:** Josh (Heather Schwartz, personal assistant) | Noah (Market Catalyst Agent, stock research)
**Scan cadence:** Morning + Evening daily

---

## Day 33 Morning-2 — New Research (2026-05-20)

### defineToolPlugin CLI: Both Customers Get Concrete Plugin Build Paths

OpenClaw 2026.5.18 ships a complete plugin development CLI (`openclaw plugins init`, `build`, `validate`) that concretizes previously speculative integration paths for both customers:

| Customer | Plugin Target | Status Before 2026.5.18 | Status After |
|---|---|---|---|
| **Josh** | Google Workspace (Gmail, Calendar, Contacts) | Conceptual — boilerplate-heavy, no scaffold tooling | **Actionable** — scaffold + validate workflow documented |
| **Noah** | Alpaca paper trading (orders, positions, bars) | Conceptual — Finding 85 labeled "medium-term" | **This week** — init/build/validate post-upgrade |

**Shared requirement:** Both customers should run `openclaw plugins validate` before enabling any custom plugin in `plugins.entries`. The 2026.5.18 validator catches tool schema errors that would otherwise silently disable tools at runtime.

**Execution dependency:** Josh's plugin path still requires the Google account to be connected first (Finding 56 — Day 33, unresolved). Noah's Alpaca plugin has no prerequisite beyond the 2026.5.18 upgrade.

---

### Full-Stack OpenTelemetry: Fleet-Wide Session Health Now Observable

2026.5.18 extends OTel coverage to **context assembly** and **memory pressure**, completing full-stack observability for both instances:

| Signal | Josh (Heather) | Noah (Market Catalyst) |
|---|---|---|
| Model calls + token usage | ✅ Prior releases | ✅ Prior releases |
| Tool loops | ✅ Prior releases | ✅ Prior releases |
| **Context assembly** | ✅ New in 2026.5.x | ✅ New in 2026.5.x |
| **Memory pressure** | ✅ New in 2026.5.x | ✅ New in 2026.5.x |
| Exec processes | ✅ Recent | ✅ Recent |

**Josh:** Context assembly timing reveals whether long heartbeat startup times are bootstrap-related or model-latency-related. Memory pressure events alert before compaction fires (compaction currently unconfigured — Finding 80). High value once contextPruning (35m TTL) is active — traces confirm it's working.

**Noah:** Tool loop traces expose EDGAR scan bottlenecks at the individual SEC API call level. Context assembly timing quantifies the cost of the 5m TTL context resets (6 resets × assembly overhead = cumulative session degradation). Post-TTL-fix (35m), traces confirm the improvement quantitatively.

**Shared telemetry setup (post-upgrade — works for both instances):**
```json
"telemetry": {
  "enabled": true,
  "exporters": [{"type": "prometheus", "port": 9090}]
}
```
Prometheus scrape at `/metrics` → Grafana for both VPS instances. Low ops overhead on Hetzner.

---

### AlphaClaw Chrome DevTools MCP: Different Value Per Customer

AlphaClaw 0.8.0 (confirmed from [@chrysb on X](https://x.com/chrysb/status/2032943853012136120)) adds Chrome DevTools MCP — control Mac via OpenClaw from any VPS using Chrome's DevTools Protocol.

| Customer | Primary Use Case | Value | Priority |
|---|---|---|---|
| **Josh** | BlueBubbles iMessage Mac app GUI automation — fallback for iMessage pauses, permission dialogs, macOS system prompts | Enables Heather to interact with Mac desktop apps from Hetzner VPS | MEDIUM — directly relevant to Finding 49/57 (iMessage pause) |
| **Noah** | Alpaca web dashboard screenshots, manual confirmations, account reset flows | Visual verification layer supplementary to Alpaca REST API plugin | MEDIUM — secondary to Finding 101 (Alpaca plugin) |

**AlphaClaw version note (both customers):** Chrome DevTools MCP is available from AlphaClaw 0.8.0+. Both customers' AlphaClaw versions are unverified (target 0.9.16 per prior findings). Verify version before expecting this feature to be available.

**Security note (Josh):** Chrome DevTools MCP should be scoped to specific URLs and apps to prevent unintended access to other Mac applications that may have sensitive content.

---

### 2026.5.18 Upgrade: Noah Now Gets 4 Session Bug Fixes (Not 3)

Updated count — prior cross-customer analysis entries documented 3 bugs fixed in 2026.5.18 for Noah:

| Bug | Fixed In | Notes |
|---|---|---|
| Session corruption bug #75235 | 2026.5.7 | Symptom-level fix |
| Context pruning split bug | 2026.5.x | 5m TTL behavior corrected |
| Memory flush bug #19488 | 2026.5.12 | memoryFlush silent skip resolved |
| **Stale session diagnostics recovery** | **2026.5.18** | **Root-cause fix for session hang pattern** |

The 4th fix (stale session diagnostics recovery) addresses the upstream cause that 2026.5.7 fixed at the symptom level: sessions that failed to clean up properly left stale state that caused subsequent sessions to inherit bad context. This is consistent with the 30-minute EDGAR scan hang pattern.

Single upgrade to 2026.5.18 delivers all four cumulative session reliability fixes. This raises the priority of the upgrade for Wednesday/Thursday/Friday trading sessions this week.

---

### Python Debugging Skill: Fleet-Wide Bundled Capability in 2026.5.18

| Customer | Relevance | When It Matters |
|---|---|---|
| **Josh** | Low immediate relevance | If custom Python utility scripts are developed for iMessage processing or personal data management |
| **Noah** | **Direct relevance** | Once Python EDGAR parsing, catalyst scoring, or Alpaca order logic scripts are in active development |

No installation required post-upgrade. Supports pdb, breakpoint(), post-mortem inspection, debugpy remote attach. The Python debugging skill paired with the Alpaca plugin (Finding 101) enables interactive debugging of trading scripts directly through the agent.

---

### Updated Priority Additions — Day 33 Morning-2

| Action | Josh | Noah | Source Finding |
|---|---|---|---|
| `openclaw plugins init/build/validate` | Post-upgrade + Google OAuth (Finding 56) | **Post-upgrade this week** | Finding 87 / 101 |
| Full-stack OTel telemetry config | Post-upgrade (verifies contextPruning + memory-core) | Post-upgrade (EDGAR tool loop profiling) | Finding 90 / 103 |
| Chrome DevTools MCP (AlphaClaw 0.8.0+) | Verify AlphaClaw version, enable for BlueBubbles | Verify version, enable for Alpaca web UI | Finding 92 / 104 |
| Stale session diagnostics (4th bug) | N/A (not in affected version range) | Additive upgrade urgency — 4th session bug fixed in 2026.5.18 | Finding 102 |

---

## Day 33 Morning — New Research (2026-05-20)

### contextPruning Gap: Josh Has None, Noah Has the 5m Bug — Both Need Fixing

This morning's research into OpenClaw contextPruning best practices confirms both instances are misconfigured in opposite ways:

| Instance | contextPruning Status | Problem | Fix |
|---|---|---|---|
| **Josh** (2026.3.22) | **None — completely absent** | Token debt accumulates silently until compaction fires | Add `{"mode":"cache-ttl","ttl":"35m","keepLastAssistants":3}` |
| **Noah** (2026.4.15) | **5m TTL — Day 6 CRITICAL** | Context pruned every 5 minutes — 6 resets per 30-min session | Change `"ttl":"5m"` → `"ttl":"35m"` |

Community research confirms 35m is optimal for both agent types (personal assistant and trading agent using Anthropic Sonnet with medium tool-call frequency). The `keepLastAssistants: 3` parameter retains the last 3 assistant turns even after aggressive pruning, preserving response coherence.

**Both fixes apply immediately — no restart required.**

---

### memory-core: Josh Missing Entirely, Noah Allowlisted But Unconfigured

| Instance | memory-core Status | Problem | Fix |
|---|---|---|---|
| **Josh** | Not in allow list, no entries block | memory-core not loaded at all | Add to allow list + entries block post-upgrade (needs 2026.4.10+) |
| **Noah** | In allow list — **NO entries block** | Plugin allowlisted but not instantiated | Add entries block now (no upgrade needed) |

**Noah's memory-core inconsistency (confirmed this morning):**
- `plugins.allow`: includes `"memory-core"` ✅
- `plugins.entries`: NO `memory-core` block ❌
- `plugins.load.paths`: does NOT reference memory-core ❌

Result: `memory-core` is not running despite being in the allow list. The Active Memory configuration (Finding 90) and all memory-core functionality depend on this entries block being present.

**Fix for Noah (paste into `plugins.entries`, no restart):**
```json
"memory-core": {
  "enabled": true,
  "config": {
    "deduplication": true,
    "temporalDecay": true
  }
}
```

---

### skills/gog-cli: Wrong Repo, Unaudited, Financial Environment Risk

**Noah's `skills/gog-cli` is a Google OAuth CLI skill installed in the wrong instance:**
- Noah: Anthropic direct, no Google dependency — gog-cli is unused
- Josh: Needs Google Workspace (Gmail, Calendar, Contacts) — gog-cli should be here but isn't

**Security risk (Noah):** ClawHub Feb 2026 audit flagged 20% of financial integration skills for credential exfiltration. Noah's environment handles Alpaca paper trading and SEC EDGAR data. An unaudited Google OAuth skill in this environment represents unnecessary attack surface.

**Action for Noah:** Audit `skills/gog-cli/SKILL.md` and `package.json`. If unused (no reference in `openclaw.json`): remove. If needed: review source for outbound HTTP beyond documented Google OAuth endpoints.

**Note for Josh:** When Google account is connected (Finding 48/56), evaluate whether a gog-cli equivalent is needed — don't assume Noah's copy is usable or safe.

---

### New Platform Capabilities (2026.5.18 Stable) — Fleet Impact

| Capability | Josh Impact | Noah Impact | Status |
|---|---|---|---|
| **Gemini-native TTS** (`gemini-2.5-flash-preview-tts`) | High — Josh has Gemini key; no ElevenLabs needed for voice | Minimal — Anthropic primary, different TTS path | Available post-upgrade to 2026.5.18 |
| **File transfer plugin** (`file_fetch`, `dir_list`, `dir_fetch`, `file_write`) | High — iMessage attachment workflows, file forwarding | Medium — SEC PDF pipeline, EDGAR document storage | Available post-upgrade; 16MB ceiling per round-trip |
| **Docker security hardening** (drops NET_RAW/NET_ADMIN, no-new-privileges) | Applies on upgrade — reduced blast radius if container compromised | Same | Bundled with 2026.5.18 upgrade |
| **Grok OAuth** (xAI, SuperGrok) | Low — social monitoring, brand awareness | **HIGH** — real-time pre-market catalyst intelligence | Stable in 2026.5.18; requires SuperGrok subscription |
| **defineToolPlugin** | Medium — Google Workspace as native agent tools | **HIGH** — Alpaca paper trading as native agent tools | Stable in 2026.5.18 |
| **Cron `--wait` flag** | Medium — synchronous heartbeat patterns (check → if actionable → notify) | **HIGH** — synchronous pre-market EDGAR scan → conditional alert | Stable in 2026.5.18 |
| **Active Memory `allowedChatIds`** | Medium — scope MEMORY.md to Josh's private DM only | Medium — isolate trading vs recap session memory | Stable in 2026.5.18 |

---

### Gemini Semantic Memory: Josh-Specific Zero-Config Benefit

When Josh activates `memory-core` (post-upgrade), OpenClaw automatically uses Gemini embeddings for semantic memory search because `auth.profiles.google:default` is already configured. No additional configuration required.

**Practical difference:**
- Without semantic memory: keyword search only — Heather finds "Oben HiFi" but misses "the speaker company Josh mentioned"
- With semantic memory: contextual retrieval — finds relevant memory even when phrasing doesn't match exact stored text

Noah uses Anthropic direct — would require a separate embedding model configuration. Josh gets this free.

---

## Day 33 Fleet Overview

| Dimension | Josh / Heather | Noah / Market Catalyst |
|---|---|---|
| OpenClaw version | **2026.3.22** (85+ days old) | **2026.4.15** (28+ days old) |
| Releases behind stable (2026.5.18) | **21 releases** | **14 releases** |
| Primary model | `google/gemini-3-flash-preview` | `anthropic/claude-sonnet-4-6` |
| Fallback model | OpenRouter (gemini-2.5-flash, **claude-3.5-haiku ⚠️ retired**) | **None — Anthropic SPOF** |
| Model provider | Google + OpenRouter | Anthropic direct |
| Active Memory plugin | ❌ Not eligible (needs 2026.4.10+) | ⚠️ **Eligible NOW — not configured** |
| memory-core plugin | ❌ Not in allow list | ⚠️ **In allow list — NO entries block** |
| contextPruning | ❌ **NONE — token debt accumulates** | ⚠️ **5m TTL — Day 6 CRITICAL** |
| Compaction config | ❌ **None — platform default (20K, too tight)** | ✅ Configured (40K floor, flush enabled) |
| Discord streaming | ❌ Explicitly `"off"` | ❌ Not set (default off) |
| HEARTBEAT.md | ⚠️ Empty (168 bytes) | ⚠️ Empty (193 bytes + artifact) |
| MEMORY.md | ❌ Never created | ❌ Never created |
| memory/ directory | ❌ None — 33 sessions lost | ❌ None — 33 sessions lost |
| SOUL.md | ⚠️ Base template, unevolved | ⚠️ **Wrong persona — personal assistant template in a trading agent** |
| IDENTITY.md | ✅ Heather (partially filled) | ❌ **Blank template — Day 33** |
| USER.md | ✅ Josh (populated) | ❌ **Blank template — Day 33** |
| TOOLS.md | ⚠️ Template only | ⚠️ **Template only — Day 33** |
| AGENTS.md | ✅ Standard (identical SHA in both) | ✅ Standard (identical SHA in both) |
| Google account connected | 🔴 **CRITICAL — never connected (33 days)** | ✅ Connected |
| Discord groupPolicy | `open` | `allowlist` (more secure) |
| Discord dmPolicy | `open` | `pairing` (more secure) |
| Discord streaming | `"off"` | Not set |
| eventQueue.listenerTimeout | Not set (default) | ⚠️ **120s (2 min) — market-hours quiet period risk** |
| workspace/reports/ | ❌ Missing | ✅ Exists (28+ days stale) |
| skills/ directory | ❌ None | ⚠️ **gog-cli present — unaudited HIGH security risk** |
| Gemini TTS available post-upgrade | ✅ Yes — existing Google key | ❌ No — requires separate config |
| Session corruption bug #75235 | ✅ Not affected (pre-2026.4) | 🔴 **CRITICAL — in affected range, unpatched** |
| Memory flush bug #19488 | ✅ Not affected | 🔴 **HIGH — flush silently skipping** |
| Context pruning split bug (2026.4.x) | ✅ Not affected | 🔴 **CRITICAL — affected** |
| Stale session diagnostics bug | ✅ Not affected | 🔴 **HIGH — partially mitigated, fully fixed in 2026.5.18** |
| Cumulative findings | **93** | **107** |
| Resolved findings | **0** | **0** |
| Days since last implementation | **33** | **33** |

---

## Workspace File Gap Analysis — Day 33

### Files Present in Both ✅ (identical SHA)
- `SOUL.md` — **SHA 792306ac in both** — personal assistant template, unevolved after 33 days
- `AGENTS.md` — **SHA 3faead97 in both** — generic personal assistant template in both
- `TOOLS.md` — **SHA 917e2fa8 in both** — template only in both
- `BOOTSTRAP.md` — still present in both (should be deleted in both — Day 33)
- `HEARTBEAT.md` — both effectively empty

### Files Josh Has, Noah Missing ⚠️
- **IDENTITY.md populated** — Josh: Heather (partial). Noah: blank template (Day 33)
- **USER.md populated** — Josh: name, timezone, employer. Noah: blank (Day 33)

### Files Noah Has, Josh Missing ⚠️
- **`workspace/reports/`** — Noah has reports directory (28+ days stale). Josh lacks one.
- **`skills/gog-cli/`** — Noah has unaudited Google OAuth skill. Josh has no skills directory.
- **`gogcli/`** — Noah-specific root-level tooling.

### Files Missing in Both ❌
- **`MEMORY.md`** — Day 33 without it in either instance
- **`memory/` directory** — Neither has any daily session logs. Both completely stateless across 33 sessions.

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
**Fix fallback now (no restart):**
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
// No fallbacks — single point of failure
```
**Fix contextPruning (now, no restart):**
```json
"contextPruning": {
  "mode": "cache-ttl",
  "ttl": "35m",
  "keepLastAssistants": 3
}
```
**Add fallback (post-upgrade recommended):**
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

### contextPruning — Both Instances (35m is optimal for both agent types)
```json
"contextPruning": {
  "mode": "cache-ttl",
  "ttl": "35m",
  "keepLastAssistants": 3
}
```

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

### memory-core full add — Josh (post-upgrade to 2026.4.10+)
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
        "agents": ["main"],
        "chatTypes": ["dm"],
        "inheritSessionModel": true,
        "timeout": 12000,
        "setupGraceTimeoutMs": 5000,
        "maxSummaryChars": 220
      }
    }
  }
}
```

### Active Memory — Noah (add after memory-core entries block is configured)
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

### Discord streaming — Both instances
```json
"streaming": "block"
```
Add/change under `channels.discord`.

### eventQueue timeout fix — Noah
```json
"eventQueue": {
  "listenerTimeout": 600000
}
```
Under `channels.discord`.

### Gemini-native TTS — Josh (post-upgrade to 2026.5.18, no ElevenLabs required)
```json
"agents": {
  "defaults": {
    "tts": {
      "provider": "gemini",
      "model": "gemini-2.5-flash-preview-tts"
    }
  }
}
```

### Grok model catalog entry — Noah (post-upgrade + Grok OAuth)
```json
"models": {
  "anthropic/claude-opus-4-6": {},
  "anthropic/claude-sonnet-4-6": {},
  "xai/grok-3-mini": {}
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

### Telemetry — Both instances (post-upgrade, Prometheus minimal setup)
```json
"telemetry": {
  "enabled": true,
  "exporters": [{"type": "prometheus", "port": 9090}]
}
```

### Temporal Memory Format — Both instances (when logging begins)
**Avoid (flat fact):**
```
- Josh dislikes emojis
```
**Use (temporal + contextual — +29.6 pts retrieval on temporal queries):**
```
## 2026-04-15 — Communication Preferences
- Josh explicitly requested no emojis (2026-04-15). STRICT. Applies to all platforms.
- Context: Heather used a celebratory emoji in Discord; Josh corrected immediately.
```

---

## Platform Risk Summary — Day 33 (Active)

### Context Pruning Split Bug — 2026.4.x (Noah Only — CRITICAL)
Fix: `"ttl":"5m"` → `"ttl":"35m"` in `openclaw.json`. **2 minutes. No restart. Day 6.**

### Session Corruption Bug #75235 (Noah — CRITICAL)
Fixed in 2026.5.7. Stale session diagnostics root cause fixed in 2026.5.18. Noah must upgrade to 2026.5.18 for the complete fix.

### Memory Flush Bug #19488 (Noah — HIGH)
Noah's `memoryFlush.enabled: true` silently skipping. Fixed in 2026.5.12.

### Stale Session Diagnostics (Noah — HIGH)
Root cause of session hang pattern. Fixed in 2026.5.18. Additive to #75235 fix.

### Anthropic Auth Disruption Risk (Noah — HIGH)
`mode: "token"` could be session auth. Verify API key format: `sk-ant-api03-...` = API key (safe). Anything else = session token risk during market hours.

### Config-Wipe Bug During Updates (Both — HIGH)
GitHub issue #65105: updating through certain version ranges can wipe `channels.discord` block. Back up before any upgrade:
```
cp openclaw.json openclaw.json.bak-pre-5.18
```

### ClawHub Malware — skills/gog-cli (Noah — HIGH)
20% of ClawHub financial integration skills flagged post-Feb 2026 audit. Noah's `skills/gog-cli` is unreviewed. Audit or remove before next upgrade.

---

## Priority Matrix — Fleet-Wide (Day 33 Morning-2)

### CRITICAL — Right Now
| Item | Josh | Noah |
|---|---|---|
| Fix contextPruning (Josh: add, Noah: 5m→35m) | ⬜ **Add 35m TTL — 2 min** | 🔴 **5m→35m — Day 6 — 2 min** |
| Confirm/create Week 21 playbook | N/A | 🔴 **Day 3 of 5 — 15 min** |
| Connect Google account | 🔴 **33 days — 10 min** | N/A |
| Add memory-core entries block | N/A (needs upgrade) | 🔴 **Paste 3 lines — 3 min** |

### HIGH — Today
| Item | Josh | Noah |
|---|---|---|
| Delete BOOTSTRAP.md | ⬜ 30 sec — Day 33 | ⬜ 30 sec — Day 33 |
| Fix retired fallback (claude-3.5-haiku) | ⬜ 3 min | N/A |
| Start daily memory log | ⬜ 5 min | ⬜ 5 min (after market close) |
| Enable Discord streaming | ⬜ 1 min | ⬜ 1 min (add key) |
| Audit skills/gog-cli | N/A | ⬜ **10 min — HIGH security** |
| Verify ae-target-companies watchlist | N/A | ⬜ **30 min — Wednesday active** |

### MEDIUM — Before End of Week
| Item | Josh | Noah |
|---|---|---|
| Verify Node.js ≥ 22.19 | ⬜ `node --version` on VPS | ⬜ Same |
| Back up openclaw.json | ⬜ Before upgrade | ⬜ **Before any change** |
| Upgrade OpenClaw to 2026.5.18 | 3.22 → 5.18 (21 releases) | 4.15 → 5.18 (fixes 4 bugs) |
| Verify Anthropic auth mode | N/A | ⬜ Confirm API key format |
| Apply IDENTITY.md, USER.md, TOOLS.md, SOUL.md | N/A (Josh's are done) | ⬜ **10 min — paste-ready — Day 33** |

### LOW / OPPORTUNITY — Post-Upgrade
| Item | Josh | Noah |
|---|---|---|
| Gemini-native TTS (no ElevenLabs needed) | ⬜ Post-upgrade | N/A |
| File transfer plugin (iMessage attachments / EDGAR PDFs) | ⬜ Post-upgrade | ⬜ Post-upgrade |
| Configure Active Memory + allowedChatIds | ⬜ Post-upgrade | ⬜ Post-upgrade |
| **Alpaca plugin via defineToolPlugin CLI** | N/A | **⬜ This week post-upgrade — concrete path** |
| **Google Workspace plugin via defineToolPlugin CLI** | ⬜ Post-upgrade + Google OAuth | N/A |
| Full-stack OTel telemetry | ⬜ Post-upgrade | ⬜ Post-upgrade (EDGAR profiling) |
| Chrome DevTools MCP (AlphaClaw 0.8.0+) | ⬜ Verify version, enable for BlueBubbles | ⬜ Verify version, enable for Alpaca web UI |
| Grok OAuth — X/Twitter catalyst feed | Low priority | **High value** — post-upgrade |
| Python debugging skill | Post-upgrade (bundled) | Post-upgrade (bundled, relevant to Alpaca scripts) |
| memory-lancedb-pro | After memory-core baseline | After memory-core baseline |
| Populate HEARTBEAT.md with cron --wait | Post-upgrade | Post-upgrade |

---

## Trend Analysis — Day 33

This fleet has received **zero implementations** across 33 days of documented research.

**Josh:** Version gap is 21 stable releases. Google account has never been connected — zero primary functionality (Gmail, Calendar, Contacts) for 33 consecutive days. No memory logs. BOOTSTRAP.md present. The 18-minute implementation queue for the 4 most critical items has been documented for 33 days. Total findings: 93.

**Noah:** 5 CRITICAL findings. contextPruning TTL fix is 2 minutes and has been paste-ready for 6 days. Week 21 Day 3 opens without a confirmed playbook. 107th cumulative finding filed today. Zero resolved. The 2026.5.18 upgrade now delivers 4 session reliability fixes (up from 3 documented previously). Alpaca plugin has a concrete build path via `openclaw plugins init/build/validate` — first time this has been a deliverable rather than a research item.

**Both:** 33 sessions of complete statelessness. SOUL.md, AGENTS.md, and TOOLS.md remain byte-for-byte identical between a personal assistant and a trading agent — confirmed by identical SHA hashes on all three files.

**Day 33 Morning-2 zero-config backlog (unchanged from Morning-1 — approximately 25 minutes for both instances combined):**

| Action | Target | Effort |
|---|---|---|
| Add contextPruning 35m TTL | **Josh** | 2 min |
| Fix contextPruning 5m → 35m | **Noah** | 2 min |
| Add memory-core entries block | **Noah** | 3 min |
| Enable Discord streaming | **Both** | 1 min each |
| Fix retired fallback (claude-3.5-haiku) | **Josh** | 3 min |

---

## Historical Reference — Day 31 Morning Findings (2026-05-18)

*The following are key findings from the Day 31 morning scan preserved for reference. See findings-2026-05-18-morning.md for full detail.*

### Active Memory Plugin — Fleet-Wide Gap
Active Memory plugin (`memory-core`) available since 2026.4.10. Josh not eligible (needs upgrade). Noah eligible now — entries block missing (Finding 97 in Day 33 morning scan above addresses this).

### 2026.5.18 Upgrade: Four Critical Noah Bugs Fixed Simultaneously
| Bug | Noah Impact | Fixed In |
|---|---|---|
| Context pruning split bug | Sessions silently fail mid-prune | 2026.5.x |
| Session corruption bug #75235 | 30-min sessions at risk of hang | 2026.5.7 |
| Memory flush bug #19488 | memoryFlush silently skips | 2026.5.x |
| **Stale session diagnostics** | **Session hang root cause** | **2026.5.18** |
All four fixed in 2026.5.18. One upgrade action.

### Streaming Gap: Josh Has It Off, Noah Not Set
Both should add `"streaming": "block"` under `channels.discord`. No restart required.

### Noah eventQueue.listenerTimeout: Market-Hours Risk
Noah's Discord: `"listenerTimeout": 120000` (2 min). Fix: `600000` (10 min). Applies to quiet pre-market monitoring windows.

---

*Generated by AlphaClaw Apex Fleet Research Agent — Morning Scan 2 — 2026-05-20 (Day 33)*
