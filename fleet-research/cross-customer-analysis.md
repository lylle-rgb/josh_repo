# Fleet Research: Cross-Customer Analysis
**Last Updated:** 2026-06-04 morning
**Fleet:** AlphaClaw Apex (2 instances)
**Customers:** Josh (Heather Schwartz) · Noah (Market Catalyst Agent)

---

## Fleet-Wide Platform Status

| Metric | Josh (Heather) | Noah (MCA) | Fleet Target |
|---|---|---|---|
| OpenClaw version | 2026.3.22 | 2026.4.15 | **2026.6.1** |
| Days behind stable | **74** | **51** | 0 |
| Latest stable | **2026.6.1** | **2026.6.1** | 2026.6.1 (released June 3, 2026) |
| Beta track | 2026.6.2-beta.1 | 2026.6.2-beta.1 | Monitor only |
| Primary model | google/gemini-3-flash-preview | anthropic/claude-sonnet-4-6 | — |
| Fallback model | openrouter/claude-3.5-haiku (dead) | **NONE** | CRITICAL for Noah |
| Upgrade action | VPS required | VPS required | — |

**Version note (2026-06-04 morning):** OpenClaw **2026.6.1 is now stable** (graduated June 3, 2026). The stable target has changed from 2026.5.27 to 2026.6.1. Both customers should upgrade directly to 2026.6.1 — do not target 2026.5.27. Key additions: Skill Workshop, SQLite-backed state management (iMessage, inbound queues, session metadata), memory QMD improvements, runtime recovery, cron rate-limit retry + fallback preflight, MiniMax M3 support, OpenRouter SQLite model caching. Additionally, 2026.6.2-beta.1 (released June 3) introduces the new operator install policy replacing the dangerous-code scanner — expect this to become stable in late June.

---

## Workspace Files Gap Analysis

### Josh (lylle-rgb/josh_repo → workspace/)

| File | Status | Days Gap | Notes |
|---|---|---|---|
| SOUL.md | Generic template | 74 | Never personalized for Heather/Josh context |
| AGENTS.md | Functional but contradicts USER.md | 74 | Emoji reactions enabled vs USER.md STRICT disabling |
| TOOLS.md | **Empty** | 74 | No tool documentation |
| HEARTBEAT.md | **Empty** | 74 | No proactive monitoring configured |
| MEMORY.md | **Missing** | 74 | Never created — critical gap |
| IDENTITY.md | Present (basic) | — | OK |
| USER.md | Present (basic) | — | Josh Meyers, LA timezone, Bliss/Oben |
| BOOTSTRAP.md | Present (stale) | 74 | Should have been deleted at go-live |
| hooks/ | Present | — | bootstrap-extra-files active |
| memory/ | Partial | — | Some daily logs exist; no MEMORY.md to consolidate into |

**Josh gap summary:** Google Workspace NOT connected (CRITICAL), MEMORY.md missing (CRITICAL), empty HEARTBEAT.md (HIGH), upgrade 74 days behind (HIGH), TOOLS.md empty (MEDIUM), stale SOUL.md (MEDIUM), AGENTS.md emoji contradiction (MEDIUM), stale BOOTSTRAP.md (MEDIUM), dead OpenRouter fallback (MEDIUM).

---

### Noah (lylle-rgb/noah--repo → workspace/)

| File | Status | Days Gap | Notes |
|---|---|---|---|
| SOUL.md | Generic template | 51 | Never personalized for trading agent |
| AGENTS.md | Generic — no trading rules | 51 | No paper-only guardrail, no trading-specific constraints |
| TOOLS.md | **Blank template** | 51 | gog-cli capabilities completely undocumented |
| HEARTBEAT.md | **Structurally broken** | 51 | Fenced code block wraps entire content — agent cannot parse |
| MEMORY.md | **Missing** | 51 | Never created |
| IDENTITY.md | **Blank template** | 51 | Agent does not know its own name or role |
| USER.md | **Blank template** | 51 | Agent does not know Noah Katz's context |
| BOOTSTRAP.md | Present (stale) | 51 | Should have been deleted at go-live |
| hooks/ | Present | — | bootstrap-extra-files active |
| reports/ | Contains 1 report | — | ae-target-companies-2026-04-22.md (21KB) — not referenced in memory |

**Noah gap summary:** contextPruning TTL bug truncating every session (CRITICAL — Day 21), no fallback model (CRITICAL), IDENTITY.md + USER.md blank (CRITICAL — now confirmed: Noah Katz, Ngkatz.ai@gmail.com), MEMORY.md missing (HIGH), HEARTBEAT.md structurally broken (HIGH), memory-core half-configured (HIGH), upgrade 51 days behind (HIGH).

---

## Common Patterns Across Fleet

### What Both Instances Share (Shared Template Base)

Both repos share identical `workspace/AGENTS.md`, `workspace/SOUL.md`, and `workspace/TOOLS.md` — the same stock template, meaning neither has been customized. Fleet-wide improvements to the shared template propagate to both instances simultaneously.

