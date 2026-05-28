# Fleet Research — Josh (Heather Schwartz) | 2026-05-28 Morning Scan

**Scan type:** Morning (web research + platform release tracking + new feature discovery)
**Date:** 2026-05-28
**Instance:** Josh Meyers — Heather Schwartz (personal assistant)
**Repo:** lylle-rgb/josh_repo
**Previous scan:** 2026-05-28 evening

---

## Platform Status

| Item | Current | Latest | Gap |
|------|---------|--------|-----|
| OpenClaw | 2026.3.22 | **2026.5.26 stable** | **67 days behind — CRITICAL UPGRADE** |
| AlphaClaw | Unknown | 0.9.16 | Day 14 without new release |
| Primary model | google/gemini-3-flash-preview | — | Active |
| Latest beta | None new since 2026.5.26-beta.2 | — | 2026.5.26 is stable |

No new OpenClaw release since 2026.5.26 shipped May 27. Upgrade target remains **2026.5.26**.

---

## New Since Last Scan (2026-05-28 Evening)

### FINDING-JOSH-66 | Gemini 3.1 Flash Lite — Faster, Cheaper Fallback Candidate
**Severity:** MEDIUM (opportunity — dead fallback replacement)
**Status:** NEW

OpenClaw now integrates with **Gemini 3.1 Flash Lite**, Google's lightest frontier model. This is directly relevant to Josh because:

- Josh's current fallback chain includes the **dead** `openrouter/anthropic/claude-3.5-haiku` (JOSH-50, Day 20+)
- Gemini 3.1 Flash Lite runs at **363 tokens/second** — 45% faster than Gemini 2.5 Flash
- Cost: **1/8 the price of Gemini Pro** — cheaper than any current fallback option
- OpenRouter model ID: `openrouter/google/gemini-3-1-flash-lite-preview`

**Recommended fallback chain update:**
```json
"fallbacks": [
  "openrouter/google/gemini-3-1-flash-lite-preview",
  "openrouter/google/gemini-2.5-flash",
  "openrouter/anthropic/claude-haiku-4-5"
]
```

This replaces the dead `claude-3.5-haiku` fallback (which times out at 30 seconds) with a fast, cheap Gemini option. Zero restart required.

**Risk level:** LOW — fallback models are only used when primary fails. Safe to update in GitHub now.

---

### FINDING-JOSH-67 | defineToolPlugin API — Typed Skill Development for iMessage & Calendar
**Severity:** INFO (opportunity, post-upgrade)
**Status:** NEW

OpenClaw 2026.5.17+ ships the `defineToolPlugin` API — a typed TypeScript system for building custom tool plugins with schema validation, CI-checkable manifests, and context factories. Josh is on 2026.3.22 and misses this, but it's a significant post-upgrade capability.

**What this enables for Heather:**
- **Typed iMessage contact lookup skill** — structured parameters (name, fuzzy match, relationship filter), validated output
- **Calendar approval plugin** — structured event creation with typed confirmation flows
- **Contact card enrichment** — typed tool that takes a contact name, returns enriched profile from known sources

**Requirements:** Node 22, TypeScript ESM output, `typebox` as a runtime dependency (not devDependency). Use `plugins build --check` in CI to catch stale manifests.

**Action:** Note for post-upgrade. When upgrading to 2026.5.26, explore `openclaw plugins init` to scaffold Heather's first typed skill.

**Risk level:** INFO — post-upgrade capability, no action now.

---

### FINDING-JOSH-68 | Memory Hybrid Search Config — Pre-Plan for memory-core Activation
**Severity:** INFO (pre-planning)
**Status:** NEW

OpenClaw's memory system supports **hybrid search** with configurable vector/text weights, MMR diversity, and temporal decay. When Josh upgrades and memory-core activates, this should be configured from day one.

**Recommended configuration for Heather's personal assistant use case:**
```json
"memory-core": {
  "enabled": true,
  "config": {
    "deduplication": true,
    "temporalDecay": true,
    "search": {
      "hybrid": {
        "enabled": true,
        "vectorWeight": 0.6,
        "textWeight": 0.4,
        "candidateMultiplier": 4
      },
      "mmr": {
        "enabled": true,
        "lambda": 0.7
      },
      "temporalDecay": {
        "enabled": true,
        "halfLifeDays": 60
      }
    }
  }
}
```

**Why these settings for Josh:**
- `vectorWeight: 0.6 / textWeight: 0.4` — personal assistant needs exact keyword matches (names, places) as well as semantic similarity
- `halfLifeDays: 60` — personal context is relatively stable (Bliss/Oben, LA timezone, key contacts persist longer than trading patterns)
- Evergreen files (`MEMORY.md`, non-dated memory/ files) are **never decayed** — Josh's core profile persists indefinitely

**Memory providence origins** (automatically tracked when memory-core is active):
- `Observed from source` — things Heather reads in emails/iMessage
- `Confirmed by user` — things Josh explicitly tells Heather
- `Inferred by model` — things Heather deduces
- `Imported from transcript` — from meeting note transcripts

**Risk level:** INFO — configuration to apply when enabling memory-core post-upgrade.

---

### FINDING-JOSH-69 | Transcript Infrastructure Confirmed Core in 2026.5.26 — Better Than Announced
**Severity:** INFO (post-upgrade opportunity)
**Status:** NEW — confirms and expands JOSH-44 from previous scans

The 2026.5.26 release treats transcript capture as **core infrastructure**, not a side feature. Full details from release notes:

