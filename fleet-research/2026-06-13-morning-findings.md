# Fleet Research — Josh (Heather Schwartz) | 2026-06-13 Morning Scan

**Scan type:** Platform delta + persistent gap escalation + web research  
**Date:** 2026-06-13  
**Instance:** Josh Meyers — Heather Schwartz (personal assistant)  
**Repo:** lylle-rgb/josh_repo  
**Prior scan:** 2026-06-05 evening — zero findings resolved in 8 days

---

## Platform Status

| Item | Current | Latest Stable | Gap |
|------|---------|--------------|-----|
| OpenClaw | 2026.3.22 | **2026.6.5** | **83 days** |
| AlphaClaw | Unknown | 0.9.16 | Check deployment |
| Primary model | google/gemini-3-flash-preview | gemini-3.5-flash (GA) | Preview status |
| Fallback 1 | openrouter/google/gemini-2.5-flash | — | **⚠️ DEPRECATES JUNE 17** |
| Fallback 2 | openrouter/anthropic/claude-3.5-haiku | — | OK |

---

## ⚠️ URGENT — JOSH-50 | Gemini 2.5 Flash Fallback Deprecates in 4 Days
**Severity:** CRITICAL  
**Status:** NEW — Time-sensitive (deadline: June 17, 2026)

Josh's first fallback model `openrouter/google/gemini-2.5-flash` is scheduled for deprecation on **June 17, 2026** — four days from today. Google confirmed `gemini-2.5-flash` and `gemini-2.5-pro` will both be shut down on that date.

**Impact:**
- If Heather's primary (`gemini-3-flash-preview`) hits a rate limit or error, the first fallback will be a dead endpoint after June 17
- The second fallback (`openrouter/anthropic/claude-3.5-haiku`) will catch it, but it should not be the only safety net
- This is a broken config with a hard deadline

**Exact changes to apply (GitHub-only, zero downtime):**  
In `openclaw.json`, under `agents.defaults.model.fallbacks`, replace the first entry:

```json
// CURRENT:
"fallbacks": [
  "openrouter/google/gemini-2.5-flash",
  "openrouter/anthropic/claude-3.5-haiku"
]

// CHANGE TO:
"fallbacks": [
  "openrouter/google/gemini-3.5-flash",
  "openrouter/anthropic/claude-3.5-haiku"
]
```

`gemini-3.5-flash` reached GA on May 19, 2026 at Google I/O and is the direct stable successor to `gemini-2.5-flash`. It is available on OpenRouter today.

**Risk level:** LOW (model swap in fallback array — primary model unchanged)  
**Deadline:** June 17, 2026

---

## JOSH-51 | Primary Model on Preview — Stable GA Available
**Severity:** MEDIUM  
**Status:** NEW — Informational (no shutdown date announced)

Josh's primary model is `google/gemini-3-flash-preview`. The stable GA replacement is `gemini-3.5-flash`, released May 19, 2026.

**Why the preview isn't urgent yet:**  
Google has not announced a shutdown date for `gemini-3-flash-preview` because it still supports Computer Use (a capability not yet in `gemini-3.5-flash`). If Heather doesn't use Computer Use, transitioning now is lower-risk than staying on an unversioned preview.

**Recommendation:**  
Do not change the primary model today. Monitor for a Computer Use-capable stable model or a Google-announced shutdown date. The June 17 fallback fix (JOSH-50) is the only model change required this week.

**Risk level:** LOW (no action required — informational)

---

## JOSH-52 | OpenClaw 2026.6.5 Now Latest Stable — New Features
**Severity:** HIGH  
**Status:** NEW — Platform delta (extends prior JOSH-44)

OpenClaw has shipped through 2026.6.5 since the June 5 scan noted 2026.6.2 stable. Josh is now 83 days behind.

**What's new in 2026.6.3–2026.6.5 beyond what was in JOSH-44:**
- **Bundled Parallel web search:** `web_search` tool can now use Parallel as a provider (alongside Brave) with `PARALLEL_API_KEY` discovery. For Heather, this means more reliable real-time search fallback — if Brave is slow or returns poor results, Parallel kicks in automatically.
- **Stronger chat safety:** Safer handling of edge cases in Discord channel message composition — directly relevant to Heather's primary interface.
- **Durable auth and storage:** Auth state is more resilient across container restarts. If Heather's VPS restarts (or AlphaClaw's container cycles), she reconnects to Discord faster.
- **Safer upgrade and service paths:** Smoother in-place upgrades for the 2026.3.22 → 2026.6.5 jump.

