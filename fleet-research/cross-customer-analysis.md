# Fleet Research: Cross-Customer Analysis
**Last Updated:** 2026-06-06 morning
**Fleet:** AlphaClaw Apex (2 instances)
**Customers:** Josh (Heather Schwartz) · Noah (Market Catalyst Agent)

---

## Fleet-Wide Platform Status

| Metric | Josh (Heather) | Noah (MCA) | Fleet Target |
|---|---|---|---|
| OpenClaw version | 2026.3.22 | 2026.4.15 | **2026.6.2** |
| Days behind stable | **76** | **53** | 0 |
| Latest stable | **2026.6.2** | **2026.6.2** | Released ~June 5–6, 2026 |
| Next beta track | 2026.5.31-beta (Tailscale Serve) | 2026.5.31-beta | Monitor only |
| Primary model | google/gemini-3-flash-preview | anthropic/claude-sonnet-4-6 | — |
| Fallback model | openrouter/claude-3.5-haiku (dead) | **NONE** | CRITICAL for Noah |
| Upgrade action | VPS required | VPS required | — |

**Version note (2026-06-06 morning):** OpenClaw **2026.6.2 is now the latest stable**. The target has moved from 2026.6.1 → 2026.6.2. Key additions over 2026.6.1: operator install policy (replaces dangerous-code scanner), broader channel reliability fixes (Discord, Telegram, Feishu, WhatsApp, iMessage), agent and CLI runtime recovery improvements, CI and packaging tightening.

Both customers should target **2026.6.2** for their upgrade. All features that were in beta at the June 3 scan (Skill Workshop, SQLite-backed iMessage state, interrupted tool call recovery, iOS push relay) are confirmed stable in 2026.6.2.

---

## Workspace Files Gap Analysis

### Josh (lylle-rgb/josh_repo → workspace/)

| File | Status | Days Gap | Notes |
|---|---|---|---|
| SOUL.md | Generic template | 76 | Never personalized for Heather/Josh context |
| AGENTS.md | Functional but contradicts USER.md | 76 | Emoji reactions rule vs USER.md STRICT disable |
| TOOLS.md | **Empty template** | 76 | No tool documentation |
| HEARTBEAT.md | **Empty** | 76 | No proactive monitoring configured |
| MEMORY.md | **Missing** | 76 | Never created — CRITICAL |
| IDENTITY.md | Present (basic) | — | OK |
| USER.md | Present | — | Josh Meyers, LA timezone, Bliss/Oben, emoji STRICT |
| BOOTSTRAP.md | Present (stale) | 76 | Should have been deleted at go-live |
| hooks/ | Present | — | bootstrap-extra-files active |
| memory/ | Present | — | Some daily logs; no MEMORY.md to consolidate into |

**Josh gap summary:** MEMORY.md missing (CRITICAL), empty HEARTBEAT.md (HIGH), upgrade 76 days behind (HIGH), TOOLS.md empty (MEDIUM), stale SOUL.md (MEDIUM), AGENTS.md emoji contradiction (MEDIUM), Discord security overexposed (MEDIUM — NEW), missing compaction config (MEDIUM — NEW), missing memory-core plugin (MEDIUM — NEW).

---

### Noah (lylle-rgb/noah--repo → workspace/)

| File | Status | Days Gap | Notes |
|---|---|---|---|
| SOUL.md | Generic template | 53 | Never personalized for trading agent |
| AGENTS.md | Generic — no trading rules | 53 | No paper-only guardrail, no market hours awareness |
| TOOLS.md | **Blank template** | 53 | gog-cli, Alpaca, SEC sources all undocumented |
| HEARTBEAT.md | **Structurally broken** | 53 | Fenced code block wraps content — agent reads it as code |
| MEMORY.md | **Missing** | 53 | Never created |
| IDENTITY.md | **Blank template** | 53 | Agent has no name or self-concept |
| USER.md | **Blank template** | 53 | Agent does not know Noah’s context |
| BOOTSTRAP.md | Present (stale) | 53 | Should have been deleted at go-live |
| hooks/ | Present | — | bootstrap-extra-files active |
| reports/ | 1 report present | — | ae-target-companies-2026-04-22.md — not referenced in memory |

**Noah gap summary:** contextPruning TTL bug truncating every session (CRITICAL — Day 22), no fallback model (CRITICAL), IDENTITY.md + USER.md blank (CRITICAL), MEMORY.md missing (HIGH), HEARTBEAT.md structurally broken (HIGH), memory-core half-configured (HIGH), upgrade 53 days behind (HIGH).

---

## Common Patterns Across Fleet

### What Both Instances Share (Shared Template Base)

Both repos share identical `workspace/AGENTS.md`, `workspace/SOUL.md`, and `workspace/TOOLS.md` — the same stock template. Neither has been customized. Both lack:

1. **Persistent memory** — Neither has MEMORY.md. Memory-core is partially configured for Noah; Josh has none.
2. **Proactive monitoring** — Neither has a functional HEARTBEAT.md (Josh: empty; Noah: fenced code block)
3. **Tool documentation** — Neither has TOOLS.md populated with environment-specific details
4. **Upgraded OpenClaw** — Both are behind (76 days and 53 days). Target: 2026.6.2
5. **SOUL.md personalization** — Both use the generic template
6. **Cron automation** — Neither has scheduled tasks configured
7. **Discord streaming progress** — Both have `streaming: off`; enable `"progress"` post-upgrade
8. **AlphaClaw crash notifications** — Neither configured watchdog alert channel

### What Each Instance Uniquely Needs

