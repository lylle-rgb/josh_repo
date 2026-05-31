# Fleet Research: Cross-Customer Analysis
**Last Updated:** 2026-05-31 morning
**Fleet:** AlphaClaw Apex (2 instances)
**Customers:** Josh (Heather Schwartz) · Noah (Market Catalyst Agent)

---

## Fleet-Wide Platform Status

| Metric | Josh (Heather) | Noah (MCA) | Fleet Target |
|---|---|---|---|
| OpenClaw version | 2026.3.22 | 2026.4.15 | **2026.5.28** |
| Days behind stable | **71** | **47** | 0 |
| Latest stable | 2026.5.28 | 2026.5.28 | 2026.5.28 |
| Latest beta | 2026.5.30-beta.1 | 2026.5.30-beta.1 | (Monitor) |
| Primary model | google/gemini-3-flash-preview | anthropic/claude-sonnet-4-6 | — |
| Upgrade action | VPS required | VPS required | **Skip 2026.5.27 — go to 2026.5.28** |

**Key update:** `2026.5.28` was promoted to stable on 2026-05-30. Both instances' upgrade targets are now `2026.5.28`. Do not target `2026.5.27` for either instance.

---

## Workspace Files Gap Analysis

### Josh (lylle-rgb/josh_repo → workspace/)

| File | Status | Days Gap | Notes |
|---|---|---|---|
| SOUL.md | Generic template | 71 | Never personalized for Heather/Josh context |
| AGENTS.md | Functional but contradicts USER.md | 71 | Emoji reactions enabled vs USER.md STRICT disabling |
| TOOLS.md | **Empty** | 71 | No tool documentation |
| HEARTBEAT.md | **Empty** | 71 | No proactive monitoring configured |
| MEMORY.md | **Missing** | 71 | Never created — critical gap |
| IDENTITY.md | Present (basic) | — | OK |
| USER.md | Present (basic) | — | OK |
| BOOTSTRAP.md | Present (stale) | 71 | Should have been deleted at go-live |
| hooks/ | Present | — | bootstrap-extra-files active |
| memory/ | Empty dir | 71 | No memory files |

**Josh gap summary:** Missing MEMORY.md (CRITICAL), empty HEARTBEAT.md (HIGH), empty TOOLS.md (MEDIUM), stale SOUL.md (MEDIUM), AGENTS.md emoji contradiction (MEDIUM), stale BOOTSTRAP.md (MEDIUM).

---

### Noah (lylle-rgb/noah--repo → workspace/)

| File | Status | Days Gap | Notes |
|---|---|---|---|
| SOUL.md | Generic template | 47 | Never personalized for trading agent |
| AGENTS.md | Generic — no trading rules | 47 | No paper-only guardrail, no trading-specific constraints |
| TOOLS.md | **Blank template** | 3 | gog-cli capabilities completely undocumented |
| HEARTBEAT.md | **Structurally broken** | 47 | Fenced code block wraps entire content — agent cannot parse |
| MEMORY.md | **Missing** | 47 | Never created |
| IDENTITY.md | **Blank template** | 47 | Agent does not know its own name or role |
| USER.md | **Blank template** | 47 | Agent does not know Noah Katz's context |
| BOOTSTRAP.md | Present (stale) | 47 | Should have been deleted at go-live |
| hooks/ | Present | — | bootstrap-extra-files active |
| memory/ | Not present | 47 | No memory directory |

**Noah gap summary:** IDENTITY.md blank (CRITICAL), USER.md blank (CRITICAL), TOOLS.md blank (CRITICAL), HEARTBEAT.md broken (HIGH), MEMORY.md missing (HIGH), AGENTS.md has no trading guardrails (HIGH), SOUL.md generic (MEDIUM), stale BOOTSTRAP.md (MEDIUM).

---

## Cross-Customer Comparison

### Configuration Differences

| Config Item | Josh | Noah | Assessment |
|---|---|---|---|
| Primary model | google/gemini-3-flash-preview | anthropic/claude-sonnet-4-6 | Different providers — appropriate for each use case |
| Fallback models | openrouter/gemini-2.5-flash, openrouter/claude-3.5-haiku (dead) | None | Josh has a dead fallback; Noah has none. Both need cleanup. |
| contextPruning | Not configured | `"ttl": "5m"` — **BUG** | Noah's TTL is critically short; Josh has no TTL configured (default behavior) |
| Memory flush | Not configured | `softThresholdTokens: 4000`, `enabled: true` | Noah has memory flush active; Josh does not |
| context reserve | Not configured | `reserveTokensFloor: 40000` | Noah has reserve floor; Josh does not |
| Discord groupPolicy | `open` | `allowlist` | Noah's is more secure (allowlist); Josh's is open to all |
| Discord dmPolicy | `open` | `pairing` | Noah's is more secure (pairing required) |
| Discord streaming | `off` | Default (likely off) | Both off |
| Plugins | discord, usage-tracker | anthropic, discord, usage-tracker, memory-core (allow only) | Noah has memory-core allowed but not in entries — half-configured |
| Gateway controlUi | sslip.io + localhost | sslip.io + localhost | Both have remote UI access configured |
| Cron/automation | None | None | Neither has cron — both need it |
| active-memory plugin | Not in config | Not in entries | Neither has active-memory properly configured |