**Exact changes to apply:**  
Requires VPS — upgrade from 2026.3.22 to 2026.6.5. Continue recommendation to stage through 2026.5.27 then 2026.6.2 then 2026.6.5.

**Risk level:** MEDIUM (large version jump; staging recommended)

---

## Persistent Critical Findings (8 Days Without Resolution)

| Finding | Severity | Days Open | Status |
|---------|----------|-----------|--------|
| JOSH-50: gemini-2.5-flash deprecates June 17 | **CRITICAL** | NEW | Fix by June 17 |
| JOSH-45: No Google account connected | **CRITICAL** | 8 | Blocks all email/calendar/contacts |
| JOSH-30: MEMORY.md never created | **CRITICAL** | 83 | Heather has no long-term memory |
| JOSH-44/52: Platform 83 days outdated | HIGH | 83 | Requires VPS |
| JOSH-31: HEARTBEAT.md empty | HIGH | 83 | No proactive monitoring |
| JOSH-46: Discord streaming disabled | MEDIUM | 8 | `streaming: "off"` = silence |
| JOSH-37: SOUL.md generic template | MEDIUM | 83 | Never personalized for Heather |
| JOSH-32: TOOLS.md empty template | MEDIUM | 83 | No setup documentation |
| JOSH-34: Emoji contradiction | LOW | 83 | AGENTS.md vs USER.md conflict |
| JOSH-49: BOOTSTRAP.md stale | LOW | 8 | Dead weight in every session |

---

## Priority Action Queue

### Immediate — This Week (Hard Deadline):

1. **[CRITICAL / DEADLINE June 17] Fix fallback model** — `openrouter/google/gemini-2.5-flash` → `openrouter/google/gemini-3.5-flash` in `openclaw.json`. GitHub-only, 30 seconds.

### GitHub-Only (Zero Downtime, No VPS Access):

2. **[CRITICAL] Create `workspace/MEMORY.md`** — Heather cannot build long-term memory without this file. 83 days open.
3. **[MEDIUM] Personalize `workspace/SOUL.md`** — See prior soul-improvements.md for exact additions.
4. **[MEDIUM] Fill in `workspace/TOOLS.md`** — Document Discord as primary channel, iMessage paused, no Google yet.
5. **[LOW] Delete `workspace/BOOTSTRAP.md`** — Stale onboarding artifact.
6. **[LOW] Fix emoji contradiction** — Remove emoji reaction encouragement from AGENTS.md.

### Requires VPS / Setup UI:

7. **[CRITICAL] Connect Google account** — Setup UI → Google Workspace → authorize Gmail + Calendar + Contacts. Unblocks the entire personal assistant use case.
8. **[HIGH] Upgrade to 2026.6.5** — Unlocks streaming, Parallel search, interrupted recovery, iOS push, Skill Workshop.
9. **[MEDIUM] Enable Discord streaming** — `channels.discord.streaming: "on"` after upgrade.

---

## Platform Research Notes (2026-06-13)

- **OpenClaw 2026.6.5** is the current stable release as of June 2026. Beta builds (2026.6.5-beta.1, -beta.2) preceded it; the final is tagged and shipping.
- **Gemini 2.5 deprecation (June 17)** is confirmed by Google. The `gemini-2.5-flash` and `gemini-2.5-pro` model IDs will stop responding on that date. OpenRouter routes to the underlying Google API — when Google kills the model, OpenRouter's route dies too.
- **Gemini 3.5 Flash** is the recommended stable successor on OpenRouter (`openrouter/google/gemini-3.5-flash`). Comparable speed and cost to 2.5 Flash, with improved reasoning on multi-step personal assistant tasks.
- **No GitHub-only fixes have been applied in 8 days.** The MEMORY.md gap (JOSH-30) and the BOOTSTRAP.md stale artifact (JOSH-49) require nothing more than a file create/delete via GitHub. These should take under 5 minutes combined.
- **Heather's use case** (iMessage monitoring, email triage, calendar, contacts) remains completely blocked until the Google account is connected. Nothing in the platform delta changes that dependency.
