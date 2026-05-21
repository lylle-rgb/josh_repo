# Cross-Customer Analysis — AlphaClaw Apex Fleet

**Last Updated:** 2026-05-21 (Morning Scan — Day 34)
**Instances:** Josh (Heather Schwartz, personal assistant) | Noah (Market Catalyst Agent, stock research)
**Scan cadence:** Morning + Evening daily

---

## Day 34 Morning — New Research (2026-05-21)

### OpenClaw 2026.5.19 Now Latest Stable — Version Gaps Updated

The evening scan on 2026-05-21 referenced 2026.5.18 as latest stable. **2026.5.19 became stable on 2026-05-20.** All upgrade target references in prior scans should be updated.

| Instance | Current | New Latest | Gap (releases) | Prior Gap |
|----------|---------|------------|---------------|-----------|
| **Josh** | 2026.3.22 | **2026.5.19** | ~22 releases | 21 releases |
| **Noah** | 2026.4.15 | **2026.5.19** | ~15 releases | 14 releases |

**What 2026.5.19 adds beyond 2026.5.18:**
- Mac app Settings redesign (card-based layouts) — AlphaClaw Apex dashboard UX improvement
- Browser modal dialog handling — relevant to Josh's iMessage/BlueBubbles automation (Finding 49/57)
- Android Talk Mode via Gateway relay voice
- Gateway startup latency optimizations — faster VPS cold-start for both instances
- Pi packages 0.75.1
- 100+ contributor fixes (Discord delivery, streaming, protocol negotiation)
- **Python debugging, node inspector, meme-maker skills bundled** (zero install cost)

**Upgrade target for both instances is now 2026.5.19, not 2026.5.18.**

---

### 2026.5.20-beta.1: Next Train Preview — Fleet Impact

Released 2026-05-21. Do not deploy (beta). Key items to track:

| Feature | Josh Impact | Noah Impact | When |
|---------|------------|------------|------|
| **Discord voice channel-following** | Medium — voice UX | Low | Stable ~7-10 days |
| **xAI device-code OAuth (Grok 3)** | Low — brand monitoring | **HIGH — real-time X/Twitter pre-market catalyst signal** | Stable ~7-10 days |
| **Policy plugin (channel conformance)** | Low | **HIGH — per-channel trading guardrails before live keys** | Stable ~7-10 days |

**Noah tracking priority:** xAI/Grok OAuth is the highest-value single feature in the 2026.5.20 train. Grok 3 has real-time X access baked in — it closes the gap between X/Twitter catalyst signal (often 15-45 min before 8-K filing) and the agent's awareness. Device-code OAuth means no developer portal setup.

**Josh tracking priority:** Lower urgency. Voice channel-following is useful if Josh adopts Discord voice commands.

---

### Python Debugging Skill: Fleet-Wide (Bundled in 2026.5.19)

The Day 33 Morning-2 analysis noted Python debugging as "bundled in 2026.5.18." Confirmed present in 2026.5.19 stable.

| Instance | When Available | Primary Use |
|----------|---------------|-------------|
| **Josh** | Post-upgrade to 2026.5.19 | Custom iMessage processing scripts, personal data utilities |
| **Noah** | **Post-upgrade to 2026.5.19** | **Alpaca plugin dev, EdgarTools parsing, catalyst scoring logic** |

For Noah, this completes the Alpaca plugin development path: upgrade to 2026.5.19 → `openclaw plugins init` → scaffold Alpaca plugin → debug with bundled Python skill. No separate install at any step.

---

### claude-opus-4-7 Available — Noah Model Catalog Expansion

claude-opus-4-7 is now available on Anthropic direct. Noah currently uses claude-sonnet-4-6 as sole primary with no fallbacks.

**Recommendation (applies to Noah):**
- Add opus-4-7 to model catalog (not as primary — cost/speed tradeoff)
- Use explicitly for deep M&A catalyst analysis, FDA pathway research, complex multi-factor scenarios
- Adding to catalog is zero-friction: one JSON key, no restart

```json
// openclaw.json → agents.defaults.models
"anthropic/claude-opus-4-7": {}
```

