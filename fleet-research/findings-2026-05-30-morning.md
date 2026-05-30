# Fleet Research — Josh (Heather Schwartz) | 2026-05-30 Morning Scan

**Scan type:** Morning (overnight release tracking + platform intelligence)
**Date:** 2026-05-30
**Instance:** Josh Meyers — Heather Schwartz (personal assistant)
**Repo:** lylle-rgb/josh_repo
**Previous scan:** 2026-05-30 evening (JOSH-71 through JOSH-76)

---

## Platform Status

| Item | Current | Latest | Gap |
|------|---------|--------|-----|
| OpenClaw | 2026.3.22 | **2026.5.27 stable** | **70 days behind — CRITICAL** |
| Beta track | — | **2026.5.28-beta.3** | NEW overnight — do not target |
| AlphaClaw | Unknown | 0.9.16 | Stable |
| Primary model | google/gemini-3-flash-preview | — | Active |

---

## New Since Last Scan (2026-05-30 Evening)

### FINDING-JOSH-77 | OpenClaw 2026.5.28-beta.3 Released Overnight
**Severity:** INFO (tracking)
**Status:** NEW — Do Not Act (not stable)

OpenClaw released `2026.5.28-beta.3` overnight. Key additions relevant to Josh's instance:

- **iMessage reactions/approvals channel delivery fix** — Improvements to iMessage channel delivery for reactions and approval flows. Directly relevant post-upgrade for Heather's iMessage bridge restoration (JOSH-73).
- **Startup scan deduplication** — OpenClaw now avoids repeated plugin, channel, session, and filesystem scans at startup. Means faster Gateway starts, faster Heather cold-start on VPS reboot.
- **Visible reply separation** — User-facing sends are now separated from slower follow-up work. Improves Discord response latency when Heather has tool calls in-flight.
- **Transcript-backed meeting summaries** — Uses a single more reliable transcript path for meeting note generation. For Heather, this means more reliable calendar event capture.

Upgrade target remains **2026.5.27 stable**. Beta.3 needs a stabilization window (~7–10 days). Re-evaluate for stable promotion around **2026-06-08**.

**Risk level:** INFO — monitor only.

---

### FINDING-JOSH-78 | memory-core Supports Gemini Embeddings — No OpenAI Keys Needed
**Severity:** HIGH (post-upgrade planning)
**Status:** NEW — changes post-upgrade memory config plan

When memory-core is configured post-upgrade (per JOSH-72), Josh's instance can use **Gemini as the embedding provider** instead of defaulting to OpenAI. Since Josh already has a Google credentials profile configured (`google:default` in `openclaw.json`), memory-core can reuse those credentials — **no additional API keys required**.

**Config to add post-upgrade (in `agents.defaults.memorySearch`):**
```json
"memorySearch": {
  "provider": "google",
  "model": "text-embedding-004"
}
```

This means the full memory stack post-upgrade runs entirely on Josh's existing Google auth:
- Primary model: `google/gemini-3-flash-preview` ✅ (existing)
- Embedding provider: `google/text-embedding-004` ✅ (no new keys)
- Fallback chain: `openrouter/google/gemini-3-5-flash` etc. ✅ (existing)

Without this config, memory-core defaults to OpenAI embeddings, which would require a new OpenAI API key. This one config block eliminates that dependency entirely.

**Risk level:** HIGH — critical planning item for post-upgrade memory configuration. Document in soul-improvements before upgrade.

---

### FINDING-JOSH-79 | 2026.5.28 Package Optimization — Faster Cold Starts
**Severity:** INFO (quality improvement)
**Status:** NEW — available post-upgrade to 2026.5.28+ stable

OpenClaw 2026.5.28 ships meaningful package weight and cold start improvements:
- Cold and warm agent turn latency reduced
- Peak RAM (RSS) usage lower
- Published npm tarball significantly smaller
- Plugin extraction and dependency cleanup keeps core leaner

For Josh's VPS-hosted instance, this translates to faster restart recovery after AlphaClaw watchdog restarts and lower baseline memory pressure — relevant for a personal assistant that should feel instant to a busy LA founder.

**Risk level:** INFO — available after upgrading to 2026.5.28 stable (expected ~2026-06-08).

---

## Codebase State Summary — 2026-05-30 Morning

No workspace files changed since last scan.

