# Cross-Customer Analysis — AlphaClaw Apex Fleet

**Last Updated:** 2026-05-24 (Morning Scan — Day 36)
**Instances:** Josh (Heather Schwartz, personal assistant) | Noah (Market Catalyst Agent, stock research)
**Scan cadence:** Morning + Evening daily

---

## Day 36 Morning — New Research (2026-05-24)

### OpenClaw 2026.5.22-beta.1 Released — Next Stable Wave in Train

A new beta train shipped overnight. **Stable target remains 2026.5.20 for both instances.** 2026.5.22 stable ETA ~7-10 days.

| Instance | Current | Stable Target | Beta Preview | Gap |
|----------|---------|--------------|--------------|-----|
| **Josh** | 2026.3.22 | **2026.5.20** | 2026.5.22-beta.1 | ~23 stable releases |
| **Noah** | 2026.4.15 | **2026.5.20** | 2026.5.22-beta.1 | ~16 stable releases |

**What 2026.5.22-beta.1 previews (track for stable ~7-10 days):**

| Feature | Josh Impact | Noah Impact |
|---------|------------|------------|
| **Meeting Capture (pluginized)** | HIGH — Discord voice → Heather memory | LOW |
| **OpenRouter Routing Controls** | HIGH — Josh uses OpenRouter fallback chain | LOW |
| **Package Integrity Gates** | MEDIUM — safer future skill installs | MEDIUM — financial env, ClawHub risk |
| **Cron + CLI Reliability** | LOW | HIGH — pre-market pipeline hardening |
| **Approval + Policy Hardening** | LOW | HIGH — safety layer before live trading |
| **Row-Level Session Helpers** | LOW | MEDIUM — cleaner Alpaca/EDGAR plugin SDK |
| **xAI Device-Code Refinement** | LOW | HIGH — Grok 3 X/Twitter catalyst signal |
| **Discord Voice Upgrades** | MEDIUM — Josh uses voice | LOW |
| **Faster Startup** | MEDIUM — Hetzner VPS | MEDIUM |
| **SecretRef Guidance** | LOW | LOW |

**Meeting Capture detail (Josh-specific):** Discord voice is the first live source for the new meeting-notes plugin. Once Heather upgrades to 2026.5.20 then 2026.5.22 stable, Josh's Discord voice conversations can be auto-captured and fed into Heather's memory system. This closes the persistent gap where voice conversations leave zero record.

**OpenRouter Routing Controls detail (Josh-specific):** Josh's fallback chain uses OpenRouter exclusively. New routing controls likely provide finer model selection, cost caps, or regional routing. Combined with fixing the dead `claude-3.5-haiku` fallback (Day 16 unresolved), this will give Josh's instance a much more reliable fallback posture post-upgrade.

**Cron reliability + Policy hardening (Noah-specific):** Both features are directly on Noah's critical path. Cron reliability hardens the pre-market 6 AM / post-market 5 PM pipeline. Policy/approval hardening is the safety layer that MUST be configured before Noah ever moves from paper to live trading.

---

### Day 36 Escalations

**Josh — Dead Fallback Model: Day 16 (3-minute fix, still unresolved)**
`openrouter/anthropic/claude-3.5-haiku` is retired. Every fallback attempt silently fails. This has been documented for 16 days.
```json
"fallbacks": [
  "openrouter/google/gemini-3.1-flash-lite-preview",
  "openrouter/google/gemini-2.5-flash",
  "openrouter/anthropic/claude-haiku-4-5"
]
```

**Noah — contextPruning 5m TTL: Day 9 CRITICAL (2-minute fix, still unresolved)**
6 context resets per 30-minute catalyst session. Every research session degrades mid-way.
```json
"contextPruning": {
  "mode": "cache-ttl",
  "ttl": "30m",
  "keepLastAssistants": 3
}
```

**Both — Zero implementations: Day 36**
No customer has resolved a single finding in 36 days of documented research.

---

### Day 36 Fleet State