This also sets up for future Grok integration (xAI device-code OAuth — 2026.5.20-beta.1) by establishing the pattern of a multi-model catalog.

---

### Day 34 Fleet State — Zero Implementations (Day 34 of 34)

| Dimension | Josh / Heather | Noah / Market Catalyst |
|---|---|---|
| OpenClaw version | **2026.3.22** | **2026.4.15** |
| Latest stable | **2026.5.19** | **2026.5.19** |
| Releases behind | **~22** | **~15** |
| contextPruning TTL | ❌ None | 🔴 **5m — Day 7 critical** |
| memory-core | ❌ Not eligible (needs upgrade) | ⚠️ **Allowlisted, no entries block** |
| MEMORY.md | ❌ Never created — Day 34 | ❌ Never created — Day 34 |
| HEARTBEAT.md | ⚠️ Empty | ⚠️ Empty |
| IDENTITY.md | ✅ Heather (partial) | ❌ **Blank template — Day 34** |
| USER.md | ✅ Josh (populated) | ❌ **Blank template — Day 34** |
| Google account | 🔴 **Never connected — 34 days** | ✅ Connected |
| Retired fallback | 🔴 **claude-3.5-haiku (dead)** | N/A |
| Discord streaming | ❌ `"off"` | ❌ Not set |
| skills/gog-cli audit | N/A | ⚠️ **Unaudited in financial env** |
| Cumulative findings | **~103** | **~117** |
| Resolved findings | **0** | **0** |
| Days since last implementation | **34** | **34** |

---

### Day 34 Morning Zero-Config Backlog (25 min for both instances combined, unchanged)

| Action | Target | Effort | Days Documented |
|--------|--------|--------|-----------------|
| Fix contextPruning 5m → 30m | **Noah** | 2 min | **7 days** |
| Connect Google account | **Josh** | 10 min | 34 days |
| Add memory-core entries block | **Noah** | 3 min | 24+ days |
| Enable Discord streaming `progress` | **Both** | 1 min each | 7+ days |
| Fix retired fallback (claude-3.5-haiku) | **Josh** | 3 min | 14+ days |
| Add opus-4-7 to Noah model catalog | **Noah** | 1 min | NEW |

---

## Day 33 Morning-2 — New Research (2026-05-20)

### defineToolPlugin CLI: Both Customers Get Concrete Plugin Build Paths

OpenClaw 2026.5.18 ships a complete plugin development CLI (`openclaw plugins init`, `build`, `validate`) that concretizes previously speculative integration paths for both customers:

| Customer | Plugin Target | Status Before 2026.5.18 | Status After |
|---|---|---|---|
| **Josh** | Google Workspace (Gmail, Calendar, Contacts) | Conceptual — boilerplate-heavy, no scaffold tooling | **Actionable** — scaffold + validate workflow documented |
| **Noah** | Alpaca paper trading (orders, positions, bars) | Conceptual — Finding 85 labeled "medium-term" | **This week** — init/build/validate post-upgrade |

**Shared requirement:** Both customers should run `openclaw plugins validate` before enabling any custom plugin in `plugins.entries`. The 2026.5.18 validator catches tool schema errors that would otherwise silently disable tools at runtime.

**Execution dependency:** Josh's plugin path still requires the Google account to be connected first (Finding 56 — Day 33, unresolved). Noah's Alpaca plugin has no prerequisite beyond the 2026.5.19 upgrade.

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

**Josh:** Context assembly timing reveals whether long heartbeat startup times are bootstrap-related or model-latency-related. Memory pressure events alert before compaction fires (compaction currently unconfigured). High value once contextPruning (35m TTL) is active — traces confirm it's working.

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

AlphaClaw 0.8.0 adds Chrome DevTools MCP — control Mac via OpenClaw from any VPS using Chrome's DevTools Protocol.

