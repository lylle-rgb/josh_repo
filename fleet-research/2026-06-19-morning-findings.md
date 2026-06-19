# Fleet Research — Josh (Heather Schwartz) | 2026-06-19 Morning Scan

**Scan type:** Morning delta check — version status, community news, overnight changes
**Date:** 2026-06-19 (morning)
**Instance:** Josh Meyers — Heather Schwartz (personal assistant)
**Repo:** lylle-rgb/josh_repo
**Prior scan:** 2026-06-19 evening — Corrected 2026.6.8 regression; rolled upgrade target back to 2026.6.6

---

## Summary

Lean scan. The June 19 evening scan was comprehensive and made two critical corrections (regression rollback, TOOLS.md + MEMORY.md updated). This morning's scan confirms those corrections hold and surfaces one new development: **2026.6.9 has entered beta**.

No new actionable config changes. No new Josh-specific issues found. Persistent blockers remain unchanged.

---

## Finding 1 — OpenClaw 2026.6.9-beta.1 Released (June 19) — NEW

**Status: INFO — Positive signal, no action yet**

Overnight (June 19), OpenClaw released `v2026.6.9-beta.1` as a pre-release on GitHub. The prior evening scan reported alpha.6 as the latest pre-release; the jump to beta.1 is a meaningful milestone.

**What this means:**
- The alpha → beta transition suggests the regression fixes for 2026.6.8 (Discord image tools #94266, memory-search #94316, cron isolation) are now being stabilized
- Beta.1 key focus areas: Telegram rich delivery, agent recovery reliability, Codex integration strengthening
- OpenClaw's typical beta lane runs 1-3 beta builds before stable promotion (days to ~1 week per historical cadence)
- 2026.6.9-stable may arrive within the next 3-7 days

**Action:** No change to upgrade path. Continue holding at 2026.6.6. Monitor for 2026.6.9-stable.

**When 2026.6.9-stable ships:** Run staged upgrade from 2026.3.22 → 2026.5.27 → 2026.6.2 → 2026.6.5 → 2026.6.6 → 2026.6.9-stable. After reaching 2026.6.9-stable:
- Enable Discord streaming (set to `"progress"`)
- Upgrade fallback 2 to `openrouter/anthropic/claude-haiku-4-5`
- Enable auto-thread titles (available in 2026.6.8+)
- Consider enabling compaction/memoryFlush

**Risk level:** LOW — informational only.

---

## Finding 2 — 2026.6.8 Regression Hold: Confirmed (Morning Recheck)

**Status: HOLD — No change**

- npm `latest` (stable channel): still **2026.6.6**
- 2026.6.8 GitHub release page: marked "Latest" on GitHub but NOT promoted to npm `latest`
- ClawStat.us guidance: "Wait for next release" — holds
- Known regressions still unpatched in 2026.6.8: Discord image tools (#94266), memory-search (#94316), misleading fallback (#94176), cron isolation

The June 19 evening correction stands. Target remains 2026.6.6 until 2026.6.9-stable.

**Risk level:** N/A — confirmation only.

---

## Finding 3 — Persistent Blockers: No Change

| Blocker | Days | Status |
|---------|------|--------|
| Google Workspace OAuth not connected | Day 89 | ⛔ CRITICAL |
| iMessage monitoring paused | Day 53+ | ⛔ HIGH |
| heartbeat-state.json all null | Day 3 | ⚠️ MEDIUM-HIGH |
| OpenClaw on 2026.3.22 (87 days behind) | Day 87 | ⚠️ HIGH (safe path exists) |
| Noah session scope mismatch | Day 7+ | ⚠️ FLEET OPS |

No changes to any of these since evening scan. These require Josh (Google Workspace, heartbeat check) or fleet operator (Noah scope) action.

---

## Finding 4 — Community Research: Nothing New Actionable

Morning web sweep across OpenClaw community channels found no new tips or configurations relevant to Heather's personal assistant use case beyond what's already been documented in prior scans. Key highlights from the broader research (already incorporated into prior findings):

- **Memory best practice (confirmed):** Pre-compaction memory flush is the single most important reliability config — if not in openclaw.json, context loss on compaction is silent. This should be added to Josh's config when upgrading.
- **Heartbeat batching (confirmed):** Multiple periodic checks should be batched into HEARTBEAT.md rather than separate cron jobs. Josh's current HEARTBEAT.md is set up correctly for this pattern.
- **Discord threading:** Auto-thread titles (60s timeout, 4,096-token reasoning budget) will be available after 2026.6.9-stable. Worth enabling for cleaner Discord history.
- **iMessage recovery:** The iMessage bridge self-healing is improved in 2026.6.6+ — once Josh upgrades, paused iMessage monitoring may resume automatically or need a single `openclaw doctor --fix` run.

---

## Platform Status (June 19 Morning)

| Item | Current | Target | Status |
|------|---------|--------|--------|
| OpenClaw | 2026.3.22 | 2026.6.9-stable | ⛔ Waiting — do NOT go to 2026.6.8 |
| 2026.6.9 status | beta.1 | stable | 🟡 Getting closer — monitor |
| npm latest | 2026.6.6 | — | 2026.6.8 NOT on stable channel |
| AlphaClaw | Unknown | 0.9.18 | Check Watchdog tab |
| Primary model | google/gemini-3-flash-preview | — | Active |
| Fallback 1 | openrouter/google/gemini-3.5-flash | — | Current |
| Fallback 2 | openrouter/anthropic/claude-3.5-haiku | claude-haiku-4-5 | Pending post-upgrade |
| Google Workspace | Not connected | — | ⛔ CRITICAL — Day 89 |
| iMessage | Paused | — | ⛔ Day 53+ |
| heartbeat-state.json | All null | — | ⚠️ Day 3 |
| SOUL.md | Personalized | — | ✅ Good |
| MEMORY.md | Updated June 19 (evening) | — | ✅ Current |
| TOOLS.md | Updated June 19 (evening) | — | ✅ Regression warning present |

---

## Priority Action Queue (June 19 Morning)

No changes from evening scan. Carried forward:

| Priority | Action | Method | Effort |
|---------|--------|--------|-------|
| 0 — HOLD | Do NOT upgrade to 2026.6.8 | Passive | — |
| 1 — CRITICAL | Connect Google Workspace OAuth | AlphaClaw UI → General tab | ~30 min |
| 2 — HIGH | Verify heartbeat checks are running | Ask Heather in Discord | ~5 min |
| 3 — HIGH | When 2026.6.9-stable ships: run staged upgrade | VPS: `openclaw update` | ~30 min |
| 4 — HIGH | Add compaction + Dreaming (minScore: 0.8) to openclaw.json | VPS edit (post-upgrade) | ~5 min |
| 5 — MEDIUM | Tighten Discord security (open → allowlist) | VPS: openclaw.json | ~5 min |
| 6 — FUTURE | Upgrade fallback 2 to claude-haiku-4-5 | openclaw.json post-2026.6.9-stable | ~1 min |
| 7 — FLEET OPS | Fix Noah session scope | Fleet config | ~5 min |