| Dimension | Josh / Heather | Noah / Market Catalyst |
|---|---|---|
| OpenClaw version | **2026.3.22** | **2026.4.15** |
| Latest stable | **2026.5.20** | **2026.5.20** |
| Beta train | 2026.5.22-beta.1 | 2026.5.22-beta.1 |
| Gap (releases) | **~23** | **~16** |
| contextPruning TTL | ❌ None configured | 🔴 **5m — Day 9 CRITICAL** |
| memory-core | ❌ Not eligible (needs upgrade) | ⚠️ Allowlisted, **no entries block — Day 26** |
| MEMORY.md | ❌ Never created — **Day 36** | ❌ Never created — **Day 36** |
| HEARTBEAT.md | ⚠️ Empty | ⚠️ Empty |
| IDENTITY.md | ✅ Heather (partial) | ❌ **Blank template — Day 36** |
| USER.md | ✅ Josh (populated) | ❌ **Blank template — Day 36** |
| Google account | 🔴 **Never connected — 36 days** | ✅ Connected |
| Dead fallback | 🔴 **claude-3.5-haiku — Day 16** | N/A |
| Discord streaming | ❌ `"off"` | ❌ Not set |
| skills/gog-cli audit | N/A | ⚠️ **Unaudited — HIGH security** |
| Active Memory allowedChatIds | ⚠️ Not configured | ⚠️ Not configured |
| Meeting capture | 🔸 Coming in 2026.5.22 stable | N/A |
| Cron reliability | N/A | 🔸 Hardening in 2026.5.22 |
| Policy/approval hardening | N/A | 🔸 Critical before live trading |
| Cumulative findings | **~109** | **~125** |
| Resolved findings | **0** | **0** |
| Days since last implementation | **36** | **36** |

---

### Day 36 Zero-Config Backlog (unchanged — still 0 items done in 36 days)

| Action | Target | Effort | Days Documented | Status |
|--------|--------|--------|-----------------|--------|
| Fix dead claude-3.5-haiku fallback | **Josh** | 3 min | **16 days** | 🔴 DO IT |
| Fix contextPruning 5m → 30m | **Noah** | 2 min | **9 days** | 🔴 DO IT |
| Add memory-core entries block | **Noah** | 3 min | **26+ days** | 🔴 DO IT |
| Add opus-4-7 to Noah catalog | **Noah** | 1 min | 3 days | ✅ Easy |
| Enable Discord streaming `progress` | **Both** | 1 min each | 9+ days | ✅ Easy |
| Connect Google account | **Josh** | 10 min | **36 days** | 🔴 DO IT |

**Total: ~21 minutes. Zero upgrades required. Still 0 of this done.**

---

## Day 35 Morning — New Research (2026-05-22)

### OpenClaw 2026.5.21-alpha.1 Released — Stable Target Unchanged at 2026.5.20

An alpha shipped overnight. Both instances should remain on their upgrade path to **2026.5.20 stable** — do not deploy alpha to either instance.

| Instance | Current | Stable Target | Alpha (do not deploy) | Gap |
|----------|---------|--------------|----------------------|-----|
| **Josh** | 2026.3.22 | **2026.5.20** | 2026.5.21-alpha.1 | ~2 months |
| **Noah** | 2026.4.15 | **2026.5.20** | 2026.5.21-alpha.1 | ~37 days |

**What 2026.5.21-alpha.1 previews (track for next stable wave ~3-7 days):**
- **Voice-first improvements:** Paced audio streaming, backpressure-aware buffering, barge-in queue clearing — relevant to Heather post-upgrade
- **Discord voice channel-following confirmed stable in 2026.5.20** (not just alpha)
- **Bounded partial recall in Active Memory** — sub-agent timeout no longer discards all recovered context; partial summary preserved
- **cron `--wait`** with timeout + poll-interval controls confirmed working in 2026.5.x train

---

### Active Memory allowedChatIds — Fleet-Wide Privacy Implication

Active Memory's `allowedChatIds`/`deniedChatIds` controls are confirmed stable in 2026.5.x. This is the most important new configuration pattern for both instances because both have Discord policies that could expose private memories to non-owners.

| Instance | Risk Without allowedChatIds | Recommended Config |
|----------|---------------------------|-------------------|
| **Josh** | Open guild (`groupPolicy: open`) — any Discord user could trigger memory recall surfacing MEMORY.md | Restrict to Josh's direct DM channel ID only |
| **Noah** | Trading channel is allowlisted, but memory-core would fire in any session by default | Restrict to channel `1496556746444112173` only |