| Customer | Primary Use Case | Value | Priority |
|---|---|---|---|
| **Josh** | BlueBubbles iMessage Mac app GUI automation — fallback for iMessage pauses, permission dialogs, macOS system prompts | Enables Heather to interact with Mac desktop apps from Hetzner VPS | MEDIUM — directly relevant to Finding 49/57 (iMessage pause) |
| **Noah** | Alpaca web dashboard screenshots, manual confirmations, account reset flows | Visual verification layer supplementary to Alpaca REST API plugin | MEDIUM — secondary to Finding 101 (Alpaca plugin) |

**AlphaClaw version note:** Chrome DevTools MCP requires AlphaClaw 0.8.0+. Both customers' AlphaClaw versions are unverified (target 0.9.16). Verify version before expecting this feature.

**Security note (Josh):** Chrome DevTools MCP should be scoped to specific URLs and apps to prevent access to sensitive Mac applications.

---

### 2026.5.19 Upgrade: Noah Gets 4 Session Bug Fixes

| Bug | Fixed In | Notes |
|---|---|---|
| Session corruption bug #75235 | 2026.5.7 | Symptom-level fix |
| Context pruning split bug | 2026.5.x | 5m TTL behavior corrected |
| Memory flush bug #19488 | 2026.5.12 | memoryFlush silent skip resolved |
| **Stale session diagnostics recovery** | **2026.5.18+** | **Root-cause fix for session hang pattern** |

All four are fixed in 2026.5.19. Single upgrade action delivers complete remediation.

---

### Python Debugging Skill: Fleet-Wide Bundled Capability

| Customer | Relevance | When It Matters |
|---|---|---|
| **Josh** | Low immediate relevance | Custom Python scripts for iMessage processing or personal data management |
| **Noah** | **Direct relevance** | Alpaca order logic, EDGAR parsing, catalyst scoring scripts |

Bundled in 2026.5.19. No installation required. Supports pdb, breakpoint(), post-mortem inspection, debugpy remote attach.

---

### Updated Priority Additions — Day 33 Morning-2

| Action | Josh | Noah | Source Finding |
|---|---|---|---|
| `openclaw plugins init/build/validate` | Post-upgrade + Google OAuth | **Post-upgrade this week** | Finding 87 / 101 |
| Full-stack OTel telemetry config | Post-upgrade | Post-upgrade (EDGAR profiling) | Finding 90 / 103 |
| Chrome DevTools MCP (AlphaClaw 0.8.0+) | Verify version, enable for BlueBubbles | Verify version, enable for Alpaca web UI | Finding 92 / 104 |
| 4th session bug (stale diagnostics) | N/A | Additive upgrade urgency | Finding 102 |

---

## Day 33 Morning — New Research (2026-05-20)

### contextPruning Gap: Josh Has None, Noah Has the 5m Bug — Both Need Fixing

| Instance | contextPruning Status | Problem | Fix |
|---|---|---|---|
| **Josh** (2026.3.22) | **None — completely absent** | Token debt accumulates silently until compaction fires | Add `{"mode":"cache-ttl","ttl":"35m","keepLastAssistants":3}` |
| **Noah** (2026.4.15) | **5m TTL — Day 7 CRITICAL** | Context pruned every 5 minutes — 6 resets per 30-min session | Change `"ttl":"5m"` → `"ttl":"30m"` |

Community research confirms 30-35m is optimal for both agent types. The `keepLastAssistants: 3` parameter retains the last 3 assistant turns even after aggressive pruning, preserving response coherence.

**Both fixes apply immediately — no restart required.**

---

### memory-core: Josh Missing Entirely, Noah Allowlisted But Unconfigured

| Instance | memory-core Status | Problem | Fix |
|---|---|---|---|
| **Josh** | Not in allow list, no entries block | memory-core not loaded at all | Add to allow list + entries block post-upgrade (needs 2026.4.10+) |
| **Noah** | In allow list — **NO entries block** | Plugin allowlisted but not instantiated | Add entries block now (no upgrade needed) |

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

**Security risk (Noah):** ClawHub Feb 2026 audit flagged 20% of financial integration skills for credential exfiltration. Noah's environment handles Alpaca paper trading and SEC EDGAR data. Unaudited OAuth skill = unnecessary attack surface.

