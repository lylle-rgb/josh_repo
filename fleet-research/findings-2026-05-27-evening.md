# Fleet Research — Josh (Heather Schwartz) | 2026-05-27 Evening Scan

**Scan type:** Evening (web research + platform release tracking + deep codebase analysis)
**Date:** 2026-05-27
**Instance:** Josh Meyers — Heather Schwartz (personal assistant)
**Repo:** lylle-rgb/josh_repo
**Previous scan:** 2026-05-26 evening

---

## Platform Status

| Item | Current | Latest | Gap |
|------|---------|--------|-----|
| OpenClaw | 2026.3.22 | **2026.5.22 stable** | **~2 months behind — CRITICAL UPGRADE** |
| AlphaClaw | Unknown | 0.9.16 | No new release since May 15 — 12 days |
| Primary model | google/gemini-3-flash-preview | — | Active |
| 2026.5.25-alpha.1 | In train | — | Alpha — do NOT upgrade |

---

## 🔴 UPGRADE TARGET CHANGE: 2026.5.22 Now Stable

The upgrade target has changed. OpenClaw **2026.5.22 shipped May 23-24, 2026** and is confirmed stable on npm. Previous scans recommended upgrading to 2026.5.20. The new recommendation is to upgrade directly to **2026.5.22** — skipping 2026.5.20 entirely.

Josh's instance has been on 2026.3.22 for 65+ days. The current gap includes two full stable releases (2026.5.20 and 2026.5.22).

---

## New Since Last Scan (2026-05-26 Evening)

### FINDING-JOSH-57 | OpenClaw 2026.5.22 Stable — Confirmed Live, Upgrade Target Revised
**Severity:** HIGH (upgrade urgency escalates)
**Status:** NEW — upgrade target is now 2026.5.22, not 2026.5.20

OpenClaw v2026.5.22 shipped May 23-24, 2026 and is now the latest stable release. The upgrade path recommendation changes:

**Old recommendation:** Upgrade to `2026.5.20` immediately.
**New recommendation:** Upgrade directly to `2026.5.22` — it is stable and includes everything from 2026.5.20 plus significant new value.

**What 2026.5.22 adds that Josh should care about:**
- **4,100x faster model listing** — pre-warm provider auth at startup; `/models` drops from 20s to ~5ms. Heather's Gemini model calls will start faster every session.
- **Reused channel catalogs, plugin metadata snapshots, SDK alias maps at startup** — gateway hot path is dramatically leaner.
- **Meeting notes plugin** — Discord voice is now a first-class source. Heather can capture voice conversations in Josh's Discord server and turn them into searchable memory.
- **Cleaner onboarding and better history access** — improved session picker with search + Load More pagination.
- **Safer agent defaults** — tighter external action confirmation flows.
- **Gemini fractional seconds fix** (from 2026.5.25-alpha.1, not in 2026.5.22 yet) — noted for future: Gemini web search will stop failing on freshness-bound queries.

**Risk level:** HIGH — upgrading is zero-risk on the platform side (community confirms clean upgrade from any 2026.3.x or 2026.4.x). Running 65+ days behind means Heather is missing two full release cycles of stability and performance improvements.

---

### FINDING-JOSH-58 | Gemini Fractional Seconds Fix in Alpha — Heather's Web Search May Be Silently Failing
**Severity:** MEDIUM
**Status:** NEW

2026.5.25-alpha.1 includes a fix: "Strip fractional seconds from web-search time range filters so Gemini accepts freshness-bound search requests."

Heather's primary model is `google/gemini-3-flash-preview`. If she runs web searches with time constraints ("what happened today", "latest news about X"), those requests may be silently rejected by Gemini when fractional seconds are present in the time filter. The fix is in alpha — it will land in the next stable release after 2026.5.22.

**Workaround until fix ships stable:** Heather should avoid time-constrained search queries that would trigger freshness bounds, or accept that she may get broader (non-bounded) results.

**Risk level:** MEDIUM — web search degradation is real but non-critical for a personal assistant. No action needed until next stable ships.

---

### FINDING-JOSH-59 | Discord Thread Idling — Rate Limit Risk in Active Servers
**Severity:** LOW (preventive)
**Status:** NEW — from community Discord bot research

Community guidance for Discord bots in 2026: in active servers, open threads accumulate over time. A bot that never archives finished threads can end up monitoring dozens of stale conversations, increasing memory footprint and risk of hitting Discord API rate limits if the server becomes busy.

Josh's Discord config (`guilds.1484448262290276464` with `requireMention: false`) means Heather responds to all messages in that guild without needing to be @-mentioned. If the server has active channels, thread accumulation may occur.

**Recommended check:** Once Josh upgrades, verify whether old threads are being archived. OpenClaw 2026.5.22 introduced improved Discord session management as part of the hot-path improvements.

**Risk level:** LOW — preventive awareness only.

---

### FINDING-JOSH-60 | npm Package Shrinkwrap Security Now Required
**Severity:** INFO (security improvement)
**Status:** NEW — from 2026.5.25-alpha.1 changelog

OpenClaw's npm package and owned plugins now ship with generated shrinkwrap, requiring review for lockfile/shrinkwrap changes. This means any OpenClaw plugin Josh installs via ClawHub or npm will have its dependency tree locked.