**Standard pattern (customized per instance):**
```json
"active-memory": {
  "config": {
    "allowedChatIds": ["<specific-channel-id>"],
    "chatTypes": ["dm"]
  }
}
```
Design decision: MEMORY.md should be built with this scoping in mind from Day 1.

---

### Gemini 3.1 Flash Lite on OpenRouter — Josh Dead Fallback Fix (Today)

Gemini 3.1 Flash Lite confirmed available on OpenRouter. Josh's `openrouter/anthropic/claude-3.5-haiku` is retired — silent errors on every fallback attempt.

**No upgrade, no restart required:**
```json
"fallbacks": [
  "openrouter/google/gemini-3.1-flash-lite-preview",
  "openrouter/google/gemini-2.5-flash",
  "openrouter/anthropic/claude-haiku-4-5"
]
```

Gemini 3.5 Flash also released today with higher prices — not recommended for Heather's heartbeat workload. 3.1 Flash Lite is the right tier.

---

### cron --wait Confirmed — Noah Pre-Market Pipeline Path Clear

`openclaw cron run --wait` confirmed working in 2026.5.x. Enables reliable 6 AM EDGAR scan → 9 AM pre-market summary → 5 PM P&L review without fire-and-forget uncertainty. Full cron config documented in NOAH-44. Blocked on: upgrade + onboarding + HEARTBEAT.md.

---

### claude-opus-4-7 Confirmed Available — Noah Catalog Add (1 min, today)

Previously flagged as upcoming. Now confirmed on Anthropic direct. Add to Noah's model catalog:
```json
"anthropic/claude-opus-4-7": {}
```
Use for deep M&A analysis, FDA research, complex multi-factor catalyst scenarios.

---

### Day 35 Fleet State

| Dimension | Josh / Heather | Noah / Market Catalyst |
|---|---|---|
| OpenClaw version | **2026.3.22** | **2026.4.15** |
| Latest stable | **2026.5.20** | **2026.5.20** |
| Gap (releases) | **~23** | **~16** |
| contextPruning TTL | ❌ None configured | 🔴 **5m — Day 8 CRITICAL** |
| memory-core | ❌ Not eligible (needs upgrade) | ⚠️ Allowlisted, **no entries block** |
| MEMORY.md | ❌ Never created — **Day 35** | ❌ Never created — **Day 35** |
| HEARTBEAT.md | ⚠️ Empty | ⚠️ Empty |
| IDENTITY.md | ✅ Heather (partial) | ❌ **Blank template — Day 35** |
| USER.md | ✅ Josh (populated) | ❌ **Blank template — Day 35** |
| Google account | 🔴 **Never connected — 35 days** | ✅ Connected |
| Retired fallback | 🔴 **claude-3.5-haiku (dead) — fix today** | N/A |
| Discord streaming | ❌ `"off"` | ❌ Not set |
| skills/gog-cli audit | N/A | ⚠️ **Unaudited in financial env** |
| Active Memory allowedChatIds | ⚠️ Not configured (post-upgrade) | ⚠️ Not configured (post-upgrade) |
| Cumulative findings | **~107** | **~122** |
| Resolved findings | **0** | **0** |
| Days since last implementation | **35** | **35** |

---

### Day 35 Morning Zero-Config Backlog

| Action | Target | Effort | Days Documented | Actionable Today? |
|--------|--------|--------|-----------------|------------------|
| Fix dead claude-3.5-haiku fallback | **Josh** | 3 min | NEW | ✅ **Yes — no upgrade** |
| Fix contextPruning 5m → 30m | **Noah** | 2 min | **8 days** | ✅ **Yes — no upgrade** |
| Add memory-core entries block | **Noah** | 3 min | 25+ days | ✅ **Yes — no upgrade** |
| Add opus-4-7 to Noah catalog | **Noah** | 1 min | 2 days | ✅ **Yes — no upgrade** |
| Enable Discord streaming `progress` | **Both** | 1 min each | 8+ days | ✅ Yes |
| Connect Google account | **Josh** | 10 min | 35 days | ✅ Yes |

