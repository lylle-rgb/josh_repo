# Fleet Research — Josh (Heather Schwartz) | 2026-06-15 Evening Scan

**Scan type:** Platform delta + persistent gap escalation + web research
**Date:** 2026-06-15 (evening)
**Instance:** Josh Meyers — Heather Schwartz (personal assistant)
**Repo:** lylle-rgb/josh_repo
**Prior scan:** 2026-06-14 morning — zero fixes applied in 10 consecutive days

---

## ⛔ JOSH-50 | FINAL ESCALATION — gemini-2.5-flash Deprecation IN 2 DAYS

**Status:** CRITICAL — Deadline June 17, 2026 (2 days from now)
**Days open:** 3 — Identified June 13, still unresolved.
**Action taken since June 14 morning:** NONE.

The `openrouter/google/gemini-2.5-flash` fallback model **will stop responding on June 17** when Google shuts down Gemini 2.5 Flash and Pro. This is the last scan before the deadline. After June 17, Heather's fallback chain degrades to two active hops instead of three, with an orphaned dead endpoint in the middle.

**The fix is 30 seconds. One line. Zero risk:**

File: `openclaw.json`, key `agents.defaults.model.fallbacks[0]`

```
CURRENT:  "openrouter/google/gemini-2.5-flash"
CHANGE TO: "openrouter/google/gemini-3.5-flash"
```

`gemini-3.5-flash` hit GA on May 19 at Google I/O. Available on OpenRouter now. Direct successor, same speed tier, improved reasoning, lower cost. This is not an upgrade decision — it's a dead-endpoint swap.

**After June 17, do not apply the fix** — the broken hop will be silently skipped. Apply it before June 17 to avoid even the transient failure window.

---

## Platform Status

| Item | Current | Latest Stable | Latest Beta | Gap |
|------|---------|--------------|------------|-----|
| OpenClaw | 2026.3.22 | 2026.6.5 | 2026.6.5-beta.6 (Jun 9) | **84 days** |
| Primary model | google/gemini-3-flash-preview | gemini-3.5-flash (GA) | — | Preview |
| Fallback 1 | openrouter/google/gemini-2.5-flash | — | — | ⛔ DEAD IN 2 DAYS |
| Fallback 2 | openrouter/anthropic/claude-3.5-haiku | — | — | OK |

---

## Noah Repo Access Failure

**Scope issue discovered:** The fleet scan is configured for `lylle-rgb/noah--repo`, which does not exist (GitHub 404). Search found two candidate repos: `lylle-rgb/Noahrepo2` (updated 2026-03-08) and `lylle-rgb/Noah-workspace` (updated 2026-03-07). Neither is in the permitted scope for this session.

**Impact:** No Noah analysis was possible this scan. Fleet scan for the Market Catalyst Agent (Noah) has been completely blind for this session.

**Action needed:** Update the fleet scan configuration to point to the correct Noah repo name (`Noahrepo2` or `Noah-workspace`). Once corrected, Noah analysis can resume.

---

## NEW — JOSH-57 | OpenClaw 2026.6.5 MCP Coercion — Prevents Anthropic 400 Errors

**Severity:** MEDIUM (post-upgrade benefit)
**Status:** NEW — Feature available in 2026.6.5 stable

OpenClaw 2026.6.5 added MCP tool result coercion at the materialize boundary. Before this fix, if an MCP tool returned a `resource_link`, `resource`, `audio`, or malformed image block, the session could receive an Anthropic 400 error and corrupt the session history. After this fix:
- Valid images pass through unchanged
- Richer content (resource, audio) becomes safe text
- Unsupported blocks don't masquerade as malformed images
- Anthropic 400s from tool result shape mismatches are eliminated

**Why it matters for Heather:** Heather's claude-3.5-haiku fallback is via Anthropic. Any MCP skill that returns rich content (Google Calendar events, iMessage attachments, webhook payloads) can trigger these 400s on the current unpatched version. Post-upgrade, these fail silently rather than corrupting the session.

**Risk level:** NONE (automatic on upgrade)
**Dependency:** Requires OpenClaw upgrade to 2026.6.5

---

## NEW — JOSH-58 | Parallel Web Search Now Bundled in OpenClaw

**Severity:** LOW (post-upgrade capability)
**Status:** NEW — Feature added in 2026.6.5-beta.3, present in stable

OpenClaw 2026.6.5 bundles Parallel as a native `web_search` provider. Parallel is a parallel search engine that fans out queries across multiple sources simultaneously. Setup:
- Set `PARALLEL_API_KEY` in the VPS environment (via AlphaClaw Envars tab)
- OpenClaw picks it up automatically; no `openclaw.json` change required

**Why it matters for Heather:** Research tasks — when Josh asks Heather to look something up about Bliss competitors, Oben HiFi market landscape, or general research — complete faster and with more thorough coverage. The bundled integration handles caching and session IDs automatically.