### Key Cross-Customer Findings

**1. Both instances are behind on OpenClaw upgrades — but by different amounts**

Josh is 71 days behind (2026.3.22). Noah is 47 days behind (2026.4.15). Both should upgrade to 2026.5.28. Josh's upgrade gap is much larger and includes iMessage recovery, a critical feature for his use case.

**2. Neither instance has persistent memory**

Both MEMORY.md (the file) and the active-memory plugin (the indexer) are missing from both instances. The April 2026 mem0 temporal algorithm research confirms persistent memory now delivers +29.6 pts on temporal queries and +23.1 pts on multi-hop reasoning. This is the highest-leverage improvement available to both agents.

- Josh: Needs MEMORY.md created + active-memory plugin configured (post-upgrade to 2026.5.28)
- Noah: Needs MEMORY.md created + active-memory plugin in `plugins.entries` (available NOW on 2026.4.15)

**3. Noah has a critical one-line bug Josh does not have**

Noah's `contextPruning.ttl` of `"5m"` truncates every session over 5 minutes. Josh does not have this misconfiguration. Noah's trading workflow almost certainly runs sessions longer than 5 minutes — every pre-market briefing is degraded. This is the single highest-priority actionable item in the entire fleet.

**4. Noah's security posture is more conservative than Josh's (appropriate for use case)**

Noah's Discord uses `dmPolicy: pairing` and `groupPolicy: allowlist` — appropriate for a trading agent that should not respond to arbitrary Discord DMs. Josh's Discord uses `dmPolicy: open` and `groupPolicy: open` — appropriate for a personal assistant that should respond to all family/team messages. Both configurations are correct for their use cases.

**5. Josh has a dead OpenRouter fallback; Noah has no fallbacks**

Josh's `openrouter/anthropic/claude-3.5-haiku` endpoint is dead and should be removed from the fallbacks array. Noah has no fallbacks configured — if the Anthropic API is unavailable, Noah's agent fails silently. Adding OpenRouter as a fallback for Noah would improve resilience.

**6. Josh's SOUL.md is generic; Noah's workspace is more severely underpopulated**

Josh at least has SOUL.md, IDENTITY.md, and USER.md present (even if SOUL.md is generic and MEMORY.md is missing). Noah has IDENTITY.md and USER.md as completely blank templates, making every session start with zero personalization context. Noah's workspace gap is wider.

**7. Tokenjuice plugin: High priority for Noah, low for Josh**

The new official Tokenjuice plugin compacts exec/bash output. Noah's trading agent is exec-heavy (SEC filing downloads, Alpaca API calls, Gmail search). Josh's personal assistant is conversational-heavy (email, calendar, iMessage). Tokenjuice is a material improvement for Noah post-upgrade; minimal benefit for Josh.

**8. Workboard orchestration: Relevant for Noah, less so for Josh (short term)**

The new Workboard orchestration primitives support multi-agent coordination. Noah's pre-market workflow has natural multi-step structure (catalyst scan → analysis → position sizing → briefing) that could benefit from agent coordination. Josh's personal assistant is single-agent conversational. This is a near-term strategic item for Noah; longer-horizon for Josh.

---

## Prioritized Fleet Action List

### Zero-Downtime, GitHub-Only (Both Customers)

| Priority | Customer | Action | ID |
|---|---|---|---|
| CRITICAL | Noah | Fix contextPruning TTL `"5m"` → `"30m"` in openclaw.json | NOAH-87/95 |
| CRITICAL | Noah | Populate IDENTITY.md | NOAH-91 |
| CRITICAL | Noah | Populate USER.md with Noah Katz context | NOAH-91 |
| CRITICAL | Noah | Rewrite TOOLS.md with gog-cli documentation | NOAH-80 |
| CRITICAL | Josh | Create workspace/MEMORY.md stub | JOSH-30/79 |
| HIGH | Noah | Fix HEARTBEAT.md (remove fenced block, add Gmail EDGAR polling) | NOAH-33 |
| HIGH | Noah | Create workspace/MEMORY.md stub | NOAH-34 |
| HIGH | Noah | Add active-memory to plugins.entries | NOAH-84 |
| HIGH | Noah | Add trading rules + paper-only guardrail to AGENTS.md | NOAH-60 |
| HIGH | Josh | Populate HEARTBEAT.md with proactive monitoring tasks | JOSH-31 |
| MEDIUM | Josh | Remove dead OpenRouter fallback from openclaw.json | JOSH-50 |
| MEDIUM | Josh | Fix AGENTS.md emoji contradiction | JOSH-34 |
| MEDIUM | Josh | Personalize SOUL.md for Heather/Josh context | JOSH-37 |
| MEDIUM | Josh | Populate TOOLS.md | JOSH-55 |
| MEDIUM | Josh | Delete BOOTSTRAP.md | JOSH-63 |
| MEDIUM | Noah | Delete BOOTSTRAP.md | NOAH-69 |
| INFO | Noah | Add claude-opus-4-8 to models block (optional) | NOAH-98 |