**~20 minutes total. Zero upgrades. Zero of this done in 35 days.**

---

## Day 34 Morning — New Research (2026-05-21)

### OpenClaw 2026.5.19 Now Latest Stable — Version Gaps Updated

| Instance | Current | New Latest | Gap (releases) | Prior Gap |
|----------|---------|------------|---------------|----------|
| **Josh** | 2026.3.22 | **2026.5.19** | ~22 releases | 21 releases |
| **Noah** | 2026.4.15 | **2026.5.19** | ~15 releases | 14 releases |

**What 2026.5.19 adds:**
- Mac app Settings redesign, browser modal dialog handling, Android Talk Mode
- Gateway startup latency optimizations — faster VPS cold-start; impactful for Noah's pre-market session timing
- Python debugging, node inspector, meme-maker skills bundled (zero install cost)
- 100+ contributor fixes (Discord delivery, streaming, protocol negotiation)

---

### 2026.5.20-beta.1: Next Train Preview

| Feature | Josh Impact | Noah Impact |
|---------|------------|------------|
| Discord voice channel-following | Medium — voice UX | Low |
| **xAI device-code OAuth (Grok 3)** | Low | **HIGH — real-time X/Twitter catalyst signal** |
| **Policy plugin** | Low | **HIGH — per-channel trading guardrails before live keys** |

---

### claude-opus-4-7 — Noah Model Catalog Expansion

```json
// openclaw.json → agents.defaults.models
"anthropic/claude-opus-4-7": {}
```

---

### Day 34 Fleet State — Zero Implementations (Day 34 of 34)

| Dimension | Josh / Heather | Noah / Market Catalyst |
|---|---|---|
| OpenClaw version | **2026.3.22** | **2026.4.15** |
| Latest stable (Day 34) | **2026.5.19** | **2026.5.19** |
| Releases behind | **~22** | **~15** |
| contextPruning TTL | ❌ None | 🔴 **5m — Day 7 critical** |
| memory-core | ❌ Not eligible | ⚠️ **Allowlisted, no entries block** |
| MEMORY.md | ❌ Never created — Day 34 | ❌ Never created — Day 34 |
| HEARTBEAT.md | ⚠️ Empty | ⚠️ Empty |
| IDENTITY.md | ✅ Heather (partial) | ❌ **Blank template — Day 34** |
| USER.md | ✅ Josh (populated) | ❌ **Blank template — Day 34** |
| Google account | 🔴 **Never connected — 34 days** | ✅ Connected |
| Retired fallback | 🔴 **claude-3.5-haiku (dead)** | N/A |
| Cumulative findings | **~103** | **~117** |
| Resolved findings | **0** | **0** |

---

## Day 33 — Key Research Items (2026-05-20)

### contextPruning Gap

| Instance | Status | Fix |
|---|---|---|
| **Josh** | None | Add `{"mode":"cache-ttl","ttl":"35m","keepLastAssistants":3}` |
| **Noah** | **5m TTL — CRITICAL** | Change `"ttl":"5m"` → `"ttl":"30m"` |

### memory-core

| Instance | Status | Fix |
|---|---|---|
| **Josh** | Not in allow list | Add post-upgrade |
| **Noah** | **Allowlisted — NO entries block** | Paste 3-line config block now |

### skills/gog-cli — Noah Security Risk

Noah has `skills/gog-cli` — unused Google OAuth CLI in a financial trading environment. ClawHub Feb 2026 audit: 20% of financial integration skills flagged for credential exfiltration. **Audit or remove.**

### Platform Capabilities (2026.5.18+)

| Capability | Josh | Noah |
|---|---|---|
| Gemini-native TTS | High (existing Google key) | Minimal |
| File transfer plugin | High (iMessage attachments) | Medium (SEC PDFs) |
| Grok OAuth (xAI) | Low | **HIGH — X/Twitter catalyst signal** |
| defineToolPlugin CLI | Medium | **HIGH — Alpaca plugin** |
| Cron `--wait` flag | Medium | **HIGH — scan → alert pipeline** |
| Active Memory `allowedChatIds` | **HIGH — privacy for open guild** | **HIGH — trading channel scope** |

---

## Workspace File Gap Analysis (Persistent)

