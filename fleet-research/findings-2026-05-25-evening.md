# Fleet Research — Josh (Heather Schwartz) | 2026-05-25 Evening Scan

**Scan type:** Evening (web research + platform release tracking + workspace analysis)
**Date:** 2026-05-25
**Instance:** Josh Meyers — Heather Schwartz (personal assistant)
**Repo:** lylle-rgb/josh_repo
**Previous scan:** 2026-05-24 morning

---

## Platform Status

| Item | Current | Latest | Gap |
|------|---------|--------|-----|
| OpenClaw | 2026.3.22 | **2026.5.20 stable** | **~2 months behind; upgrade recommendation now Day 3** |
| AlphaClaw | Unknown | 0.9.16 | No new release since May 15 |
| Primary model | google/gemini-3-flash-preview | — | Active |
| 2026.5.22-beta.1 | In train | — | Day 2 — do not upgrade |

---

## New Since Last Scan (2026-05-24 Morning)

### FINDING-JOSH-46 | Meeting Capture Plugin Architecture Now Fully Documented
**Severity:** INFO (opportunity — post-upgrade)
**Status:** NEW (community documentation matured since yesterday)

The meeting capture plugin released in 2026.5.18/5.20 is now well-documented in the community. Key architecture details now confirmed:

**How it works:**
- External plugin (outside core npm package) — installed separately after platform upgrade
- SDK source-provider contract: any channel can be a transcript source
- Discord voice is the **first live capture source** built and documented
- Auto-start capture config supported — no manual trigger needed once configured
- Manual transcript imports also supported (paste in a transcript)
- Read-only `openclaw meeting-notes` CLI for querying captured content
- Captured transcripts flow into memory system automatically when memory-core is active

**Why this matters for Heather specifically:**
Josh uses Discord voice. Currently, any conversation in a Discord voice channel leaves zero persistent record for Heather. The meeting capture plugin closes this gap entirely. Once active:
- Josh's voice calls become searchable memory
- Context from voice conversations persists across sessions
- Heather can reference "what Josh discussed on Tuesday's call" for the first time

