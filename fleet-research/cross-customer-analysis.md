# Fleet Research: Cross-Customer Analysis
**Last Updated:** 2026-06-03 morning  
**Fleet:** AlphaClaw Apex (2 instances)  
**Customers:** Josh (Heather Schwartz) · Noah (Market Catalyst Agent)

---

## Fleet-Wide Platform Status

| Metric | Josh (Heather) | Noah (MCA) | Fleet Target |
|---|---|---|---|
| OpenClaw version | 2026.3.22 | 2026.4.15 | **2026.5.27** |
| Days behind stable | **73** | **49** | 0 |
| Latest stable | 2026.5.27 | 2026.5.27 | 2026.5.27 |
| Latest beta | 2026.6.1-beta.3 | 2026.6.1-beta.3 | (Monitor only) |
| Next stable target | 2026.5.31 (mid-June) | 2026.5.31 (mid-June) | Monitor — SQLite state, Skill Workshop |
| Primary model | google/gemini-3-flash-preview | anthropic/claude-sonnet-4-6 | — |
| Upgrade action | VPS required | VPS required | — |

**Version note (2026-06-03):** OpenClaw `2026.6.1-beta.3` released TODAY (June 3, 2026) with SQLite-backed iMessage state management, Skill Workshop, iOS push relay, interrupted tool call recovery, cron rate-limit retry + fallback preflight, and Tokenjuice externalized as official plugin. Stable upgrade target remains **2026.5.27**. The `2026.5.31` stable is expected mid-to-late June and will bring SQLite state management to stable users — this changes the iMessage repair strategy for Josh (see JOSH-40).

**Previous version note (2026-06-02):** The 2026-05-31 cross-customer analysis incorrectly cited `2026.5.28` as stable. Confirmed stable is **2026.5.27**. `2026.5.28` reached beta.4 with notable iMessage and Discord improvements — now superseded by the 2026.6.1 beta train.

---

## Workspace Files Gap Analysis

### Josh (lylle-rgb/josh_repo → workspace/)

| File | Status | Days Gap | Notes |
|---|---|---|---|
| SOUL.md | Generic template | 73 | Never personalized for Heather/Josh context |
| AGENTS.md | Functional but contradicts USER.md | 73 | Emoji reactions enabled vs USER.md STRICT disabling |
| TOOLS.md | **Empty** | 73 | No tool documentation |
| HEARTBEAT.md | **Empty** | 73 | No proactive monitoring configured |
| MEMORY.md | **Missing** | 73 | Never created — critical gap |
| IDENTITY.md | Present (basic) | — | OK |
| USER.md | Present (basic) | — | OK |
| BOOTSTRAP.md | Present (stale) | 73 | Should have been deleted at go-live |
| hooks/ | Present | — | bootstrap-extra-files active |
| memory/ | Empty dir | 73 | No memory files — Dreaming has nothing to consolidate |

**Josh gap summary:** Missing MEMORY.md (CRITICAL), empty HEARTBEAT.md (HIGH), empty TOOLS.md (MEDIUM), stale SOUL.md (MEDIUM), AGENTS.md emoji contradiction (MEDIUM), stale BOOTSTRAP.md (MEDIUM).

---

### Noah (lylle-rgb/noah--repo → workspace/)

| File | Status | Days Gap | Notes |
|---|---|---|---|
| SOUL.md | Generic template | 49 | Never personalized for trading agent |
| AGENTS.md | Generic — no trading rules | 49 | No paper-only guardrail, no trading-specific constraints |
| TOOLS.md | **Blank template** | 49 | gog-cli capabilities completely undocumented |
| HEARTBEAT.md | **Structurally broken** | 49 | Fenced code block wraps entire content — agent cannot parse |
| MEMORY.md | **Missing** | 49 | Never created — Dreaming cannot activate without it |
| IDENTITY.md | **Blank template** | 49 | Agent does not know its own name or role |
| USER.md | **Blank template** | 49 | Agent does not know Noah Katz's context |
| BOOTSTRAP.md | Present (stale) | 49 | Should have been deleted at go-live |
| hooks/ | Present | — | bootstrap-extra-files active |
| reports/ | Empty dir | 49 | Intended for catalyst reports — unused |

**Noah gap summary:** IDENTITY.md blank (CRITICAL), USER.md blank (CRITICAL), TOOLS.md blank (CRITICAL), contextPruning TTL bug (CRITICAL — Day 19), HEARTBEAT.md broken (HIGH), MEMORY.md missing (HIGH), AGENTS.md has no trading guardrails (HIGH), SOUL.md generic (MEDIUM), stale BOOTSTRAP.md (MEDIUM).