### What Both Instances Lack (Fleet Gaps)

1. **Persistent memory** — Neither has MEMORY.md created. Memory-core is configured for Noah (but half-loaded); Josh has no memory plugin.
2. **Proactive monitoring** — Neither has a functional HEARTBEAT.md (Josh: empty; Noah: fenced code block)
3. **Tool documentation** — Neither has TOOLS.md populated with environment-specific details
4. **Upgraded OpenClaw** — Both are behind (74 days and 51 days respectively). Target: 2026.6.1
5. **SOUL.md personalization** — Both use the generic template
6. **Cron automation** — Neither has scheduled tasks configured
7. **Skill Workshop skills** — Neither has installed community skills from ClawHub (Memory Core, Web Browsing are top recommendations)
8. **Discord streaming progress** — Both have `streaming: off`; consider enabling `"progress"` post-upgrade

### What Each Instance Uniquely Needs

**Josh (Heather) — Google Workspace Personal Assistant:**
- Google Workspace connection (email/calendar/contacts completely inaccessible — 74 days)
- Gemini OAuth key audit (verify AIza key, not ya29 OAuth token)
- iMessage recovery (2026.6.1 SQLite migration auto-fixes malformed inbox-state.json)
- Dead OpenRouter fallback removal (causes 30s timeout risk on model failure)
- AGENTS.md emoji contradiction fix (USER.md says STRICT NO, AGENTS.md says react)
- Voice session follow evaluation (Josh has Discord calls — Heather could take meeting notes)
- Memory Core skill installation (post-upgrade, top community recommendation)

**Noah (Market Catalyst Agent) — Stock Catalyst Hunter:**
- contextPruning TTL fix: `"5m"` → `"30m"` (CRITICAL, Day 21, one-line change)
- OpenRouter fallback configuration (CRITICAL — required for cron rate-limit retry to work)
- IDENTITY.md + USER.md populated from blank (Noah Katz confirmed, Ngkatz.ai@gmail.com)
- Trading-specific AGENTS.md rules (paper-only guardrail, audit trail, ET timezone)
- HEARTBEAT.md repair (structurally broken — no autonomous monitoring ever fired)
- memory-core plugin fully enabled (in allow list but missing from entries)
- Alpaca trading skill installation from Skill Workshop (post-upgrade)
- EDGAR/SEC filing skill evaluation from ClawHub (post-upgrade)

---

## Fleet Capability Comparison

| Capability | Josh | Noah | Notes |
|---|---|---|---|
| Memory plugin | None | Configured (half) | Noah has memory-core in allow list |
| Compaction config | None | Configured | Noah has reserveTokensFloor + memoryFlush |
| Context pruning | None | Configured (broken) | Noah has it but TTL is wrong |
| Google Workspace | Not connected | Connected (full) | Noah: 14 services, full read+write |
| Fallback model | Dead (claude-3.5-haiku) | None | Both effectively have no working fallback |
| Discord run timeout | Default | 30 minutes | Noah has inboundWorker.runTimeoutMs = 30m |
| Discord listener timeout | Default | 2 minutes | Noah has eventQueue.listenerTimeout = 120000 |
| iMessage | Paused (broken JSON) | N/A | Josh-only channel |
| Skills/plugins | discord + usage-tracker | discord + usage-tracker + anthropic + memory-core | Noah has more plugins allowed |

---

## Fleet Trend: Version Gap

| Date | Josh Gap | Noah Gap | Stable Target | Notes |
|---|---|---|---|---|
| 2026-03-24 | 0 days | — | 2026.3.22 | Josh last touched |
| 2026-04-22 | — | 0 days | 2026.4.15 | Noah last touched |
| 2026-05-31 (morning) | 71 days | 47 days | 2026.5.27 | — |
| 2026-06-02 (morning) | 72 days | 48 days | 2026.5.27 | Reconciled stable |
| 2026-06-03 (morning) | 73 days | 49 days | 2026.5.27 | 2026.6.1-beta.3 released |
| **2026-06-04 (morning)** | **74 days** | **51 days** | **2026.6.1** | **Target updated — 2026.6.1 is now stable** |

The gap continues widening. The upgrade target has changed: **both customers should upgrade directly to 2026.6.1** (not 2026.5.27). 2026.6.2-beta.1 is in the pipeline with the operator install policy overhaul.

**Recommendation:** Apply all GitHub-only fixes immediately (zero VPS, zero risk). For Noah, contextPruning TTL fix + OpenRouter fallback config can be done TODAY via GitHub — no upgrade required and both are CRITICAL. For Josh, MEMORY.md creation, HEARTBEAT.md replacement, and OpenRouter fallback removal are the highest-value GitHub-only fixes. Prioritize VPS upgrades as soon as access is available, targeting 2026.6.1.

---

*Analysis last updated: 2026-06-04 morning by AlphaClaw Fleet Research daemon.*