| File | Status | Days Stale |
|------|--------|------------|
| SOUL.md | Stock template (SHA: 792306ac — identical to Noah's) | 70 |
| AGENTS.md | Stock template — emoji contradiction with USER.md active | 70 |
| TOOLS.md | Blank example-only template | 70 |
| IDENTITY.md | Partially filled (Heather name set, template artifacts remain) | 70 |
| USER.md | Well-populated — best-maintained file | Current |
| HEARTBEAT.md | Empty (3 comment lines) — zero proactive monitoring | 70 |
| MEMORY.md | Does not exist | — |
| BOOTSTRAP.md | Never deleted — stale onboarding artifact | 70 |
| openclaw.json | 2026.3.22, dead OpenRouter fallback (claude-3.5-haiku) | 70 |

---

## Persistent Findings (All Unresolved)

| Finding | Severity | Status | Day # |
|---------|----------|--------|-------|
| JOSH-30: MEMORY.md never created | CRITICAL | PERSISTENT | **70+** |
| JOSH-31/69: HEARTBEAT.md empty | HIGH | CONFIRMED EMPTY | **70+** |
| JOSH-34/70: Emoji contradiction AGENTS vs USER | MEDIUM | ACTIVE CONFLICT | 70 |
| JOSH-37: SOUL.md never personalized | MEDIUM | PERSISTENT | **70+** |
| JOSH-39/66: Upgrade to 2026.5.27 | HIGH | PERSISTENT | **70 days** |
| JOSH-50: Dead OpenRouter fallback | MEDIUM | PERSISTENT | — |
| JOSH-55: TOOLS.md empty | MEDIUM | PERSISTENT | **70+** |
| JOSH-63: BOOTSTRAP.md never deleted | MEDIUM | PERSISTENT | 70 |
| JOSH-71: Beta 2026.5.28-beta.1/.2 detected | INFO | Superseded by beta.3 | 1 |
| JOSH-72: Active Memory Plugin post-upgrade | HIGH | PERSISTENT | 1 |
| JOSH-73: iMessage paused confirmed | MEDIUM | PERSISTENT (fix on upgrade) | 1 |
| JOSH-74: Google API key mode clarification | INFO | PERSISTENT | 1 |
| JOSH-75: 70 days email activity, zero memory | CRITICAL | ESCALATING | 1 |
| JOSH-76: SEC AI monitoring — heartbeat urgency | INFO | Persistent context | 1 |
| JOSH-77: 2026.5.28-beta.3 — iMessage + cold start | INFO | **NEW** | 0 |
| JOSH-78: memory-core Gemini embeddings | HIGH | **NEW** | 0 |
| JOSH-79: Package optimization — faster cold starts | INFO | **NEW** | 0 |

---

## Immediate Action List (Unchanged — Priority Order)

**Zero-downtime — GitHub file edits only:**

1. **[CRITICAL] Create `workspace/MEMORY.md`** — Resolves JOSH-30/75. Template in soul-improvements docs.
2. **[HIGH] Populate `workspace/HEARTBEAT.md`** — Activate email + calendar monitoring. Quiet hours 23:00–08:00 PT. Resolves JOSH-31/69/76.
3. **[MEDIUM] Fix `workspace/AGENTS.md` emoji contradiction** — Add Josh-specific override at top of file disabling emoji reactions. Resolves JOSH-34/70.
4. **[MEDIUM] Personalize `workspace/SOUL.md`** — Add Heather-specific content for luxury brand/LA founder context.
5. **[MEDIUM] Populate `workspace/TOOLS.md`** — Document Gmail API key mode, Discord guild, iMessage paused status.
6. **[MEDIUM] Fix dead OpenRouter fallback in `openclaw.json`** — Remove `openrouter/anthropic/claude-3.5-haiku`, add Gemini 3.5 Flash chain.
7. **[MEDIUM] Delete `workspace/BOOTSTRAP.md`** — 70-day stale artifact.

**Requires VPS access:**

8. **[HIGH] Upgrade OpenClaw 2026.3.22 → 2026.5.27** — 70 days behind. Unlocks: iMessage fix, memory-core, Active Memory Plugin, security group prompt isolation, Discord improvements.
9. **[HIGH — post-upgrade] Configure memory-core with Gemini embeddings** (JOSH-78 — no new API keys needed).
10. **[HIGH — post-upgrade] Apply Active Memory Plugin config** (JOSH-72).
11. **[HIGH — post-upgrade] Confirm iMessage bridge resumes** — verify `imessage_monitoring_paused` clears.

---

## Platform Research Notes (Morning — 2026-05-30)

- **Latest stable:** 2026.5.27 — Josh is 70 days behind
- **Latest beta:** 2026.5.28-beta.3 (released overnight)
  - iMessage reactions/approvals channel delivery fix (post-upgrade relevance)
  - Startup scan deduplication (faster cold starts)
  - Reply separation (better Discord latency)
  - Transcript-backed meeting summaries
- **Gemini embeddings confirmed for memory-core** — Josh can run full memory stack on existing Google credentials post-upgrade (no OpenAI key needed)
- **AlphaClaw:** 0.9.16 stable, no new release overnight
- **Community:** No new AlphaClaw tips overnight. OpenClaw beta.3 is under normal stabilization watch.