---

## Cross-Customer Comparison

### Configuration Differences

| Config Item | Josh | Noah | Assessment |
|---|---|---|---|
| Primary model | google/gemini-3-flash-preview | anthropic/claude-sonnet-4-6 | Different providers — appropriate for each use case |
| New model opportunity | gemini-3.1-flash-lite-preview (JOSH-85) | None new | Josh has a prep-step model upgrade available |
| Fallback models | openrouter/gemini-2.5-flash, openrouter/claude-3.5-haiku (dead) | **None** | Josh has a dead fallback; Noah has zero fallbacks (NOAH-102 — now HIGH) |
| contextPruning | Not configured | `"ttl": "5m"` — **BUG Day 19** | Noah's TTL is critically short; Josh has no TTL configured |
| Memory flush | Not configured | `softThresholdTokens: 4000`, `enabled: true` | Noah has memory flush active; Josh does not |
| context reserve | Not configured | `reserveTokensFloor: 40000` | Noah has reserve floor; Josh does not |
| Discord groupPolicy | `open` | `allowlist` | Noah's is more secure — appropriate for trading agent |
| Discord dmPolicy | `open` | `pairing` | Noah's is more secure — appropriate for trading agent |
| Discord streaming | `off` | Default (off) | Both off — both should enable `"progress"` post-upgrade |
| Plugins | discord, usage-tracker | anthropic, discord, usage-tracker, memory-core (allow only) | Noah has memory-core allowed but not in entries — half-configured |
| Dreaming (memory-core) | Not available (pre-upgrade) | Available NOW — needs config | Noah can enable Dreaming today; Josh needs upgrade first |
| Embedding provider | Not configured | Not configured | Neither has hybrid search — both opportunity (NOAH-101) |
| Gateway controlUi | sslip.io + localhost | sslip.io + localhost | Both have remote UI access configured |
| Cron/automation | None | None | Neither has cron — both need it |
| active-memory plugin | Not in config | Not in entries | Neither has active-memory properly configured |

---

## Cross-Customer Key Findings

**1. Both instances are behind on OpenClaw upgrades — by different amounts**  
Josh is 73 days behind (2026.3.22). Noah is 49 days behind (2026.4.15). Both should upgrade to 2026.5.27 now; then target 2026.5.31-stable when it lands (mid-to-late June). Josh's gap is larger and includes iMessage recovery, critical for his personal assistant use case.

**2. Neither instance has persistent memory**  
Both MEMORY.md (the file) and the active-memory plugin (the indexer) are missing from both instances. The April 2026 mem0 temporal algorithm delivers +29.6 pts on temporal queries and +23.1 pts on multi-hop reasoning.
- Josh: Needs MEMORY.md created + active-memory plugin configured (post-upgrade to 2026.5.27)
- Noah: Needs MEMORY.md created + active-memory plugin in `plugins.entries` (available NOW on 2026.4.15)

**3. Noah has a critical one-line bug (Day 19) that Josh does not have**  
Noah's `contextPruning.ttl` of `"5m"` truncates every session over 5 minutes. Every pre-market briefing, every SEC filing review, every Alpaca position discussion has been degraded for 19 consecutive days (~285 total truncation events). The fix is a one-character change in one file. This is the single highest-priority actionable item in the entire fleet.

**4. Noah's security posture is more conservative than Josh's — both correct for their use cases**  
Noah's Discord uses `dmPolicy: pairing` and `groupPolicy: allowlist` — appropriate for a trading agent. Josh's Discord uses `dmPolicy: open` and `groupPolicy: open` — appropriate for a personal assistant responding to all family/team messages. Both configurations are intentional.

**5. Josh has a dead OpenRouter fallback; Noah has no fallbacks — elevated to HIGH for Noah**  
Josh's `openrouter/anthropic/claude-3.5-haiku` endpoint is dead and should be removed. Noah has no fallbacks — if Anthropic API is rate-limited or down at 6:30 AM ET on a catalyst morning, Noah's agent fails silently. **New (June 3):** OpenClaw 2026.6.1-beta.3 introduces cron rate-limit retry with fallback preflight — it will test the fallback model before skipping a cron run. This feature only activates if a fallback exists. NOAH-102 is now HIGH priority.