### VPS-Required (Both Customers)

| Priority | Customer | Action | ID |
|---|---|---|---|
| HIGH | Josh | Upgrade OpenClaw 2026.3.22 → **2026.5.28** (skip 2026.5.27) | JOSH-39/81 |
| HIGH | Noah | Upgrade OpenClaw 2026.4.15 → **2026.5.28** (skip 2026.5.27) | NOAH-32/93 |
| HIGH | Noah | Enable gmailWatch in gogcli/state.json | NOAH-81 |
| HIGH | Noah | Configure pre/post-market cron (6:30 AM ET / 4:30 PM ET) | NOAH-39 |
| HIGH | Josh | Verify iMessage resumes post-upgrade | JOSH-73 |
| HIGH (post-upgrade) | Noah | Enable Tokenjuice plugin | NOAH-94 |
| HIGH (post-upgrade) | Josh | Configure active-memory plugin | JOSH-72 |
| HIGH (post-upgrade) | Noah | Install sec-filing-watcher skill | NOAH-82 |
| HIGH (post-upgrade) | Noah | Evaluate Alpaca MCP Server V2 | NOAH-36 |
| HIGH (post-upgrade) | Noah | Evaluate EdgarTools MCP | NOAH-37 |
| MEDIUM | Both | Review ClawHub skills security advisory | JOSH-42 |

---

## Common Patterns Across Fleet

### What Both Instances Share (Shared Template Base)

Both repos share identical `workspace/AGENTS.md` (SHA: `3faead9716a2c168df79c2fba558bd04cd8c76d0`) and `workspace/SOUL.md` (SHA: `792306ac60f6c600b8ded97899354557ce900f40`) — these are the same files, meaning both instances started from an identical template and neither has been customized. Both also share the same `workspace/TOOLS.md` template (SHA: `917e2fa86ccb01bab7227e223555daa1f5a76ebc`).

This means fleet-wide improvements to the shared template propagate to both instances simultaneously.

### What Both Instances Lack (Fleet Gaps)

1. **Persistent memory** — Neither has MEMORY.md or active-memory plugin properly configured
2. **Proactive monitoring** — Neither has a functional HEARTBEAT.md
3. **Tool documentation** — Neither has TOOLS.md populated
4. **Upgraded OpenClaw** — Both are behind (71 days and 47 days respectively)
5. **SOUL.md personalization** — Both use the generic template
6. **Cron automation** — Neither has scheduled tasks configured

### What Each Instance Uniquely Needs

**Josh (Heather):**
- iMessage recovery (requires 2026.5.28 upgrade)
- Dead OpenRouter fallback removal
- AGENTS.md emoji contradiction fix
- Lower urgency on IDENTITY.md/USER.md (already populated, just not deep)

**Noah (Market Catalyst Agent):**
- contextPruning TTL bug fix (CRITICAL — Day 17)
- IDENTITY.md and USER.md populated from blank (CRITICAL)
- Trading-specific AGENTS.md rules and paper-only guardrail
- SEC filing workflow integrations (gmailWatch, cron, EDGAR, Alpaca MCP V2)
- Tokenjuice plugin (exec-heavy workloads)
- Workboard orchestration for multi-step trading sessions

---

## Fleet Trend: Version Gap Widening

| Date | Josh Gap | Noah Gap | Notes |
|---|---|---|---|
| 2026-03-24 | 0 days | — | Josh last touched |
| 2026-04-22 | — | 0 days | Noah last touched |
| 2026-05-31 (morning) | **71 days** | **47 days** | After 2026.5.28 stable release |

The gap is widening for both instances. OpenClaw is actively releasing new stable versions. With 2026.5.28 now stable and 2026.5.30-beta.1 released today, the gap will continue to grow until VPS access is applied for upgrades.

**Recommendation:** Prioritize upgrades for both customers as soon as VPS access is available. The GitHub-only fixes can and should be applied immediately — they don't require any VPS interaction and the AlphaClaw self-healing watchdog provides a safety net.

---

*Analysis last updated: 2026-05-31 morning by AlphaClaw Fleet Research daemon.*
