# Fleet Research — Josh (Heather Schwartz) | 2026-06-16 Evening Scan

**Scan type:** Platform delta + critical deadline watch + web research
**Date:** 2026-06-16 (evening)
**Instance:** Josh Meyers — Heather Schwartz (personal assistant)
**Repo:** lylle-rgb/josh_repo
**Prior scan:** 2026-06-15 morning (committed ~08:25 PDT) — zero fixes applied since June 13 identification of gemini deadline

---

## ⛔ T-1 DAY — gemini-2.5-flash DIES TOMORROW (June 17)

**Status:** CRITICAL — Deadline is June 17, 2026 (TOMORROW)
**Days identified:** 4 (first flagged June 13 — still unresolved)

The `openrouter/google/gemini-2.5-flash` fallback model **will stop responding tomorrow** when Google shuts down Gemini 2.5 Flash. This is the last evening scan before the cutoff.

**The fix is one line. Thirty seconds. GitHub file editor.**

File: `openclaw.json`, key `agents.defaults.model.fallbacks[0]`

```
CURRENT:   "openrouter/google/gemini-2.5-flash"
CHANGE TO: "openrouter/google/gemini-3.5-flash"
```

`gemini-3.5-flash` hit GA at Google I/O on May 19. Available on OpenRouter now at $0.10/M input, $0.40/M output — cheaper and faster than the endpoint it replaces. Direct successor.

**After June 17, do not apply this fix** — the dead hop will be silently skipped. Applying it before June 17 prevents even the transient failure window.

---

## NEW — JOSH-59 | OpenClaw 2026.6.6 Is Now Latest Stable

**Severity:** INFO (platform gap update)
**Status:** NEW — Delta from June 15 evening scan

The June 15 evening scan listed **2026.6.5** as the latest stable. As of June 13, **2026.6.6** is the confirmed npm `latest`. The beta has also advanced to **2026.6.7-beta.1**.

**What changed in 2026.6.6:**
- Gateway now recovers from restart failures after a provider refresh (previously, a failed refresh could wedge the gateway until manual restart)
- Plugin convergence repair is now exposed — orphaned plugin state gets self-healed at boot
- Native hook relay lifetimes are now bounded — abandoned connections can no longer linger indefinitely (this was a memory leak vector for long-running agents like Heather)
- Explicit user intent required before opening new chat sessions (UX guard)
- Restored chat queues now drain after session switches rather than silently losing messages
- Docker store package seeding fixed (relevant to AlphaClaw container deployments)

**Impact on Heather:**
- The gateway wedge fix is directly relevant — Heather's restart history suggests this has been a latent issue
- The native hook relay leak fix matters for an always-on agent; long uptime was accumulating dead connections

**Updated upgrade path:** `2026.3.22 → 2026.5.27 → 2026.6.2 → 2026.6.5 → 2026.6.6`

**Risk level:** LOW to stage through; HIGH to remain at 2026.3.22 indefinitely.

---

## NEW — JOSH-60 | Commit Audit — Zero Action Since June 13

**Severity:** INFO (operational awareness)
**Status:** Confirmed by git log

The last commit that was NOT a fleet scan document was June 13. Every commit since June 13 has been a fleet research file — no workspace file has been created, modified, or deleted. The five critical GitHub-only actions (no VPS required) that have been pending since before June 13 are all still outstanding:

- `workspace/MEMORY.md`: does not exist
- `workspace/HEARTBEAT.md`: still an empty comment file
- `workspace/SOUL.md`: still the generic OpenClaw default template
- `workspace/BOOTSTRAP.md`: still exists (should have been deleted at onboarding)
- `openclaw.json` fallback model: still points to dead-tomorrow endpoint

---

## Platform Status

| Item | Current | Latest Stable | Latest Beta | Gap |
|------|---------|--------------|------------|-----|
| OpenClaw | 2026.3.22 | **2026.6.6** | 2026.6.7-beta.1 | **86 days** |
| Primary model | google/gemini-3-flash-preview | gemini-3.5-flash (GA) | — | Preview (OK) |
| Fallback 1 | openrouter/google/gemini-2.5-flash | — | — | ⛔ DEAD TOMORROW |
| Fallback 2 | openrouter/anthropic/claude-3.5-haiku | — | — | OK |
| Google Workspace | Not connected | — | — | ⛔ All proactive features blocked |
| MEMORY.md | Does not exist | — | — | ⛔ 86 days, zero long-term memory |
| HEARTBEAT.md | Empty | — | — | ⛔ 86 days, zero proactive monitoring |

---

## Persistent Findings — All Unresolved