**6. Josh's workspace is partially populated; Noah's is severely underpopulated**  
Josh has SOUL.md, IDENTITY.md, and USER.md present (even if SOUL.md is generic). Noah has IDENTITY.md and USER.md as completely blank templates — every session starts with zero personalization context. Noah's workspace gap is wider and more urgent.

**7. Josh has a new model upgrade prep opportunity (JOSH-85)**  
`google/gemini-3.1-flash-lite-preview` is now available: 363 tok/s (45% faster than Gemini 2.5 Flash), 1/8 the cost of Pro. Adding it to Josh's models block is a GitHub-only zero-risk prep step. Noah uses Anthropic models and is not affected.

**8. File transfer plugin available for both customers post-upgrade (JOSH-87 / NOAH-97)**  
v2026.5.3 ships `file_fetch`, `dir_list`, `dir_fetch`, `file_write` as native agent tools. For Josh: document management and email attachment handling. For Noah: EDGAR filing downloads directly to `workspace/reports/` — pairs with Noah's existing but unused reports directory.

**9. v2026.5 speed benchmarks quantify upgrade value (JOSH-86 / NOAH-96)**  
2.9× faster cold turn (9.8s → 3.4s), 2.5× faster warm turn (7.5s → 3.0s), 7% lower peak RSS. For Noah's time-critical pre-market window, the cold-start reduction is especially meaningful.

**10. Workboard orchestration: near-term for Noah, longer horizon for Josh**  
Workboard multi-agent primitives directly support Noah's pre/post-market workflow. Josh's personal assistant is single-agent conversational — Workboard is longer-horizon.

**11. Active Memory Dreaming: available NOW for Noah, post-upgrade for Josh (JOSH-88 / NOAH-100)**  
Dreaming shipped in OpenClaw 2026.4.5. Noah is on 2026.4.15 — Dreaming is **already available** on Noah's current install, pending only memory-core plugins.entries addition (NOAH-84) and MEMORY.md creation (NOAH-34). Josh requires upgrade to 2026.5.27 first.

Dreaming auto-curates MEMORY.md via a three-phase consolidation cycle (Light Sleep → REM → Deep Sleep), scoring candidates on six signals (Relevance 30%, Frequency 24%, Query diversity 15%, Recency 15%, Consolidation 10%, Conceptual richness 6%). After each cycle it writes DREAMS.md — a transparency log of promotions and discards.

**Critical implication for Noah:** The contextPruning TTL bug (Day 19) is directly degrading the quality of memory candidates available for Dreaming. Fix NOAH-99 FIRST, then enable Dreaming — ensures first consolidation pass has high-quality inputs.

**12. 2026.5.31-stable / 2026.6.x — iMessage and Discord reliability (JOSH-89)**  
The 2026.5.31 beta train and 2026.6.1 beta train include iMessage polling continuity after denied reactions, duplicate exec approval suppression, Discord recovered-tool-warning suppression, and channel timeout capping. These are directly relevant to Josh's iMessage-heavy personal assistant use case. Monitor for stable promotion (expected mid-to-late June 2026).

**13. Hybrid memory search requires embedding provider — opportunity for Noah (NOAH-101)**  
With an embedding provider configured, `memory_search` uses hybrid search (vector similarity + keyword matching). For Noah's SEC/EDGAR workflow, exact ticker symbol and filing ID recall via keyword is critical. Neither instance currently has an embedding provider configured. Adding one (`openrouter/openai/text-embedding-3-small`) is a GitHub-only medium-priority improvement.

**14. Noah has zero fallback models — single point of failure (NOAH-102) — NOW HIGH PRIORITY**  
Noah has no fallbacks. Anthropic outage or rate limit at 6:30 AM ET = no pre-market analysis, no recovery. **New (June 3):** OpenClaw 2026.6.1-beta introduces cron fallback preflight — it attempts the fallback model before skipping a run. This creates a complete recovery chain, but only if a fallback exists. Medium priority → **HIGH priority**. Add `openrouter/anthropic/claude-sonnet-4-6` as fallback + openrouter:default auth profile in openclaw.json.

**15. [NEW June 3] Cron rate-limit retry + fallback preflight (2026.6.1-beta) — Critical for Noah**  
OpenClaw 2026.6.1-beta.3 (released today) adds cron retry after transient rate limits AND preflight fallback model testing before skipping. For Noah's pre-market catalyst scan at 6:30 AM ET: a single Anthropic rate limit hiccup previously silently skipped the most valuable run of the day. With fallback preflight, the cron job tries OpenRouter before giving up. **Action:** Add OpenRouter fallback today (NOAH-102) — zero risk, immediate benefit when this feature lands in stable.