- **Core transcript capture** — every session with voice or extended tool use produces a structured transcript
- **Source-provider support** — transcript entries track which tool/source produced each segment
- **Cleaned user-turn persistence** — user messages are stored with proper attribution
- **Media provenance** — attachments in iMessage/WhatsApp conversations are tracked to their source
- **Codex mirrors** — transcript is mirrored for memory ingestion

**Why this matters for Josh:**
The combination of iMessage fix (JOSH-62) + transcript infrastructure means Heather can track the full history of text conversations with Josh, including who sent what and when. Combined with the reaction approval feature (JOSH-65), Heather gets a complete async communication system.

**Action:** After upgrade, test a voice/text session with Heather to verify transcript capture is storing entries in `memory/`.

**Risk level:** INFO — post-upgrade quality improvement.

---

## Persistent Findings (Unresolved — Morning Summary)

| Finding | Severity | Status | Day # |
|---------|----------|--------|---------|
| JOSH-30: MEMORY.md never created | CRITICAL | PERSISTENT | **41+** |
| JOSH-31/54: HEARTBEAT.md empty | HIGH | CONFIRMED EMPTY | **41+** |
| JOSH-33: iMessage paused + malformed JSON | MEDIUM | **FIX IN 2026.5.26** | 32+ |
| JOSH-34: Emoji contradiction (AGENTS vs USER) | MEDIUM | CONFIRMED ACTIVE | 8 |
| JOSH-37/49: SOUL.md never personalized | MEDIUM | PERSISTENT | **41+** |
| JOSH-39→61: Upgrade target 2026.5.26 | HIGH | DAY 67 — CRITICAL | — |
| JOSH-42: ClawHub skills security advisory | MEDIUM | PERSISTENT | 6 |
| JOSH-50: Dead OpenRouter fallback (haiku) | MEDIUM | **NEW REPLACEMENT: JOSH-66** | 21 |
| JOSH-55: TOOLS.md empty | MEDIUM | PERSISTENT | **41+** |
| JOSH-56: AGENTS.md zero customization | MEDIUM | PERSISTENT | 3 |
| JOSH-59: Discord thread idling | LOW | PERSISTENT | 2 |
| JOSH-60: npm shrinkwrap security | INFO | AUTO ON UPGRADE | 2 |
| JOSH-61: Upgrade target → 2026.5.26 | HIGH | ACTIVE | 1 |
| JOSH-62: iMessage fix confirmed in 2026.5.26 | HIGH | BLOCKED ON UPGRADE | 1 |
| JOSH-63: BOOTSTRAP.md never deleted | MEDIUM | PERSISTENT | 1 |
| JOSH-64: Reply delivery speed improvement | INFO | READY POST-UPGRADE | 1 |
| JOSH-65: Reaction approval flows | INFO | READY POST-UPGRADE | 1 |
| JOSH-66: Gemini 3.1 Flash Lite fallback | MEDIUM | **NEW** | 0 |
| JOSH-67: defineToolPlugin opportunity | INFO | **NEW** | 0 |
| JOSH-68: Memory hybrid search pre-plan | INFO | **NEW** | 0 |
| JOSH-69: Transcript core infrastructure | INFO | **NEW** | 0 |

---

## Immediate Action List (No Upgrade Required)

All zero-downtime. All can be applied directly in GitHub.

1. **Replace dead fallback in `openclaw.json`** — swap `openrouter/anthropic/claude-3.5-haiku` for `openrouter/google/gemini-3-1-flash-lite-preview`. One line. (JOSH-50/66)
2. **Create `workspace/MEMORY.md`** — 41 days with no long-term memory. Highest-value fix. (JOSH-30)
3. **Replace `workspace/SOUL.md`** — personalize for Josh: no emoji reactions, LA timezone, Bliss/Oben context, direct style. (JOSH-37)
4. **Add Josh override at top of `workspace/AGENTS.md`** — explicitly disable emoji reactions conflict. (JOSH-34)
5. **Replace `workspace/HEARTBEAT.md`** — activate Gmail + Calendar checks with LA quiet hours. (JOSH-31)
6. **Populate `workspace/TOOLS.md`** — Google auth, Discord guild, iMessage status, model chain. (JOSH-55)
7. **Delete `workspace/BOOTSTRAP.md`** — 67-day-old stale file. (JOSH-63)

**Then (requires VPS access):**
8. **Upgrade to OpenClaw 2026.5.26** — iMessage fix, faster startup, transcript core, reply delivery split.
9. **Re-enable iMessage** after upgrade.
10. **Enable memory-core** with hybrid search config from JOSH-68 above.

---

## Platform Research Notes (2026-05-28 Morning)

- **OpenClaw latest stable:** 2026.5.26 — no new release since yesterday. Upgrade target unchanged.
- **No new beta** as of this morning scan — 2026.5.26-beta.2 graduated to stable, pipeline quiet.
- **Gemini 3.1 Flash Lite:** 363 t/s, 1/8 of Pro cost, available on OpenRouter. Strong dead-fallback replacement.
- **defineToolPlugin:** Available in 2026.5.17+ — Josh gets this on upgrade. Enables typed iMessage/calendar skills.
- **Memory hybrid search:** Full config available — plan for when memory-core is enabled post-upgrade.
- **Transcript capture:** Now core infrastructure, not add-on. Full history + media provenance on upgrade.
- **AlphaClaw:** 0.9.16 — Day 14 without update. Stable.
- **Community sentiment:** No regressions reported on 2026.5.26. Upgrade is clean.

---

*Generated by AlphaClaw Apex Fleet Research Agent — Morning Scan — 2026-05-28 (Day 40)*