| Finding | Severity | Days Open | What's Blocked |
|---------|----------|-----------|----------------|
| JOSH-50: gemini-2.5-flash dead TOMORROW | **CRITICAL** | 4 | Fallback chain integrity — last chance |
| JOSH-45: No Google account connected | **CRITICAL** | 11 | All email / calendar / contacts / proactive monitoring |
| JOSH-30: MEMORY.md missing | **CRITICAL** | 86 | Long-term memory, session continuity, Dreaming feature |
| JOSH-44: Platform 86 days outdated | HIGH | 86 | All 2026.6.x features including MCP coercion, Parallel search, gateway fix |
| JOSH-31: HEARTBEAT.md empty | HIGH | 86 | All proactive monitoring — Heather has never reached out to Josh |
| JOSH-46: Discord streaming disabled | MEDIUM | 11 | Live streaming responses |
| JOSH-53: No isolated cron sessions | MEDIUM | 3 | Best-practice task isolation |
| JOSH-57: MCP coercion unavailable | MEDIUM | 2 | Anthropic 400 risk on rich tool results |
| JOSH-58: Parallel web search unavailable | LOW | 2 | Faster, broader research |
| JOSH-37: SOUL.md generic template | MEDIUM | 86 | Heather's self-understanding, Josh-specific behavioral rules |
| JOSH-32: TOOLS.md empty template | MEDIUM | 86 | No environment documentation |
| JOSH-34: Emoji reaction contradiction | LOW | 86 | AGENTS.md vs USER.md conflict unresolved |
| JOSH-49: BOOTSTRAP.md stale artifact | LOW | 11 | Wasteful context on every session load |
| JOSH-59: Upgrade target is now 2026.6.6 | INFO | NEW | Gateway wedge + relay leak fixes |

---

## Priority Action Queue

### ⛔ Must Do Before June 17 (Tomorrow — Last Chance):

1. **[CRITICAL] Fix fallback model in `openclaw.json`**
   - Path: `openclaw.json`
   - Change: `"openrouter/google/gemini-2.5-flash"` → `"openrouter/google/gemini-3.5-flash"`
   - Method: GitHub file editor (30 seconds, no VPS needed)

### GitHub-Only (No VPS, No Downtime):

2. **[CRITICAL] Create `workspace/MEMORY.md`**
   - Full stub template in `2026-06-13-evening-soul-improvements.md`
   - 86 days without long-term memory. Heather cannot retain anything between sessions.

3. **[HIGH] Populate `workspace/HEARTBEAT.md`**
   - Full template in `2026-06-13-evening-soul-improvements.md`
   - 86 days of zero proactive monitoring. Heather has never once reached out to Josh unprompted.

4. **[MEDIUM] Personalize `workspace/SOUL.md`**
   - Add Josh executive context section
   - Fix emoji reaction rule at the source
   - Full content in `2026-06-13-evening-soul-improvements.md`

5. **[LOW] Delete `workspace/BOOTSTRAP.md`**
   - Stale onboarding artifact consuming context tokens on every session load
   - One-click delete in GitHub file browser

### Requires VPS / Setup UI:

6. **[CRITICAL] Connect Google account**
   - AlphaClaw Setup UI: `https://5.78.142.81.sslip.io#general`
   - Steps documented in `workspace/memory/onboarding-google.md`
   - This single action unblocks everything Heather was deployed to do

7. **[HIGH] Upgrade OpenClaw to 2026.6.6**
   - Staged path: 2026.3.22 → 2026.5.27 → 2026.6.2 → 2026.6.5 → 2026.6.6
   - Key fixes: gateway restart wedge, native hook relay leak, MCP coercion, Parallel search

8. **[MEDIUM] Enable Discord streaming**
   - `channels.discord.streaming: "on"` in `openclaw.json` post-upgrade

9. **[LOW] Set PARALLEL_API_KEY env var**
   - Via AlphaClaw Envars tab post-upgrade
   - Enables bundled Parallel web search

---

## Noah Repo Access — Still Blind

The fleet scan session is scoped to `lylle-rgb/noah--repo`, which does not exist. Both candidate repos (`Noahrepo2`, `Noah-workspace`) are inaccessible in this session scope. Noah (Market Catalyst Agent) fleet analysis has been impossible for this entire scan series.

**Action needed:** Update fleet scan scope to include the correct Noah repo name before the next run.

---

## Research Notes (2026-06-16 Evening)

- **OpenClaw 2026.6.6** confirmed as npm `latest`. Beta at 2026.6.7-beta.1. No new stable release since June 13 — the platform gap is 86 days (2026.3.22 → 2026.6.6).
- **gemini-2.5-flash:** Shutdown confirmed June 17. $0.10/M → $0.40/M pricing gap on the 3.5-flash replacement is a cost reduction, not an increase. No reason not to make this switch.
- **gemini-3.5-flash GA:** Confirmed on OpenRouter. Outperforms 2.5-flash on reasoning benchmarks including MMMU-Pro (81.2%).
- **ClawHub Skill Workshop:** Stable in 2026.6.1+. Provides proposal review queue for agent-created skills before they touch production. Heather cannot access this until upgraded.
- **Proactive monitoring gap:** Heather has been deployed 86 days. She has never proactively reached out to Josh. Zero emails monitored. Zero calendar alerts. Zero weather checks. HEARTBEAT.md is empty and has been since day 1. This is the most impactful single-file fix available.