**16. [NEW June 3] SQLite-backed iMessage state changes Josh's JOSH-33 repair strategy**  
OpenClaw 2026.5.31-beta.3 introduced SQLite-backed iMessage state management (shipping in the upcoming stable). Josh's inbox-state.json has a malformed duplicate key causing the 38-day iMessage pause. **New guidance:** Do NOT manually edit inbox-state.json. After upgrading to ≥2026.5.31-stable, run `openclaw doctor --fix` to trigger the SQLite migration and clean state reset.

---

## Prioritized Fleet Action List

### Zero-Downtime, GitHub-Only (Both Customers)

| Priority | Customer | Action | ID |
|---|---|---|---|
| CRITICAL | Noah | Fix contextPruning TTL `"5m"` → `"30m"` in openclaw.json | NOAH-99 (Day 19) |
| CRITICAL | Noah | Populate IDENTITY.md | NOAH-91 |
| CRITICAL | Noah | Populate USER.md with Noah Katz context | NOAH-91 |
| CRITICAL | Noah | Rewrite TOOLS.md with gog-cli documentation | NOAH-80 |
| CRITICAL | Josh | Create workspace/MEMORY.md stub | JOSH-30/79 |
| HIGH | Noah | Add OpenRouter fallback + auth profile to openclaw.json | NOAH-102 (elevated June 3) |
| HIGH | Noah | Add memory-core to plugins.entries WITH Dreaming config | NOAH-84 / NOAH-100 |
| HIGH | Noah | Fix HEARTBEAT.md (remove fenced block, add Gmail/EDGAR polling) | NOAH-33 |
| HIGH | Noah | Create workspace/MEMORY.md stub | NOAH-34 |
| HIGH | Noah | Add trading rules + paper-only guardrail to AGENTS.md | NOAH-60 |
| HIGH | Josh | Populate HEARTBEAT.md with proactive monitoring tasks | JOSH-31 |
| MEDIUM | Josh | Remove dead OpenRouter fallback from openclaw.json | JOSH-50 |
| MEDIUM | Josh | Fix AGENTS.md emoji contradiction | JOSH-34 |
| MEDIUM | Josh | Personalize SOUL.md for Heather/Josh context | JOSH-37 |
| MEDIUM | Josh | Populate TOOLS.md | JOSH-55 |
| MEDIUM | Josh | Delete BOOTSTRAP.md | JOSH-63 |
| MEDIUM | Noah | Delete BOOTSTRAP.md | NOAH-69 |
| MEDIUM | Josh | Add gemini-3.1-flash-lite-preview to models block | JOSH-85 |
| MEDIUM | Noah | Add embedding provider for hybrid memory search | NOAH-101 |
| INFO | Noah | Add claude-opus-4-8 to models block (optional) | NOAH-98 |
| INFO | Josh | Track 2026.5.31 beta train for stable promotion | JOSH-89 |

### VPS-Required (Both Customers)

| Priority | Customer | Action | ID |
|---|---|---|---|
| HIGH | Josh | Upgrade OpenClaw 2026.3.22 → **2026.5.27** | JOSH-39/81 |
| HIGH | Noah | Upgrade OpenClaw 2026.4.15 → **2026.5.27** | NOAH-32/67 |
| HIGH | Noah | Enable gmailWatch in gogcli/state.json | NOAH-81 |
| HIGH | Noah | Configure pre/post-market cron (6:30 AM ET / 4:30 PM ET) | NOAH-39 |
| HIGH | Josh | Run `openclaw doctor --fix` post-upgrade to reset iMessage state via SQLite | JOSH-40 |
| HIGH | Josh | Verify iMessage resumes post-upgrade | JOSH-73 |
| HIGH (post-upgrade) | Noah | Configure active-memory plugin + Dreaming | NOAH-84 / NOAH-100 |
| HIGH (post-upgrade) | Josh | Configure active-memory plugin + Dreaming | JOSH-72 / JOSH-88 |
| HIGH (post-upgrade) | Noah | Evaluate file transfer plugin for EDGAR workflow | NOAH-97 |
| HIGH (post-upgrade) | Josh | Explore file transfer plugin for document management | JOSH-87 |
| HIGH (post-upgrade) | Noah | Install sec-filing-watcher skill | NOAH-82 |
| HIGH (post-upgrade) | Noah | Evaluate Alpaca MCP Server V2 | NOAH-36 |
| HIGH (post-upgrade) | Noah | Evaluate EdgarTools MCP | NOAH-37 |
| HIGH (post-upgrade) | Noah | Configure Workboard multi-agent pipeline | NOAH-95 |
| MEDIUM | Both | Review ClawHub skills security advisory | JOSH-42 |