**Action for Noah:** Audit `skills/gog-cli/SKILL.md` and `package.json`. If unused: remove. If needed: review source for unexpected outbound HTTP.

---

### New Platform Capabilities (2026.5.18+) — Fleet Impact

| Capability | Josh Impact | Noah Impact | Status |
|---|---|---|---|
| **Gemini-native TTS** | High — Josh has Gemini key; no ElevenLabs needed | Minimal — Anthropic primary | Post-upgrade |
| **File transfer plugin** | High — iMessage attachments, file forwarding | Medium — SEC PDF pipeline, EDGAR documents | Post-upgrade; 16MB ceiling |
| **Docker security hardening** | Applies on upgrade | Same | Bundled |
| **Grok OAuth** (xAI) | Low — brand monitoring | **HIGH — pre-market X/Twitter catalyst signal** | **2026.5.20-beta.1 — track for stable** |
| **defineToolPlugin** | Medium — Google Workspace plugin | **HIGH — Alpaca paper trading plugin** | Available in 2026.5.18+ |
| **Cron `--wait` flag** | Medium | **HIGH — scan → conditional alert** | Available in 2026.5.18+ |
| **Active Memory `allowedChatIds`** | Medium | Medium | Post-upgrade |

---

### Gemini Semantic Memory: Josh-Specific Zero-Config Benefit

When Josh activates `memory-core` (post-upgrade), OpenClaw automatically uses Gemini embeddings for semantic memory search because `auth.profiles.google:default` is already configured.

- Without: keyword search only — misses contextual references
- With: contextual retrieval — finds "the speaker company Josh mentioned" even without exact text match

Noah (Anthropic direct) would need separate embedding model configuration. Josh gets this free.

---

## Day 33 Fleet Overview

| Dimension | Josh / Heather | Noah / Market Catalyst |
|---|---|---|
| OpenClaw version | **2026.3.22** (85+ days old) | **2026.4.15** (28+ days old) |
| Releases behind stable (now 2026.5.19) | **~22 releases** | **~15 releases** |
| Primary model | `google/gemini-3-flash-preview` | `anthropic/claude-sonnet-4-6` |
| Fallback model | OpenRouter (gemini-2.5-flash, **claude-3.5-haiku ⚠️ retired**) | **None — Anthropic SPOF** |
| Model provider | Google + OpenRouter | Anthropic direct |
| Active Memory plugin | ❌ Not eligible (needs 2026.4.10+) | ⚠️ **Eligible NOW — not configured** |
| memory-core plugin | ❌ Not in allow list | ⚠️ **In allow list — NO entries block** |
| contextPruning | ❌ **NONE — token debt accumulates** | ⚠️ **5m TTL — Day 7 CRITICAL** |
| Compaction config | ❌ **None** | ✅ Configured (40K floor, flush enabled) |
| Discord streaming | ❌ Explicitly `"off"` | ❌ Not set (default off) |
| HEARTBEAT.md | ⚠️ Empty (168 bytes) | ⚠️ Empty (193 bytes + artifact) |
| MEMORY.md | ❌ Never created | ❌ Never created |
| memory/ directory | ❌ None — 34 sessions lost | ❌ None — 34 sessions lost |
| SOUL.md | ⚠️ Base template, unevolved | ⚠️ **Wrong persona — personal assistant template in a trading agent** |
| IDENTITY.md | ✅ Heather (partially filled) | ❌ **Blank template — Day 34** |
| USER.md | ✅ Josh (populated) | ❌ **Blank template — Day 34** |
| TOOLS.md | ⚠️ Template only | ⚠️ **Template only — Day 34** |
| AGENTS.md | ✅ Standard (identical SHA in both) | ✅ Standard (identical SHA in both) |
| Google account connected | 🔴 **CRITICAL — never connected (34 days)** | ✅ Connected |
| Discord groupPolicy | `open` | `allowlist` (more secure) |
| Discord dmPolicy | `open` | `pairing` (more secure) |
| eventQueue.listenerTimeout | Not set (default) | ⚠️ **120s (2 min) — market-hours risk** |
| workspace/reports/ | ❌ Missing | ✅ Exists (stale) |
| skills/ directory | ❌ None | ⚠️ **gog-cli present — unaudited HIGH security risk** |
| Cumulative findings | **~103** | **~117** |
| Resolved findings | **0** | **0** |
| Days since last implementation | **34** | **34** |

