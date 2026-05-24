# Fleet Research — Josh (Heather Schwartz) | 2026-05-24 Morning Scan

**Scan type:** Morning (web research + platform release tracking)
**Date:** 2026-05-24
**Instance:** Josh Meyers — Heather Schwartz (personal assistant)
**Repo:** lylle-rgb/josh_repo
**Previous scan:** 2026-05-23 evening

---

## Platform Status

| Item | Current | Latest | Gap |
|------|---------|--------|-----|
| OpenClaw | 2026.3.22 | **2026.5.20 stable** | **~2 months behind; 2026.5.22-beta.1 now in train** |
| AlphaClaw | Unknown | 0.9.16 | Check deployment |
| Primary model | google/gemini-3-flash-preview | — | Active |

---

## New Since Yesterday

### FINDING-JOSH-44 | OpenClaw 2026.5.22-beta.1 Released — Meeting Capture + OpenRouter Routing Controls
**Severity:** INFO  
**Status:** NEW (released overnight 2026-05-23/24)

OpenClaw 2026.5.22-beta.1 dropped overnight. Do not upgrade (beta). Key features previewing the next stable wave (~7-10 days):

**Meeting Capture (pluginized) — HIGH value for Heather post-upgrade:**
- New external meeting-notes plugin with auto-start capture config
- Manual transcript imports supported
- Discord voice is the first live capture source
- Read-only `openclaw meeting-notes` CLI access

**Why it matters for Heather specifically:** Josh uses Discord voice. The meeting capture plugin can automatically transcribe Josh's voice sessions into Heather's memory system once memory-core is active. This closes the voice conversation gap — currently, any conversation in a Discord voice channel leaves zero persistent record for Heather. *Requires upgrade to 2026.5.20 first, then 2026.5.22 stable (ETA ~7-10 days).*

**OpenRouter Routing Controls — Relevant to Josh's fallback chain:**
- New controls for managing OpenRouter model routing behavior
- Josh currently uses OpenRouter as the primary fallback layer (`openrouter/google/gemini-2.5-flash`, dead `openrouter/anthropic/claude-3.5-haiku`)
- Exact routing controls TBD pending stable release, but this is likely to give finer control over which OpenRouter models are selected and in what order
- Possibly includes: explicit model preference hints, regional routing, cost caps

**Other 2026.5.22 features:**
- Package integrity gates (ClawHub security hardening — addresses FINDING-JOSH-42)
- Faster startup (Hetzner VPS cold-start improvement)
- Stronger approval and policy handling
- Row-level session workflow helpers (Plugin SDK)
- Observability smoke tests
- SecretRef guidance (improved docs for `${DISCORD_BOT_TOKEN}` handling)
- xAI device-code login refinement (low relevance — Josh uses Gemini/OpenRouter)

**Risk level:** INFO — track for stable. No action today.

---

### FINDING-JOSH-45 | Package Integrity Gates in 2026.5.22 — ClawHub Security Hardens
**Severity:** INFO (advisory)
**Status:** NEW (2026.5.22-beta.1 preview)

2026.5.22 adds platform-level package integrity gates for skill installation. This directly hardens the ClawHub malware risk surface flagged in FINDING-JOSH-42 (2,419 skills purged in early 2026, including 1,184 distributing wallet-stealing malware).

**What it does:** Before a ClawHub skill is enabled, the platform verifies its package integrity (checksum/signature). A malicious package that modifies itself post-installation or injects code at runtime would be caught at the gate.

**Current impact for Heather:** Low — Heather has zero ClawHub skills installed. But FINDING-JOSH-43 identified two high-value custom plugins worth building (iMessage bridge health check, Bliss brand context tool). When those are installed post-upgrade, package integrity verification adds a meaningful safety layer.

**Risk level:** INFO — future safety improvement.

---

## Persistent Findings (Unresolved)

*All findings JOSH-30 through JOSH-43 remain unresolved. See [2026-05-23 evening findings](findings-2026-05-23-evening.md) for full detail.*

| Finding | Severity | Status | Day # |
|---------|----------|--------|-------|
| JOSH-30: MEMORY.md never created | CRITICAL | PERSISTENT | **36+** |
| JOSH-31: HEARTBEAT.md empty | HIGH | PERSISTENT | **36+** |
| JOSH-39: Upgrade to OpenClaw 2026.5.20 | HIGH | PENDING | 2 |
| JOSH-41: Bootstrap hook files possibly missing | HIGH | UNVERIFIED | 1 |
| JOSH-37: SOUL.md never personalized | MEDIUM | PERSISTENT | **36+** |
| JOSH-32: Bootstrap TOOLS.md false Google auth | MEDIUM | PERSISTENT | **36+** |
| JOSH-33: iMessage paused + malformed JSON | MEDIUM | PERSISTENT | 27+ |
| JOSH-34: Emoji contradiction | LOW | PERSISTENT | 3 |
| JOSH-35: streaming.mode progress available | INFO | OPPORTUNITY | — |
| JOSH-36: Mem0 / Active Memory plugin | INFO | OPPORTUNITY | — |
| JOSH-38: Crash notifications | INFO | OPPORTUNITY | — |
| JOSH-40: 2026.5.21 transcript durability | INFO | PERSISTENT | 1 |
| JOSH-42: ClawHub skills security advisory | MEDIUM | PERSISTENT | 1 |
| JOSH-43: defineToolPlugin custom skills | INFO | OPPORTUNITY | — |
| JOSH-44: 2026.5.22-beta.1 — meeting capture + OpenRouter controls | INFO | NEW | 0 |
| JOSH-45: Package integrity gates (2026.5.22) | INFO | NEW | 0 |

---

## Platform Research Notes (2026-05-24)

- **OpenClaw latest stable:** 2026.5.20 (unchanged — no new stable release since 2026-05-21)
- **OpenClaw 2026.5.22-beta.1:** Released overnight — meeting capture (pluginized), OpenRouter routing controls, package integrity gates, Discord voice upgrades, cron reliability improvements, xAI device-code login refinement, faster startup, stronger approval/policy handling, row-level session SDK helpers. Not for production.
- **2026.5.21-beta.1:** Also in train (first seen 2026-05-22) — stable wave is approaching
- **AlphaClaw latest:** 0.9.16 — no new release since May 15
- **Meeting Capture plugin:** Discord voice as first source; auto-start + manual import supported. Track for 2026.5.22 stable post-upgrade.
- **OpenRouter routing controls:** Details pending stable. Watch for impact on Josh's fallback chain — likely finer model selection/routing control.
- **Package integrity gates:** Addresses ClawHub malware surface (FINDING-JOSH-42). Bundled in 2026.5.22 stable.
- **SecretRef guidance:** Improved documentation for `${DISCORD_BOT_TOKEN}` pattern. Security hygiene improvement.
- **Dead fallback (JOSH-42):** `openrouter/anthropic/claude-3.5-haiku` still unresolved — **Day 16**. 3-minute fix.
- **Bootstrap hook files (JOSH-41):** Still unverified. VPS check needed: `ls /data/.openclaw/workspace/hooks/bootstrap/`
- **Upgrade urgency:** Josh is now 2 days past the first stable recommendation (2026.5.20, first documented 2026-05-22). With 2026.5.22 beta in train, the upgrade window to 2026.5.20 is optimal now before the next stable lands.