---

## Common Patterns Across Fleet

### What Both Instances Share (Shared Template Base)

Both repos share identical `workspace/AGENTS.md` (SHA: `3faead9716a2c168df79c2fba558bd04cd8c76d0`) and `workspace/SOUL.md` (SHA: `792306ac60f6c600b8ded97899354557ce900f40`) — the same files, meaning both instances started from an identical template and neither has been customized. Both also share the same `workspace/TOOLS.md` template (SHA: `917e2fa86ccb01bab7227e223555daa1f5a76ebc`). Fleet-wide improvements to the shared template propagate to both instances simultaneously.

### What Both Instances Lack (Fleet Gaps)

1. **Persistent memory** — Neither has MEMORY.md or active-memory plugin properly configured. Dreaming cannot activate without both prerequisites.
2. **Proactive monitoring** — Neither has a functional HEARTBEAT.md
3. **Tool documentation** — Neither has TOOLS.md populated
4. **Upgraded OpenClaw** — Both are behind (73 days and 49 days respectively)
5. **SOUL.md personalization** — Both use the generic template
6. **Cron automation** — Neither has scheduled tasks configured
7. **Embedding provider** — Neither has hybrid memory search configured
8. **Discord streaming progress** — Both have `streaming: off`; should enable `"progress"` post-upgrade

### What Each Instance Uniquely Needs

**Josh (Heather):**
- iMessage recovery (requires 2026.5.27 upgrade → then `doctor --fix` for SQLite state reset per JOSH-40)
- Dead OpenRouter fallback removal
- AGENTS.md emoji contradiction fix
- Gemini 3.1 Flash-Lite model prep (JOSH-85)
- Dreaming activation post-upgrade (JOSH-88)
- Track 2026.5.31 beta for stable promotion — iMessage + Discord fixes
- Lower urgency on IDENTITY.md/USER.md (already populated, just not deep)

**Noah (Market Catalyst Agent):**
- contextPruning TTL bug fix (CRITICAL — Day 19, one-line fix, 285+ truncation events)
- IDENTITY.md and USER.md populated from blank (CRITICAL)
- Trading-specific AGENTS.md rules and paper-only guardrail
- OpenRouter fallback (now HIGH — required for cron rate-limit retry to be effective)
- SEC filing workflow integrations (gmailWatch, cron, EDGAR, Alpaca MCP V2)
- Dreaming activation NOW — available on current 2026.4.15, no upgrade needed (NOAH-100)
- Embedding provider for hybrid memory search (NOAH-101)
- Workboard orchestration for multi-step trading sessions

---

## Fleet Trend: Version Gap

| Date | Josh Gap | Noah Gap | Stable Target | Notes |
|---|---|---|---|---|
| 2026-03-24 | 0 days | — | 2026.3.22 | Josh last touched |
| 2026-04-22 | — | 0 days | 2026.4.15 | Noah last touched |
| 2026-05-31 (morning) | 71 days | 47 days | 2026.5.27\* | \*Cross-customer incorrectly cited 2026.5.28 as stable |
| 2026-06-02 (morning) | 72 days | 48 days | 2026.5.27 | Reconciled — 2026.5.27 confirmed stable |
| 2026-06-02 (morning-2) | 72 days | 48 days | 2026.5.27 | 2026.5.28-beta.4 in pipeline |
| **2026-06-03 (morning)** | **73 days** | **49 days** | **2026.5.27** | 2026.6.1-beta.3 released today; next stable: 2026.5.31 (mid-June) |

The gap continues widening. OpenClaw is releasing actively; 2026.6.1-beta.3 is out today and the 2026.5.31 stable is expected mid-to-late June. Both customers should upgrade to 2026.5.27 before 2026.5.31 lands to avoid compounding the gap.

**Recommendation:** Apply all GitHub-only fixes immediately (zero VPS, zero risk). For Noah, Dreaming + memory-core config + TTL fix + OpenRouter fallback can ALL be done TODAY via GitHub — no upgrade required. Prioritize VPS upgrades as soon as access is available.

---

*Analysis last updated: 2026-06-03 morning by AlphaClaw Fleet Research daemon.*