---

## Workspace File Gap Analysis — Day 34

### Files Present in Both ✅ (identical SHA)
- `SOUL.md` — **SHA 792306ac in both** — personal assistant template, unevolved after 34 days
- `AGENTS.md` — **SHA 3faead97 in both** — generic personal assistant template in a trading agent (Noah)
- `TOOLS.md` — **SHA 917e2fa8 in both** — template only in both
- `BOOTSTRAP.md` — still present in both (should be deleted in both — Day 34)
- `HEARTBEAT.md` — both effectively empty

### Files Josh Has, Noah Missing ⚠️
- **IDENTITY.md populated** — Josh: Heather (partial). Noah: blank template (Day 34)
- **USER.md populated** — Josh: name, timezone, employer. Noah: blank (Day 34)

### Files Noah Has, Josh Missing ⚠️
- **`workspace/reports/`** — Noah has reports directory (stale). Josh lacks one.
- **`skills/gog-cli/`** — Noah has unaudited Google OAuth skill. Josh has no skills directory.
- **`gogcli/`** — Noah-specific root-level tooling.

### Files Missing in Both ❌
- **`MEMORY.md`** — Day 34 without it in either instance
- **`memory/` directory** — Neither has daily session logs. Both completely stateless across 34 sessions.

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
  "ttl": "30m",
  "keepLastAssistants": 3
}
```
**Add fallback (recommended):**
```json
"model": {
  "primary": "anthropic/claude-sonnet-4-6",
  "fallbacks": [
    "openrouter/anthropic/claude-sonnet-4-6",
    "openrouter/google/gemini-2.5-flash"
  ]
}
```
**Add opus-4-7 to catalog (1 min, no restart):**
```json
"models": {
  "anthropic/claude-opus-4-6": {},
  "anthropic/claude-sonnet-4-6": {},
  "anthropic/claude-opus-4-7": {}
}
```

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

### Active Memory — Noah (after memory-core entries block is configured)
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
"streaming": "progress"
```
Add/change under `channels.discord`.

### eventQueue timeout fix — Noah
```json
"eventQueue": {
  "listenerTimeout": 600000
}
```
Under `channels.discord`.

### Gemini-native TTS — Josh (post-upgrade to 2026.5.18+)
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

