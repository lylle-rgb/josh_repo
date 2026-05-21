# Fleet Research — Josh (Heather) | 2026-05-21 Morning Scan

**Scan type:** Morning (incremental — new releases + persistent issue review)
**Date:** 2026-05-21
**Instance:** Josh Meyers — Heather Schwartz (personal assistant)
**Repo:** lylle-rgb/josh_repo
**Day:** 34

---

## Platform Status (Updated This Morning)

| Item | Current | Latest Stable | Gap |
|------|---------|--------|-----|
| OpenClaw | 2026.3.22 | **2026.5.19** *(+1 since evening scan)* | **~2 months behind** |
| OpenClaw beta | — | 2026.5.20-beta.1 | Preview — do not deploy |
| AlphaClaw | Unknown | 0.9.16 | Verify deployment |
| Primary model | google/gemini-3-flash-preview | — | — |

> **Note:** Evening scan (2026-05-21) referenced 2026.5.18 as latest stable. 2026.5.19 became stable on 2026-05-20. The upgrade target should be 2026.5.19, not 2026.5.18.

---

## New Findings (Morning Scan)

### FINDING-JOSH-39 | Upgrade Target Bumped: 2026.5.19 Now Latest Stable
**Severity:** INFO
**Status:** NEW

OpenClaw 2026.5.19 became the latest stable release on 2026-05-20. All prior scan references to "upgrade to 2026.5.18" should now read **2026.5.19**.

**What 2026.5.19 adds beyond 2026.5.18 (relevant to Heather):**
- **Mac app Settings redesign** with card-based layouts — easier configuration from AlphaClaw Apex dashboard
- **Browser modal dialog handling** improvements — addresses web automation gaps relevant to iMessage bridge interactions and BlueBubbles macOS dialogs
- **Android Talk Mode** routes through Gateway relay voice sessions — if Josh queries Heather via Android voice, this is a UX improvement
- **Gateway startup latency optimizations** — faster agent cold-start on Hetzner VPS
- **Pi packages updated to 0.75.1** — dependency maintenance, no behavioral change
- **100+ contributor fixes** — cumulative reliability (Discord delivery, streaming, protocol negotiation)

**Exact action:** On VPS — `openclaw upgrade` → confirm `openclaw --version` shows 2026.5.19.

**Risk level:** LOW (incremental on 2026.5.18; same test checklist applies)

---

### FINDING-JOSH-40 | New Bundled Skills in 2026.5.19 (Zero Install Cost)
**Severity:** INFO
**Status:** OPPORTUNITY

2026.5.19 bundles three new skills automatically — no `openclaw skill install` required:

| Skill | Relevance to Heather |
|-------|----------------------|
| **meme-maker** | Generates memes on request — fun personal assistant capability for Josh |
| **Python debugging** | Available if Josh develops custom scripts (iMessage processing, personal data tools) |
| **Node inspector** | Available for any Node-based custom utilities |

**Why it matters:** Zero friction — Heather gets meme generation capability the moment the upgrade lands. Josh can ask Heather to make a meme without any configuration.

**Risk level:** LOW (bundled, no activation needed)

---

### FINDING-JOSH-41 | 2026.5.20-beta.1: Discord Voice Channel-Following (Preview)
**Severity:** INFO
**Status:** OPPORTUNITY (beta — do not deploy)

Released 2026-05-21. Discord voice sessions now support channel-following — the bot stays connected as the user moves between voice channels without requiring a manual reconnect command.

**Why it matters for Josh:** If Josh uses voice commands to Heather in Discord voice channels, this removes reconnect friction. Low priority given the personal assistant use case is primarily text-based.

**Action:** Track for stable 2026.5.20. No action until stable.

**Risk level:** INFO (beta)

---

### FINDING-JOSH-42 | 2026.5.20-beta.1: xAI/Grok OAuth via Device-Code (Preview)
**Severity:** INFO
**Status:** OPPORTUNITY (beta)

2026.5.20-beta.1 adds device-code OAuth for xAI (Grok). No developer portal required — sign in with SuperGrok subscription.

**Relevance for Heather:** Low direct value for personal assistant workflows. Possible use case: X/Twitter monitoring for brand mentions (Bliss, Oben HiFi). Lower priority than Google account connection (FINDING-JOSH-29 — still unresolved after 33+ days).