**Josh (Heather) — Google Workspace Personal Assistant:**
- MEMORY.md creation (JOSH-30 — CRITICAL, 76 days)
- HEARTBEAT.md with email/calendar monitoring (JOSH-31 — HIGH)
- compaction + contextPruning config (JOSH-46 — MEDIUM, GitHub-only)
- Discord security hardening: allowlist + pairing (JOSH-45 — MEDIUM, GitHub-only)
- memory-core plugin activation (JOSH-47 — MEDIUM, GitHub-only)
- iMessage recovery post-upgrade via SQLite migration (JOSH-33/JOSH-44)
- AGENTS.md emoji contradiction fix (JOSH-34)
- Dead OpenRouter fallback removal or repair (currently `openrouter/claude-3.5-haiku`)

**Noah (Market Catalyst Agent) — Stock Catalyst Hunter:**
- contextPruning TTL fix: `"5m"` → `"30m"` (NOAH-99 — CRITICAL, Day 22, one character)
- OpenRouter fallback configuration (NOAH-102 — CRITICAL, cron preflight requires it)
- IDENTITY.md + USER.md populated from blank (NOAH-91 — CRITICAL)
- TOOLS.md populated with gog-cli, Alpaca, SEC sources (NOAH-80 — CRITICAL)
- Memory directory + MEMORY.md creation (NOAH-47/34 — CRITICAL/HIGH)
- HEARTBEAT.md repair: remove fenced code block (NOAH-33 — HIGH)
- Trading guardrails in AGENTS.md: paper-only hard rules (NOAH-60 — HIGH)
- Market hours awareness in AGENTS.md (NOAH-56 — MEDIUM, NEW June 6)
- memory-core plugin fully enabled in entries (NOAH-84 — HIGH)
- EDGAR webhook integration for real-time filing alerts (NOAH-55 — HIGH, NEW June 6)
- Alpaca webhook/websocket for fill confirmations (NOAH-53 — MEDIUM)
- Claude Opus 4.8 added as model option for deep EDGAR analysis (NOAH-51 — MEDIUM)

---

## Fleet Capability Comparison

| Capability | Josh | Noah | Notes |
|---|---|---|---|
| Memory plugin | None | Partial (allow list only) | JOSH-47: add memory-core; Noah needs entries config |
| Compaction config | **None** | Configured | JOSH-46: add compaction + memoryFlush |
| Context pruning | **None** | Configured (broken — 5m) | NOAH-99: fix TTL; Josh should add with 30m |
| Discord security | **Open** (all servers, all users) | Allowlist + pairing | JOSH-45: harden to match Noah |
| Google Workspace | Connected (Gemini API) | Connected (full) | Josh: verify Gmail/Calendar access works |
| Fallback model | Dead (claude-3.5-haiku) | None | Both effectively have no working fallback |
| Discord run timeout | Default | 30 minutes | Noah’s `inboundWorker.runTimeoutMs = 30m` |
| Discord listener timeout | Default | 2 minutes | Noah’s `eventQueue.listenerTimeout = 120000` |
| iMessage | Paused (broken JSON) | N/A | Fix via SQLite migration post-upgrade |
| Skills/plugins | discord + usage-tracker | discord + usage-tracker + anthropic + memory-core | Noah has more |
| EDGAR/SEC automation | None planned | Planned (cron) | NOAH-55: EDGAR webhook > polling |
| Trading guardrails | N/A | Missing | NOAH-60: paper-only rules urgently needed |

---

## Fleet Version Gap Trend

| Date | Josh Gap | Noah Gap | Stable Target | Notes |
|---|---|---|---|---|
| 2026-03-24 | 0 days | — | 2026.3.22 | Josh last touched |
| 2026-04-22 | — | 0 days | 2026.4.15 | Noah last touched |
| 2026-05-21 (evening) | ~59 days | ~36 days | 2026.5.18 | First scan |
| 2026-06-03 (morning) | 73 days | 49 days | 2026.5.27 | 2026.6.1-beta.3 released |
| 2026-06-04 (morning) | 74 days | 51 days | 2026.6.1 | Target updated to 2026.6.1 |
| **2026-06-06 (morning)** | **76 days** | **53 days** | **2026.6.2** | **Target updated to 2026.6.2 (NEW stable)** |

The gap continues widening. Both customers have actionable GitHub-only fixes available today. The most critical single action across the entire fleet is NOAH-99: one character in `openclaw.json` that has been truncating every Noah session for 22 days.

---

## Fleet Recommendation Priorities (June 6 Morning)

**Apply today (GitHub-only, no VPS, no downtime):**

1. **[NOAH — CRITICAL]** Fix `openclaw.json` contextPruning: `"5m"` → `"30m"` — Day 22
2. **[NOAH — CRITICAL]** Populate `workspace/IDENTITY.md` and `workspace/USER.md`
3. **[NOAH — CRITICAL]** Populate `workspace/TOOLS.md` with gog-cli/Alpaca/SEC docs
4. **[NOAH — HIGH]** Create `workspace/memory/2026-06-06.md` to establish memory directory
5. **[NOAH — HIGH]** Fix `workspace/HEARTBEAT.md` (remove fenced code block)
6. **[JOSH — CRITICAL]** Create `workspace/MEMORY.md` stub
7. **[JOSH — HIGH]** Replace `workspace/HEARTBEAT.md` with email/calendar monitoring tasks
8. **[JOSH — MEDIUM]** Add compaction + contextPruning to `openclaw.json`
9. **[JOSH — MEDIUM]** Harden Discord security in `openclaw.json`
10. **[JOSH — MEDIUM]** Add memory-core to `openclaw.json` plugins

**When VPS access is available:**
- Both: `openclaw upgrade` to 2026.6.2
- Josh: `openclaw doctor --fix` (SQLite iMessage migration)
- Both: Configure AlphaClaw watchdog crash notification channel

---

*Analysis last updated: 2026-06-06 morning by AlphaClaw Fleet Research daemon.*