### Grok model catalog entry — Noah (when 2026.5.20 stable + xAI OAuth)
```json
"models": {
  "anthropic/claude-opus-4-7": {},
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

### Telemetry — Both instances (post-upgrade)
```json
"telemetry": {
  "enabled": true,
  "exporters": [{"type": "prometheus", "port": 9090}]
}
```

### Temporal Memory Format — Both instances
**Avoid (flat fact):**
```
- Josh dislikes emojis
```
**Use (temporal + contextual):**
```
## 2026-04-15 — Communication Preferences
- Josh explicitly requested no emojis (2026-04-15). STRICT. Applies to all platforms.
- Context: Heather used a celebratory emoji in Discord; Josh corrected immediately.
```

---

## Platform Risk Summary (Active)

### Context Pruning 5m TTL — Noah — CRITICAL (Day 7)
`"ttl":"5m"` → `"ttl":"30m"`. 2 minutes. No restart.

### Session Corruption + 3 Related Bugs — Noah — CRITICAL
All 4 fixed in 2026.5.19. Upgrade is the single action.

### Anthropic Auth Disruption Risk — Noah — HIGH
Verify API key format: `sk-ant-api03-...` = API key (safe). Session token = risk during market hours.

### Config-Wipe Bug During Updates — Both — HIGH
GitHub issue #65105: updating through certain version ranges can wipe `channels.discord` block.
```bash
cp openclaw.json openclaw.json.bak-pre-5.19
```

### ClawHub Malware — skills/gog-cli — Noah — HIGH
20% of ClawHub financial integration skills flagged post-Feb 2026 audit. Audit or remove.

### Google Account Never Connected — Josh — CRITICAL
Day 34. Gmail, Calendar, Contacts: zero functionality. 10-minute fix.

---

## Priority Matrix — Fleet-Wide (Day 34 Morning)

### CRITICAL — Right Now
| Item | Josh | Noah |
|---|---|---|
| contextPruning TTL | ⬜ **Add 30m TTL — 2 min** | 🔴 **5m→30m — Day 7 — 2 min** |
| Connect Google account | 🔴 **34 days — 10 min** | N/A |
| Add memory-core entries block | N/A (needs upgrade) | 🔴 **Paste 3 lines — 3 min** |

### HIGH — Today
| Item | Josh | Noah |
|---|---|---|
| Fix retired fallback (claude-3.5-haiku) | ⬜ 3 min | N/A |
| Enable Discord streaming `progress` | ⬜ 1 min | ⬜ 1 min |
| Add claude-opus-4-7 to catalog | N/A | ⬜ 1 min |
| Audit skills/gog-cli | N/A | ⬜ **10 min — HIGH security** |
| Back up openclaw.json | ⬜ Before upgrade | ⬜ **Before any change** |

### MEDIUM — Before End of Week
| Item | Josh | Noah |
|---|---|---|
| Upgrade OpenClaw to 2026.5.19 | 3.22 → 5.19 (~22 releases) | 4.15 → 5.19 (fixes 4 bugs) |
| Verify Node.js ≥ 22.19 | ⬜ `node --version` | ⬜ Same |
| Create MEMORY.md | ⬜ 5 min | ⬜ 5 min |
| Apply IDENTITY.md, USER.md, TOOLS.md, SOUL.md | N/A (done) | ⬜ **10 min — Day 34** |

### LOW / OPPORTUNITY — Post-Upgrade
| Item | Josh | Noah |
|---|---|---|
| Gemini-native TTS | ⬜ Post-upgrade | N/A |
| File transfer plugin | ⬜ Post-upgrade | ⬜ Post-upgrade |
| Active Memory + allowedChatIds | ⬜ Post-upgrade | ⬜ Post-upgrade |
| Alpaca plugin via defineToolPlugin CLI | N/A | **⬜ This week post-upgrade** |
| Google Workspace plugin | ⬜ Post-upgrade + Google OAuth | N/A |
| OTel telemetry | ⬜ Post-upgrade | ⬜ Post-upgrade |
| Chrome DevTools MCP | ⬜ Verify version | ⬜ Verify version |
| **xAI/Grok OAuth** (2026.5.20 stable) | Low | **⬜ HIGH — when stable** |
| **Policy plugin** (2026.5.20 stable) | Low | ⬜ Before live keys |
| Python debugging skill | Bundled | **Bundled — Alpaca dev** |
| Configure HEARTBEAT.md cron schedule | Post-upgrade | Post-upgrade |

---

## Trend Analysis — Day 34

**Zero implementations across 34 days of documented research.**

**Josh:** Version gap is now ~22 stable releases. Google account has never been connected — zero Gmail/Calendar/Contacts for 34 days. No memory logs. BOOTSTRAP.md still present. contextPruning not configured. ~103 cumulative findings, 0 resolved.

**Noah:** contextPruning TTL at 5m is Day 7 of a documented critical finding — it is a 2-minute paste and the bot runs catalyst analysis sessions daily. 4 known session bugs await a single upgrade action. IDENTITY.md and USER.md have been blank templates for 34 days. ~117 cumulative findings, 0 resolved.

**Both:** 34 sessions of complete statelessness. SOUL.md, AGENTS.md, and TOOLS.md remain byte-for-byte identical (confirmed SHA match) between a personal assistant and a trading agent.

**New this morning:** 2026.5.19 is now the upgrade target (not 2026.5.18). 2026.5.20-beta.1 previews xAI/Grok device-code OAuth — the highest-value near-term capability addition for Noah. Track for stable release in ~7-10 days.

---

*Generated by AlphaClaw Apex Fleet Research Agent — Morning Scan — 2026-05-21 (Day 34)*