**Action:** Wait for stable release. Only evaluate after Google account is connected.

**Risk level:** INFO (beta)

---

### FINDING-JOSH-43 | Gateway Startup Optimization — Relevant to Hetzner VPS
**Severity:** INFO
**Status:** NEW (available in 2026.5.19)

Gateway startup latency optimizations were introduced in the 2026.5.19 beta train and are stable in 2026.5.19. For a Hetzner VPS running AlphaClaw, this means faster cold-start when the container restarts or when AlphaClaw performs a managed restart.

**Why it matters:** Josh's instance restarts when AlphaClaw applies updates or when the VPS reboots. Faster startup means shorter gaps in Heather's availability.

**Risk level:** LOW (automatic on upgrade)

---

## Persistent Findings Tracker (Day 34)

All findings from the 2026-05-21 evening scan remain unresolved. The version gap has grown.

| Finding | Severity | Days Unresolved | Notes |
|---------|----------|-----------------|-------|
| JOSH-30: MEMORY.md never created | CRITICAL | **34+** | Agent is stateless across all sessions |
| JOSH-31: HEARTBEAT.md empty | HIGH | **34+** | Zero proactive monitoring |
| JOSH-29: Platform outdated | HIGH | **34+** | Target now **2026.5.19** |
| JOSH-37: SOUL.md never personalized | MEDIUM | **34+** | Generic template — Josh's preferences absent |
| JOSH-32: Bootstrap TOOLS.md stale | MEDIUM | **34+** | Poisoned Google auth state at startup |
| JOSH-33: iMessage monitoring paused | MEDIUM | **~26** | ~26 days of silent iMessage |
| JOSH-34: Emoji contradiction | LOW | **1** | USER.md vs AGENTS.md conflict |
| JOSH-35: Streaming progress available | INFO | — | 1-line config change |
| JOSH-36: Mem0 persistent memory | INFO | — | Post-upgrade opportunity |
| JOSH-38: Crash notifications | INFO | — | AlphaClaw 0.9.x feature |

---

## Morning Priority Queue

### Zero-Config (No Restart — Do Now)

**1. Fix retired model fallback (3 minutes)**
In `openclaw.json` → `agents.defaults.model.fallbacks`:
```json
"fallbacks": [
  "openrouter/google/gemini-3.1-flash-lite-preview",
  "openrouter/google/gemini-2.5-flash",
  "openrouter/anthropic/claude-haiku-4-5"
]
```
`claude-3.5-haiku` is retired and will fail on any fallback trigger.

**2. Enable Discord streaming progress (1 minute)**
In `openclaw.json` → `channels.discord`:
```json
"streaming": "progress"
```
Change from `"off"`. Heather will show incremental progress during long tasks instead of appearing frozen.

**3. Back up config before upgrade**
```bash
cp openclaw.json openclaw.json.bak-pre-5.19
```

### Before End of Week
- Connect Google account (Day 33+ unresolved — Gmail, Calendar, Contacts all blocked)
- Upgrade to 2026.5.19
- Create workspace/MEMORY.md (see soul-improvements.md)
- Configure HEARTBEAT.md with proactive calendar/email checks

---

## Research Notes

- **2026.5.19 stable:** Released 2026-05-20. Upgrade target for this instance.
- **2026.5.20-beta.1:** Released 2026-05-21. Discord voice channel-following, xAI device-code OAuth, policy plugin. Track for stable.
- **Pi packages 0.75.1:** Bundled in 2026.5.19 — no separate action needed.
- **defineToolPlugin:** `openclaw plugins init/build/validate` — Google Workspace plugin development path is actionable post-upgrade + Google OAuth.
- **Memory system:** Active Memory plugin requires 2026.4.10+ — Josh is eligible post-upgrade. Gemini embeddings for semantic search come free with existing Google auth.
- **Mem0.ai:** External memory plugin available. Survives context compaction and restarts. High value for long-term personal assistant context. Install: `openclaw skill install mem0`.

---

*Generated by AlphaClaw Apex Fleet Research Agent — Morning Scan — 2026-05-21 (Day 34)*