**Risk level:** NEGLIGIBLE (additive; key just needs to be set)
**Dependency:** Requires OpenClaw upgrade + PARALLEL_API_KEY env var

---

## Persistent Findings — 10 Days Without Any Resolution

This is the longest no-action streak in fleet history. The following table includes every open finding and days since it was first identified.

| Finding | Severity | Days Open | What's Blocked |
|---------|----------|-----------|----------------|
| JOSH-50: gemini-2.5-flash dead in 2 days | **CRITICAL** | 3 | Fallback chain integrity |
| JOSH-45: No Google account connected | **CRITICAL** | 10 | All email / calendar / contacts |
| JOSH-30: MEMORY.md missing | **CRITICAL** | 85 | Long-term memory; Dreaming |
| JOSH-44: Platform 85 days outdated | HIGH | 85 | All 2026.6.x features |
| JOSH-31: HEARTBEAT.md empty | HIGH | 85 | All proactive monitoring |
| JOSH-46: Discord streaming disabled | MEDIUM | 10 | Streaming responses |
| JOSH-53: No isolated cron sessions | MEDIUM | 2 | Best-practice automation |
| JOSH-54: No skill audit policy | MEDIUM | 2 | Security hygiene |
| JOSH-57: MCP coercion unavailable | MEDIUM | NEW | Anthropic 400 risk |
| JOSH-37: SOUL.md generic template | MEDIUM | 85 | Heather's self-understanding |
| JOSH-32: TOOLS.md empty template | MEDIUM | 85 | Tool documentation |
| JOSH-34: Emoji reaction contradiction | LOW | 85 | USER.md vs AGENTS.md conflict |
| JOSH-49: BOOTSTRAP.md stale | LOW | 10 | Wasteful context load every session |

---

## Priority Action Queue (Unchanged — No Fixes Applied)

### ⛔ Do Before June 17 (2 days):

1. **[CRITICAL] Fix fallback model** — `openclaw.json` line change. 30 seconds. GitHub file editor. See JOSH-50 above.

### GitHub-Only (No VPS, No Downtime):

2. **[CRITICAL] Create `workspace/MEMORY.md`** — Heather has no long-term memory after 85 days. Full template in June 13 evening soul-improvements.

3. **[MEDIUM] Populate `workspace/HEARTBEAT.md`** — Zero proactive monitoring for 85 days. Full config in June 13 evening soul-improvements.

4. **[MEDIUM] Personalize `workspace/SOUL.md`** — Add Josh executive context section. Content in June 13 evening soul-improvements.

5. **[LOW] Delete `workspace/BOOTSTRAP.md`** — Wasteful context load every session. One-click delete in GitHub UI.

6. **[LOW] Fix emoji contradiction** — Remove `## React Like a Human!` from AGENTS.md or add override notice; USER.md says NO emoji reactions.

### Requires VPS / Setup UI:

7. **[CRITICAL] Connect Google account** — Setup UI at `https://5.78.142.81.sslip.io#general`. Steps in `workspace/memory/onboarding-google.md`. Unblocks everything Heather was deployed to do.

8. **[HIGH] Upgrade OpenClaw to 2026.6.5** — Staged: 2026.3.22 → 2026.5.27 → 2026.6.2 → 2026.6.5.

9. **[MEDIUM] Enable Discord streaming** — `channels.discord.streaming: "on"` post-upgrade.

10. **[LOW] Set PARALLEL_API_KEY** — Enables bundled Parallel web search post-upgrade.

---

## Research Notes (2026-06-15 Evening)

**OpenClaw:** No new stable release since June 14 scan. Stable remains 2026.6.5 (June 3). Beta train at 2026.6.5-beta.6 (June 9) — no new beta tagged since June 9.

**gemini-2.5-flash deprecation:** Confirmed June 17 by Google. No change. Two days remaining.

**Gemini 3.5 Flash:** GA on OpenRouter. Pricing: $0.10/M input, $0.40/M output — significantly cheaper than the 2.5-flash it replaces. Performance improvements on reasoning benchmarks.

**Noah fleet gap:** `noah--repo` scope mismatch blocks all Noah analysis. This has been the case for this entire session. The correct repos found are `Noahrepo2` and `Noah-workspace`, both private and last updated March 2026.

**ClawHub Skill Workshop (2026.6.1+):** Now generally available. Provides a review queue for agent-created skills before they touch production workflows. Relevant post-upgrade.

**Proactive monitoring gap summary:** Heather has been deployed for 85 days. She has responded to Josh 0 times proactively. All interactions require Josh to initiate. HEARTBEAT.md is empty — she is not checking email, calendar, or notifications on any schedule. This is the most impactful gap to close and requires only a GitHub file edit.