### Files Identical in Both (SHA match)
- `SOUL.md` — SHA 792306ac — personal assistant template in a trading agent
- `AGENTS.md` — SHA 3faead97 — generic template in both
- `TOOLS.md` — SHA 917e2fa8 — template only in both
- `BOOTSTRAP.md` — should be deleted in both
- `HEARTBEAT.md` — both effectively empty

### Josh Has, Noah Missing
- IDENTITY.md (populated) | USER.md (populated)

### Noah Has, Josh Missing
- `workspace/reports/` | `skills/gog-cli/` | `gogcli/`

### Missing in Both
- **MEMORY.md** — Day 36 | **memory/ directory** — 36 sessions stateless

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

### memory-core entries block — Noah (apply now, no upgrade)
```json
"memory-core": {
  "enabled": true,
  "config": {
    "deduplication": true,
    "temporalDecay": true
  }
}
```

### memory-core + Active Memory — Josh (post-upgrade)
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
        "agents": ["main"], "chatTypes": ["dm"],
        "allowedChatIds": ["JOSH_DIRECT_DM_CHANNEL_ID"],
        "inheritSessionModel": true, "timeout": 12000,
        "setupGraceTimeoutMs": 5000, "maxSummaryChars": 220
      }
    }
  }
}
```

### Active Memory — Noah (after memory-core entries block)
```json
"active-memory": {
  "enabled": true,
  "config": {
    "agents": ["main"], "chatTypes": ["dm", "group"],
    "allowedChatIds": ["1496556746444112173"],
    "inheritSessionModel": true, "timeout": 15000,
    "setupGraceTimeoutMs": 5000, "maxSummaryChars": 220
  }
}
```

### Josh fallback fix (today, no restart)
```json
"fallbacks": [
  "openrouter/google/gemini-3.1-flash-lite-preview",
  "openrouter/google/gemini-2.5-flash",
  "openrouter/anthropic/claude-haiku-4-5"
]
```

### Noah model catalog
```json
"models": {
  "anthropic/claude-opus-4-7": {},
  "anthropic/claude-opus-4-6": {},
  "anthropic/claude-sonnet-4-6": {},
  "xai/grok-3-mini": {}
}
```

### Discord streaming — Both
```json
"streaming": "progress"
```

### eventQueue fix — Noah
```json
"eventQueue": { "listenerTimeout": 600000 }
```

### Cron (Noah post-upgrade)
```json
"cron": {
  "jobs": [
    {"name": "premarket-catalyst-scan", "schedule": "0 6 * * 1-5", "timezone": "America/New_York",
     "command": "Run EDGAR 8-K scan for overnight filings. Screen for material events. Deliver summary.",
     "deliverTo": "1496556746444112173"},
    {"name": "postmarket-pnl", "schedule": "0 17 * * 1-5", "timezone": "America/New_York",
     "command": "Review Alpaca paper positions. Calculate P&L. Update MEMORY.md.",
     "deliverTo": "1496556746444112173"}
  ],
  "retry": {"maxAttempts": 3, "backoffMs": [30000, 60000, 300000], "retryOn": ["rate_limit", "overloaded", "network", "server_error"]}
}
```

### Telemetry — Both
```json
"telemetry": {
  "enabled": true,
  "exporters": [{"type": "prometheus", "port": 9090}]
}
```

### Backup before any upgrade
```bash
cp openclaw.json openclaw.json.bak-pre-5.20
```

---

## Platform Risk Summary (Active)

### Noah contextPruning 5m — CRITICAL (Day 9)
2 min fix. No restart. Do this before anything else.

### Noah Session Bugs (4) — CRITICAL
All fixed in 2026.5.19+. One upgrade delivers all 4 fixes.

### Noah Anthropic Auth Risk — HIGH
Verify: `sk-ant-api03-...` = safe API key. Other format = session token at risk during market hours.

### Config-Wipe During Updates (Both) — HIGH
Backup before upgrade: `cp openclaw.json openclaw.json.bak-pre-5.20`

### skills/gog-cli (Noah) — HIGH
Unaudited financial env skill. Audit or remove.

### Josh Google Account Never Connected — CRITICAL
Day 36. 10-minute fix.

### Josh Dead Fallback Model — MEDIUM (Day 16, fix today)
clause-3.5-haiku retired. Silent failures on fallback. 3-minute fix. No upgrade, no restart.

---

## Priority Matrix — Fleet-Wide (Day 36 Morning)

### CRITICAL — Right Now (No Upgrade Needed)
| Item | Josh | Noah |
|---|---|---|
| Fix dead fallback model | 🔴 **3 min — Day 16** | N/A |
| contextPruning TTL | ⬜ Add 30m — 2 min | 🔴 **5m→30m — Day 9 — 2 min** |
| Connect Google account | 🔴 **36 days — 10 min** | N/A |
| Add memory-core entries block | N/A (upgrade first) | 🔴 **3 lines — 3 min** |
| Add opus-4-7 to catalog | N/A | ⬜ **1 min** |

### HIGH — Today
| Item | Josh | Noah |
|---|---|---|
| Enable Discord streaming `progress` | ⬜ 1 min | ⬜ 1 min |
| Back up openclaw.json | ⬜ Before upgrade | ⬜ **Before any change** |
| Audit skills/gog-cli | N/A | ⬜ **10 min — HIGH security** |

### MEDIUM — This Week
| Item | Josh | Noah |
|---|---|---|
| Upgrade OpenClaw to 2026.5.20 | 3.22 → 5.20 | 4.15 → 5.20 (4 bugs fixed) |
| Create MEMORY.md | ⬜ 5 min | ⬜ 5 min |
| Complete onboarding (IDENTITY/USER/TOOLS/SOUL) | N/A (done) | ⬜ **10 min — Day 36** |

### LOW / OPPORTUNITY — Post-Upgrade
| Item | Josh | Noah |
|---|---|---|
| Gemini-native TTS | ⬜ Post-upgrade | N/A |
| Meeting Capture plugin | ⬜ Post-upgrade to 2026.5.22 | N/A |
| Active Memory + allowedChatIds | ⬜ Post-upgrade (DM only) | ⬜ Post-upgrade (channel 1496556746444112173) |
| Alpaca plugin (defineToolPlugin) | N/A | **⬜ This week post-upgrade** |
| Google Workspace plugin | ⬜ Post-upgrade + OAuth | N/A |
| **xAI/Grok OAuth** (confirmed 2026.5.20) | Low | **⬜ HIGH — ready on upgrade** |
| **Policy plugin** (confirmed 2026.5.20) | Low | ⬜ Before live keys |
| Cron pre/post-market automation | Post-upgrade | **Post-upgrade + onboarding** |
| Chrome DevTools MCP | ⬜ Verify AlphaClaw version | ⬜ Verify AlphaClaw version |
| OTel telemetry | ⬜ Post-upgrade | ⬜ Post-upgrade |

---

## Trend Analysis — Day 36

**Zero implementations across 36 days of documented research.**

**Josh:** ~23 stable releases behind. Dead fallback model (`claude-3.5-haiku`) is a documented 3-minute fix sitting for 16 days. Google account never connected for 36 days. No memory. ~109 cumulative findings, 0 resolved.

**Noah:** contextPruning 5m TTL is Day 9 critical against daily market sessions. 4 session bugs await a single upgrade. IDENTITY.md and USER.md blank templates for 36 days despite source material available since yesterday's scan. memory-core allowlisted and unconfigured — a 3-line paste for 26 days. ~125 cumulative findings, 0 resolved.

**Both:** 36 sessions of complete statelessness. SOUL.md, AGENTS.md, TOOLS.md remain byte-for-byte identical (SHA match) between a personal assistant and a trading agent. The 2026.5.22-beta.1 release overnight adds meeting capture for Josh and cron/policy hardening for Noah — both gated on the same upgrade that has been pending for weeks.

**New this morning (2026-05-24):** 2026.5.22-beta.1 drops meeting capture (Josh) + cron reliability + approval hardening (Noah). Package integrity gates harden ClawHub security for both. Dead fallback at Day 16. contextPruning at Day 9. Zero configs changed in 36 days.

---

*Generated by AlphaClaw Apex Fleet Research Agent — Morning Scan — 2026-05-24 (Day 36)*