**Pre-requisites before deploying:**
1. Upgrade to 2026.5.20 stable (currently 3 days overdue)
2. Activate memory-core plugin (not currently in Josh's openclaw.json)
3. Install meeting-notes plugin separately (external package)
4. Configure Discord voice as capture source

**Risk level:** INFO — future capability. All gates on OpenClaw upgrade first.

---

### FINDING-JOSH-47 | OpenRouter Routing Controls Coming in 2026.5.22
**Severity:** LOW (track)
**Status:** NEW (confirmed in 2026.5.22-beta.1 preview)

2026.5.22 stable (ETA ~5-7 days) will include new OpenRouter routing controls. Relevant to Josh because his fallback chain uses OpenRouter:

**Current fallback chain in openclaw.json:**
```
google/gemini-3-flash-preview (primary)
openrouter/google/gemini-2.5-flash (fallback 1)
openrouter/anthropic/claude-3.5-haiku (fallback 2 — DEAD, Day 17)
```

**What the new routing controls likely add:**
- Explicit model preference hints per request type
- Regional routing (latency optimization)
- Cost cap controls
- Finer-grained fallback ordering

**Action:** Before 2026.5.22 lands — clean up the dead fallback (3-minute fix). After upgrade, review routing controls docs and update the chain:
- Remove `openrouter/anthropic/claude-3.5-haiku`
- Replace with `openrouter/anthropic/claude-3.5-sonnet` or another live model
- Consider adding routing cost cap for OpenRouter fallbacks

**Risk level:** LOW — informational track. Dead fallback removal is the immediate fix.

---

### FINDING-JOSH-48 | Memory Absence Milestone — Day 37+ Confirmed
**Severity:** CRITICAL (persistent — escalating cost)
**Status:** PERSISTENT (no change since first documented)

Direct workspace inspection confirms:
- `workspace/memory/` exists but contains only `inbox-state.json` and `onboarding-google.md`
- No `MEMORY.md` — zero long-term memory
- `inbox-state.json` records iMessage monitoring paused (`imessage_monitoring_paused: true`)
- `onboarding-google.md` contains Google service onboarding notes

Every session since March 2026 (37+ days), AGENTS.md instructs Heather to read `MEMORY.md` before doing anything. This file does not exist. Every session begins from scratch — no knowledge of Josh's preferences, businesses, prior decisions, or context.

Noteworthy context in existing memory files that MEMORY.md would capture:
- iMessage bridge was set up and is now paused (reason unknown)
- Google auth is connected via api_key mode for Gmail, Calendar, Drive, Contacts, Tasks
- Josh explicitly asked for no emoji reactions
- Josh named Heather during onboarding
- Bliss (luxury brand) and Oben HiFi (audio) business context

**Risk level:** CRITICAL — every day without MEMORY.md is a day of context loss that cannot be recovered.

---

### FINDING-JOSH-49 | SOUL.md SHA Unchanged — Still Generic Template
**Severity:** MEDIUM (persistent behavioral gap)
**Status:** PERSISTENT — Day 37+

SHA `792306ac60f6c600b8ded97899354557ce900f40` confirmed. This is the exact upstream OpenClaw generic template — identical byte-for-byte to Noah's SOUL.md. No personalization for Josh's use case, preferences, or behavioral rules has ever been written.

Consequences:
- No mention of Josh's explicit no-emoji rule in soul context
- No business voice guidance (Bliss luxury brand, Oben HiFi audio)
- No timezone anchoring (LA / PST/PDT)
- No external action guardrails specific to Josh's integrations (iMessage, Gmail, Calendar)
- The emoji contradiction between SOUL.md (neutral), AGENTS.md ("React Like a Human"), and USER.md ("STRICT: DO NOT SEND EMOJI REACTIONS") remains unresolved

All four fixes were documented in detail in [soul-improvements-2026-05-23-evening.md](soul-improvements-2026-05-23-evening.md). Still not applied.

**Risk level:** MEDIUM — Heather may accidentally send emoji reactions or use the wrong brand voice because the soul provides no Josh-specific rules.

---

### FINDING-JOSH-50 | Dead OpenRouter Fallback — Day 17
**Severity:** MEDIUM
**Status:** PERSISTENT — escalating with upcoming 2026.5.20 upgrade

`openrouter/anthropic/claude-3.5-haiku` in the fallback chain is confirmed dead (model deprecated/removed from OpenRouter). This has been documented for 17 days.

**Why it's escalating:** After the 2026.5.20 upgrade, model failover exports diagnostic OTLP events. A dead fallback model will generate ongoing diagnostic noise in monitoring. Clean it before upgrading.

**Fix (30 seconds, no restart required):**
In `openclaw.json`, update `agents.defaults.model.fallbacks`:
```json
"fallbacks": [
  "openrouter/google/gemini-2.5-flash"
]
```
Remove `openrouter/anthropic/claude-3.5-haiku`. Add a live Claude alternative if desired (e.g., `openrouter/anthropic/claude-3.5-sonnet`).

**Risk level:** LOW — minor config edit; no restart required.

---

## Persistent Findings (Unresolved)

*All findings JOSH-30 through JOSH-45 remain unresolved. See [2026-05-24 morning findings](findings-2026-05-24-morning.md) for full detail.*

| Finding | Severity | Status | Day # |
|---------|----------|--------|-------|
| JOSH-30: MEMORY.md never created | CRITICAL | PERSISTENT | **37+** |
| JOSH-31: HEARTBEAT.md empty | HIGH | PERSISTENT | **37+** |
| JOSH-39: Upgrade to OpenClaw 2026.5.20 | HIGH | **3 DAYS OVERDUE** | 3 |
| JOSH-41: Bootstrap hook files possibly missing | HIGH | UNVERIFIED | 2 |
| JOSH-37: SOUL.md never personalized | MEDIUM | PERSISTENT | **37+** |
| JOSH-32: Bootstrap TOOLS.md false Google auth | MEDIUM | PERSISTENT | **37+** |
| JOSH-33: iMessage paused + malformed JSON | MEDIUM | PERSISTENT | 28+ |
| JOSH-34: Emoji contradiction | LOW | PERSISTENT | 4 |
| JOSH-35: streaming.mode progress available | INFO | OPPORTUNITY | — |
| JOSH-36: Mem0 / Active Memory plugin | INFO | OPPORTUNITY | — |
| JOSH-38: Crash notifications | INFO | OPPORTUNITY | — |
| JOSH-40: 2026.5.21 transcript durability | INFO | PERSISTENT | 2 |
| JOSH-42: ClawHub skills security advisory | MEDIUM | PERSISTENT | 2 |
| JOSH-43: defineToolPlugin custom skills | INFO | OPPORTUNITY | — |
| JOSH-44: 2026.5.22-beta.1 — meeting capture + OpenRouter controls | INFO | TRACK | 1 |
| JOSH-45: Package integrity gates (2026.5.22) | INFO | TRACK | 1 |
| JOSH-46: Meeting capture plugin architecture confirmed | INFO | NEW | 0 |
| JOSH-47: OpenRouter routing controls in 2026.5.22 | LOW | NEW | 0 |
| JOSH-48: MEMORY.md absence Day 37+ confirmed | CRITICAL | ESCALATING | 37+ |
| JOSH-49: SOUL.md SHA unchanged — generic template | MEDIUM | PERSISTENT | 37+ |
| JOSH-50: Dead OpenRouter fallback — Day 17 | MEDIUM | PERSISTENT | 17 |

---

## Platform Research Notes (2026-05-25)

- **OpenClaw latest stable:** 2026.5.20 — no new stable release today
- **OpenClaw 2026.5.22-beta.1:** Day 2 in train. Notable: meeting capture plugin docs maturing, OpenRouter routing controls, package integrity gates, cron reliability, row-level SDK. Do not upgrade to beta.
- **AlphaClaw:** 0.9.16 — no new release since May 15. 10 days without AlphaClaw update.
- **Meeting capture plugin:** Fully documented architecture. Discord voice first live source. External npm package. Memory-core required. Not available until Josh upgrades to 2026.5.20.
- **Community X/Twitter activity:** Luke The Dev (@iamlukethedev) is the primary community voice documenting release summaries. No unexpected issues reported for 2026.5.20 stable. Community adoption positive.
- **Upgrade urgency:** Josh is now 3 days past the first stable recommendation (2026.5.20). Every day on 2026.3.22 means no memory-core, no meeting capture, no Discord improvements, no streaming progress, no transcript durability fixes. The cost of waiting is compounding.
- **Immediate no-upgrade fixes available:**
  1. Create `workspace/MEMORY.md` — zero risk, zero downtime
  2. Add content to `workspace/HEARTBEAT.md` — zero risk
  3. Add Josh's rules to `workspace/SOUL.md` — zero risk
  4. Remove dead fallback from `openclaw.json` — 30 seconds, no restart
  5. Verify bootstrap hook files on VPS — discovery only