**Why this matters for Josh:** The skill installation via npm (configured in `openclaw.json`) is now more secure. Dependency confusion attacks and supply-chain tampering are mitigated at the package level.

**Risk level:** INFO — no action needed. Security improvement that applies automatically on upgrade.

---

## Persistent Findings (Unresolved from 2026-05-26 Evening)

| Finding | Severity | Status | Day # |
|---------|----------|--------|---------|
| JOSH-30: MEMORY.md never created | CRITICAL | PERSISTENT | **39+** |
| JOSH-31: HEARTBEAT.md empty | HIGH | **CONFIRMED EMPTY** | **39+** |
| JOSH-39→57: Upgrade to OpenClaw 2026.5.22 | HIGH | **ESCALATED — now 2.5.22 target** | 5 |
| JOSH-37: SOUL.md never personalized | MEDIUM | PERSISTENT | **39+** |
| JOSH-32: Bootstrap TOOLS.md false Google auth | MEDIUM | PERSISTENT | **39+** |
| JOSH-33: iMessage paused + malformed JSON | MEDIUM | PERSISTENT | 30+ |
| JOSH-34: Emoji contradiction (AGENTS vs USER) | MEDIUM | **CONFIRMED ACTIVE** | 6 |
| JOSH-35: streaming.mode progress available | INFO | OPPORTUNITY | — |
| JOSH-36: Mem0 / Active Memory plugin | INFO | OPPORTUNITY | — |
| JOSH-38: Crash notifications | INFO | OPPORTUNITY | — |
| JOSH-40: 2026.5.21 transcript durability | INFO | PERSISTENT | 4 |
| JOSH-42: ClawHub skills security advisory | MEDIUM | PERSISTENT | 4 |
| JOSH-43: defineToolPlugin custom skills | INFO | OPPORTUNITY | — |
| JOSH-44: Meeting capture plugin (now 2026.5.22) | INFO | **READY POST-UPGRADE** | — |
| JOSH-47: OpenRouter routing controls | LOW | PERSISTENT | — |
| JOSH-48: MEMORY.md Day 39+ | CRITICAL | ESCALATING | **39+** |
| JOSH-49: SOUL.md SHA unchanged | MEDIUM | PERSISTENT | **39+** |
| JOSH-50: Dead OpenRouter fallback | MEDIUM | PERSISTENT | 19 |
| JOSH-51: 2026.5.25-alpha.1 in train | INFO | PERSISTENT | 1 |
| JOSH-52: 2026.5.22 stable confirmed | INFO | **RESOLVED → NOW STABLE** | — |
| JOSH-53: Bootstrap hooks confirmed | INFO | **RESOLVED** | — |
| JOSH-54: HEARTBEAT.md empty | HIGH | PERSISTENT | **39+** |
| JOSH-55: TOOLS.md empty | MEDIUM | PERSISTENT | **39+** |
| JOSH-56: AGENTS.md zero customization | MEDIUM | PERSISTENT | 1 |
| JOSH-57: Upgrade target → 2026.5.22 | HIGH | **NEW** | 0 |
| JOSH-58: Gemini fractional seconds fix | MEDIUM | **NEW** | 0 |
| JOSH-59: Discord thread idling | LOW | NEW | 0 |
| JOSH-60: npm shrinkwrap security | INFO | NEW | 0 |

---

## Immediate Action List (No Upgrade Required)

These fixes require **zero platform changes**, **zero downtime**, and can be applied directly to the repo:

1. **Create `workspace/MEMORY.md`** — the single highest-value fix. 5 minutes. See soul-improvements for template.
2. **Fix `workspace/SOUL.md`** — add Josh-specific rules: no emoji reactions, LA timezone, Bliss/Oben HiFi business context.
3. **Fix `workspace/AGENTS.md`** — remove or override the "React Like a Human" emoji reaction section with Josh's STRICT rule.
4. **Populate `workspace/HEARTBEAT.md`** — add Gmail + Calendar check tasks.
5. **Populate `workspace/TOOLS.md`** — add actual environment notes (Google auth, iMessage status, Discord guild, model chain).
6. **Fix `openclaw.json` dead fallback** — remove `openrouter/anthropic/claude-3.5-haiku` (30 seconds, no restart).

**Then (requires VPS access):**
7. **Upgrade to OpenClaw 2026.5.22** — skip 2026.5.20, go straight to 2026.5.22.

---

## Platform Research Notes (2026-05-27)

- **OpenClaw latest stable:** 2026.5.22 (shipped May 23-24, 2026) — Josh is 65+ days behind
- **OpenClaw 2026.5.25-alpha.1:** Alpha. Do NOT upgrade. Gemini time filter fix, npm shrinkwrap security, image paste improvement.
- **AlphaClaw:** 0.9.16 — Day 12 without update.
- **Key 2026.5.22 features for Josh:** 4,100x faster model listing (gateway warmup), meeting notes plugin (Discord voice), safer agent defaults, better session history UI.
- **Community sentiment:** 2026.5.22 upgrade is clean. No regressions reported.
- **AI personal assistant trend:** Every major assistant now ships persistent session-indexed memory. Heather's MEMORY.md gap is falling further behind industry standard with each passing day.
