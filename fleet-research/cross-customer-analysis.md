# Fleet Research: Cross-Customer Analysis
**Last Updated:** 2026-06-10 morning
**Fleet:** AlphaClaw Apex (2 instances)
**Customers:** Josh (Heather Schwartz) · Noah (Market Catalyst Agent)

---

## Fleet-Wide Platform Status

| Metric | Josh (Heather) | Noah (MCA) | Fleet Target |
|---|---|---|---|
| OpenClaw version | 2026.3.22 | 2026.4.15 | **2026.6.5** (likely npm stable, verify) |
| Days behind npm stable | **80** | **55** | 0 |
| npm stable (June 9) | 2026.6.2 | 2026.6.2 | May have advanced to 2026.6.5 |
| GitHub beta release | 2026.6.5-beta.5 (June 8) | 2026.6.5-beta.5 | Likely graduated to stable |
| AlphaClaw version | Unknown | Unknown | **0.9.18** |
| Primary model | google/gemini-3-flash-preview | anthropic/claude-sonnet-4-6 | — |
| Fallback model | Dead slug (haiku) | **NONE (NOAH-102)** | Working fallback required |
| Dead slug fix | `openrouter/anthropic/claude-haiku-4-5-20251001` | Same slug for haiku fallback | Confirmed June 10 |
| contextPruning TTL | None configured | **5m (BUG — Day 27)** | 30m for both |
| cron jobs-state.json | Not available (pre-2026.4.20) | Missing by 5 days | Both get on upgrade |

---

## New Fleet-Wide Findings (June 10 Morning)

### 1. OpenClaw 2026.6.5 — Possible npm Stable Graduation
Multiple web sources reference "OpenClaw 2026.6.5" without beta qualifier. The June 8 beta.5 track appears to have shipped. **Verify on both VPS hosts:** `openclaw update --dry-run`.

**Fleet-relevant additions in 2026.6.5:**
- **MCP tool result coercion** — non-text MCP responses coerced before provider converters. Prevents malformed Alpaca/EDGAR responses poisoning Noah's session history; prevents malformed image blocks in Josh's Calendar/Gmail tool calls.
- **Extended thinking session recovery** — Anthropic sessions survive Gateway restart (Josh: OpenRouter fallback; Noah: Opus 4.8 plan)
- **Bundled Parallel web search** — both bots get multi-source search without config
- **Channel safety hardening** — Discord output boundaries strengthened

**Fleet action:** Both customers run `openclaw update` (or AlphaClaw UI → General tab) when VPS access is available.

### 2. Confirmed OpenRouter Model Slugs (June 10)
Via web research, the current correct OpenRouter model slugs are:
- `openrouter/anthropic/claude-sonnet-4-6` (Noah primary fallback)
- `openrouter/anthropic/claude-haiku-4-5-20251001` (both secondary fallback — replaces dead `claude-3.5-haiku`)
- `openrouter/google/gemini-2.5-flash` (Josh fallback — confirm still valid)

**Fleet action:** Update both `openclaw.json` fallback arrays with correct slugs before next provider outage.

### 3. Gemini 3.1 Flash Lite — Josh Speed/Cost Opportunity
`google/gemini-3.1-flash-lite-preview` benchmarks at 363 tokens/sec (45% faster than Gemini 2.5 Flash), 1/8th cost of Gemini 3 Pro. Strong candidate for Josh's heartbeat/routine assistant tasks.

**Josh action:** Add as tertiary fallback or evaluate for heartbeat-tier tasks. Noah stays on Claude Sonnet 4.6.

### 4. Mem0 Persistent Memory Backend — Noah Opportunity
Mem0 managed memory backend via `memory-core` plugin. Semantic search over catalyst history, ticker patterns, trade thesis outcomes. Supports Gemini embeddings (no OpenAI key needed). More robust than file-based MEMORY.md for a high-frequency trading research bot.

**Noah action:** Evaluate after NOAH-99/102 resolved. Requires `MEM0_API_KEY` env var.

### 5. Active Memory setupGraceTimeoutMs (2026.5.2+) — Both Instances Need This When Activating memory-core
On 2026.5.2+, must configure `setupGraceTimeoutMs: 30000` alongside `timeoutMs: 15000` when activating memory-core. Without it, the pre-reply memory recall sub-agent may time out on long tool chains. Critical for Noah's 30-minute sessions.

**Fleet action:** Add to memory-core config block when activating on both instances.

---

## New Fleet-Wide Findings (June 9 Morning — Carried)

### 1. Cron jobs-state.json Isolation (2026.4.20+) — Both Customers Missing This
OpenClaw 2026.4.20 isolated cron runtime state into `jobs-state.json`. Both customers benefit at upgrade.
- **Josh:** Morning email/calendar digest should be cron (not heartbeat drift). Gets on 2026.6.5 upgrade.
- **Noah:** Just missed by 5 days (on 2026.4.15). Every VPS restart is a potential silent cron failure. Gets on upgrade.

### 2. Cron Rate-Limit Retry + Fallback Preflight (2026.6.1+) — Only Works with Fallback Configured
Rate-limit retry with fallback preflight for cron jobs. **Requires working fallback models.** Without fallbacks, cron failures are still silent. Josh: fix dead haiku slug. Noah: NOAH-102 fix (OpenRouter).

### 3. Interrupted Tool Call Recovery (2026.6.1) — Both Get on Upgrade
Clean session recovery from interrupted tool calls. Directly critical for Noah (EDGAR → Alpaca chains). Also benefits Josh's Calendar/Gmail chains post-gog-cli.

---

## New Fleet-Wide Findings (June 8 Morning — Carried)

### 1. npm Stable Clarification
Stable target was 2026.6.2 as of June 8–9. Key improvements included: operator install policy, Discord/Telegram safety hardening, config security hardening, interrupted tool call recovery, cron rate-limit retry.

**Updated June 10:** npm stable may have advanced to 2026.6.5.

### 2. Mandatory Memory Retrieval Rule — Missing from Both AGENTS.md Files
Add to Session Startup section in both workspaces:
```markdown
## Memory Rule
**Search memory before acting.** Before answering questions about the user's preferences, past conversations, or decisions — check MEMORY.md and today's memory file first. Never guess at information that might already be written down.
```

### 3. NVIDIA SkillSpector — Security Standard for All Future Skill Installs
Before any future skill install, verify "Clean" Skill Card. Especially critical for Noah: trading skills with Alpaca access are highest-risk.

---

## New Fleet-Wide Findings (June 7 Morning — Carried)

### 1. memory-core "Dreaming" + `plugins.slots.memory` — Activation Key Missing from Both Configs
`plugins.slots.memory: "memory-core"` is the activation key. Without it, entries config alone does not engage memory-core.

**Config template (apply to both, adjust model):**
```json
"plugins": {
  "slots": { "memory": "memory-core" },
  "entries": {
    "memory-core": {
      "enabled": true,
      "subagent": { "allowModelOverride": true },
      "config": {
        "dreaming": {
          "enabled": true,
          "frequency": "0 3 * * *",
          "model": "CUSTOMER_MODEL_HERE"
        }
      }
    }
  }
}
```
- **Josh:** `"model": "google/gemini-3-flash-preview"`
- **Noah:** `"model": "anthropic/claude-sonnet-4-6"`

### 2. gog-cli = Google Workspace CLI
**Josh** does not have gog-cli installed — critical gap for a personal assistant. Install post-upgrade: `openclaw skills install gog`.

### 3. Noah-Specific: LanceDB Pro Memory Plugin
`memory-lancedb-pro` (CortexReach): Hybrid Vector+BM25 retrieval, cross-encoder reranking. Alternative to Mem0 for Noah.

### 4. Noah-Specific: Claude Opus 4.8 Available
`claude-opus-4-8` available via AlphaClaw 0.9.17+. Add to `agents.defaults.models` for targeted deep EDGAR/earnings analysis cron jobs.

---

## Workspace Files Gap Analysis

### Josh (lylle-rgb/josh_repo → workspace/)

| File | Status | Days Gap | Notes |
|---|---|---|---|
| SOUL.md | Generic template | 80 | Never personalized for Heather/Josh context |
| AGENTS.md | Functional but has emoji contradiction | 80 | USER.md says STRICT NO; AGENTS.md says yes; missing memory retrieval rule |
| TOOLS.md | **Empty template** | 80 | No tool documentation |
| HEARTBEAT.md | **Empty** | 80 | No proactive monitoring configured |
| MEMORY.md | **Missing** | 80 | Never created — CRITICAL |
| IDENTITY.md | Present (basic) | — | OK — Heather identity set |
| USER.md | Present | — | Josh Meyers, LA timezone, Bliss/Oben, emoji STRICT |
| BOOTSTRAP.md | Present (stale) | 80 | Should have been deleted at go-live |
| hooks/ | Present | — | bootstrap-extra-files active |
| memory/ | Present | — | Some daily logs; no MEMORY.md to consolidate into |

**Josh gap summary:** MEMORY.md missing (CRITICAL, Day 80), Google Workspace not connected (CRITICAL Day 7), empty HEARTBEAT.md (HIGH), upgrade 80 days behind (HIGH), gog-cli skill not installed (HIGH), TOOLS.md empty (MEDIUM), dead fallback slug (MEDIUM), streaming off (MEDIUM), stale SOUL.md (MEDIUM), AGENTS.md emoji contradiction + missing memory retrieval rule (MEDIUM), Discord security overexposed (MEDIUM), missing compaction + contextPruning config (MEDIUM), memory-core not configured (MEDIUM), BOOTSTRAP.md stale (LOW).

---

### Noah (lylle-rgb/Noah--Repo → workspace/)

| File | Status | Days Gap | Notes |
|---|---|---|---|
| SOUL.md | Generic template | 55 | Never personalized for trading agent |
| AGENTS.md | Generic — no trading rules | 55 | No paper-only guardrail, no market hours awareness, missing memory retrieval rule |
| TOOLS.md | **Blank template** | 55 | gog-cli, Alpaca, SEC sources all undocumented |
| HEARTBEAT.md | **Structurally broken** | 55 | Fenced code block wraps content — agent reads as code, not instructions |
| MEMORY.md | **Missing** | 55 | Never created |
| IDENTITY.md | **Blank template** | 55 | Agent has no name or self-concept |
| USER.md | **Blank template** | 55 | Agent does not know Noah's context |
| BOOTSTRAP.md | Present (stale) | 55 | Should have been deleted at go-live |
| hooks/ | Present | — | bootstrap-extra-files active |
| reports/ | 1 report (50 days old) | — | ae-target-companies-2026-04-22.md — stale |
| skills/gog-cli | Present | — | Google Workspace CLI installed |
| gogcli/ | Present | — | Supporting state files for gog-cli skill |

**Noah gap summary:** contextPruning TTL bug (CRITICAL, Day 27), no fallback model (CRITICAL), IDENTITY.md + USER.md + TOOLS.md blank (CRITICAL), MEMORY.md missing (HIGH), HEARTBEAT.md structurally broken (HIGH), memory-core half-configured — missing slots key (HIGH), upgrade 55 days behind (HIGH), Discord pairing restart bug (HIGH), gmailWatch disabled (HIGH), missing trading guardrails (HIGH), research report 50 days stale (MEDIUM).

---

## Common Patterns Across Fleet

### What Both Instances Lack (Shared Gaps)

1. **`plugins.slots.memory: "memory-core"`** — activation key missing in both configs
2. **Persistent memory (MEMORY.md)** — neither has long-term memory file
3. **Functional HEARTBEAT.md** — Josh: empty; Noah: structurally broken (fenced code block)
4. **TOOLS.md populated** — neither has environment-specific tool docs
5. **OpenClaw upgrade** — Josh 80 days behind, Noah 55 days behind (stable target: 2026.6.5)
6. **SOUL.md personalized** — both use generic stock template
7. **AGENTS.md memory retrieval rule** — neither has "search memory before acting"
8. **Cron automation** — neither has scheduled tasks configured (needed post-upgrade)
9. **Working fallback model** — Josh: dead slug; Noah: NONE
10. **Discord streaming** — both have `streaming: off`; enable `"progress"` + coalescing post-upgrade
11. **AlphaClaw version** — both behind 0.9.18
12. **BOOTSTRAP.md cleanup** — both still have stale BOOTSTRAP.md
13. **setupGraceTimeoutMs** — both will need `setupGraceTimeoutMs: 30000` when memory-core activated on 2026.5.2+

### What Each Instance Uniquely Needs

**Josh (Heather) — Google Workspace Personal Assistant:**
- MEMORY.md creation (JOSH-30 — CRITICAL, Day 80)
- HEARTBEAT.md with email/calendar monitoring (JOSH-31 — HIGH)
- Google Workspace connection (JOSH-44 — CRITICAL, VPS)
- gog-cli skill installation post-upgrade (HIGH)
- compaction + contextPruning (30m) config in openclaw.json (MEDIUM)
- Discord progressive streaming config (MEDIUM)
- Discord security hardening: `dmPolicy: "pairing"` (MEDIUM)
- Dead slug fix: `openrouter/anthropic/claude-haiku-4-5-20251001` (MEDIUM)
- Gemini 3.1 Flash Lite as heartbeat-tier model (LOW-MEDIUM)
- Cron morning-digest after Google Workspace connected (MEDIUM, post-upgrade)
- iMessage BlueBubbles migration post-upgrade (MEDIUM)
- AGENTS.md emoji contradiction fix (LOW)

**Noah (Market Catalyst Agent) — Stock Catalyst Hunter:**
- contextPruning TTL fix: `"5m"` → `"30m"` (NOAH-99 — CRITICAL, Day 27, one-line change)
- OpenRouter fallback configuration with correct slugs (NOAH-102 — CRITICAL)
- IDENTITY.md + USER.md populated (CRITICAL, Day 55)
- memory-core slots + dreaming config (HIGH)
- MEMORY.md creation (HIGH)
- HEARTBEAT.md repair: remove fenced code block (HIGH)
- Trading guardrails in AGENTS.md: paper-only hard rules (HIGH)
- Discord pairing restart fix: AlphaClaw 0.9.17 upgrade (HIGH)
- gmailWatch enabled for real-time SEC filing push (HIGH)
- Mem0 persistent memory evaluation (OPPORTUNITY — post NOAH-99/102)
- EDGAR webhook architecture for catalyst monitoring (HIGH)
- gog-cli documented in TOOLS.md (MEDIUM)
- Pre-market + post-close cron automation (MEDIUM, post-upgrade)
- Claude Opus 4.8 as deep analysis model option (OPPORTUNITY)

---

## Fleet Capability Comparison

| Capability | Josh | Noah | Notes |
|---|---|---|---|
| Memory plugin | Not configured | Partial (allow, no slots key) | Both need `plugins.slots.memory: "memory-core"` |
| Memory dreaming | None | None | Both need dreaming config post-upgrade |
| Compaction config | **None** | Configured | Josh: add |
| Context pruning | **None** | **5m (BUG — Day 27)** | NOAH-99: fix to 30m; Josh: add 30m |
| Discord security | **Open** (all servers/users) | Allowlist + pairing | Josh: harden to pairing |
| Google Workspace | OAuth flow started | gog-cli installed | Josh: connect + install gog-cli; Noah: document in TOOLS.md |
| Fallback model | Dead (bad slug) | **None (NOAH-102)** | Both effectively have no working fallback |
| Fallback slug fix | `openrouter/anthropic/claude-haiku-4-5-20251001` | Same | Confirmed June 10 |
| Discord run timeout | Default | 30 minutes | Noah: `inboundWorker.runTimeoutMs = 30m` |
| Discord streaming | off | off | Both: enable `"progress"` + coalescing post-upgrade |
| AlphaClaw version | Unknown | Unknown | Both: update to 0.9.18 |
| iMessage | Paused | N/A | Josh: BlueBubbles path available post-upgrade |
| gog-cli skill | **Not installed** | Installed (undocumented) | Josh: install post-upgrade; Noah: document |
| EDGAR/SEC automation | None | Planned (gmailWatch disabled) | Noah: gmailWatch + cron post-upgrade |
| Trading guardrails | N/A | **Missing** | Noah: paper-only rules needed |
| Opus 4.8 | Not configured | Not configured | Noah: add for EDGAR deep analysis cron |
| Memory retrieval rule | Missing from AGENTS.md | Missing from AGENTS.md | Both: add to Session Startup section |
| Gemini 3.1 Flash Lite | Not configured | N/A | Josh: evaluate for heartbeat tasks |
| Mem0 memory backend | Not applicable | Not configured | Noah: evaluate post NOAH-99/102 |
| setupGraceTimeoutMs | Not configured | Not configured | Both: add when activating memory-core |
| Web search | **None** | None | Josh: Gemini grounding path; Noah: Brave/Serper |
| Cron automation | None | None | Both: build post-upgrade using jobs-state.json |
| Interrupted tool call recovery | No (pre-2026.6.1) | No (pre-2026.6.1) | Both get on 2026.6.5 upgrade |
| Cron rate-limit retry | No | No | Both get on 2026.6.1+ upgrade + fallback fix |
| MCP tool result coercion | No (pre-2026.6.5) | No (pre-2026.6.5) | Both get on 2026.6.5 upgrade |

---

## Fleet Version Gap Trend

| Date | Josh Gap | Noah Gap | npm Stable | Notes |
|---|---|---|---|---|
| 2026-03-24 | 0 days | — | 2026.3.22 | Josh last touched |
| 2026-04-22 | — | 0 days | 2026.4.15 | Noah last touched |
| 2026-05-21 (evening) | ~59 days | ~36 days | 2026.5.18 | First scan |
| 2026-06-03 (morning) | 73 days | 49 days | 2026.5.27 | |
| 2026-06-04 (morning) | 74 days | 51 days | 2026.6.1 | |
| 2026-06-06 (morning) | 76 days | 53 days | 2026.6.2 | |
| 2026-06-07 (morning) | 77 days | 53 days | 2026.6.2 | AlphaClaw → 0.9.18; memory-core slots confirmed |
| 2026-06-08 (morning) | 78 days | 54 days | 2026.6.2 | NOAH-99 Day 25; npm stable clarified |
| 2026-06-09 (morning) | 79 days | 54 days | 2026.6.2 | NOAH-99 Day 26; cron isolation + tool recovery findings |
| **2026-06-10 (morning)** | **80 days** | **55 days** | **2026.6.5?** | NOAH-99 Day 27; 2026.6.5 possible stable graduation; confirmed model slugs; Mem0 + Gemini 3.1 Flash Lite findings |

The gap continues widening daily. **NOAH-99 is now Day 27** — the single most urgent action across the fleet.

---

## Fleet Recommendation Priorities (June 10 Morning)

**Apply today (GitHub-only, no VPS, no downtime):**

1. **[NOAH — CRITICAL]** Fix `openclaw.json` contextPruning: `"5m"` → `"30m"` + `keepLastAssistants: 5` — **Day 27**
2. **[NOAH — CRITICAL]** Add OpenRouter fallback (slug: `openrouter/anthropic/claude-haiku-4-5-20251001`) + `openrouter:default` auth to `openclaw.json` (NOAH-102)
3. **[NOAH — CRITICAL]** Populate `workspace/IDENTITY.md` and `workspace/USER.md`
4. **[NOAH — CRITICAL]** Populate `workspace/TOOLS.md` (Google Workspace, Alpaca, EDGAR)
5. **[NOAH — HIGH]** Add `plugins.slots.memory: "memory-core"` + dreaming config to `openclaw.json`
6. **[NOAH — HIGH]** Fix `workspace/HEARTBEAT.md` (remove fenced code block)
7. **[NOAH — HIGH]** Create `workspace/MEMORY.md` stub
8. **[NOAH — HIGH]** Add paper-trading guardrail to `workspace/AGENTS.md`
9. **[JOSH — CRITICAL]** Create `workspace/MEMORY.md` stub — **Day 80**
10. **[JOSH — HIGH]** Replace `workspace/HEARTBEAT.md` with email/calendar task checklist
11. **[JOSH — MEDIUM]** Fix dead fallback slug: `openrouter/anthropic/claude-haiku-4-5-20251001`
12. **[JOSH — MEDIUM]** Add compaction + contextPruning (30m) to `openclaw.json`
13. **[JOSH — MEDIUM]** Enable Discord `"streaming": "progress"` + coalescing config
14. **[JOSH — LOW]** Delete `workspace/BOOTSTRAP.md`
15. **[BOTH — MEDIUM]** Add memory retrieval rule to `workspace/AGENTS.md`
16. **[BOTH — MEDIUM]** Add `setupGraceTimeoutMs: 30000` to memory-core config blocks

**When VPS access is available:**
- Both: Upgrade OpenClaw (verify `openclaw update --dry-run` — target may be 2026.6.5 now)
- Both: Upgrade AlphaClaw → 0.9.18
- Josh: Connect Google Workspace via AlphaClaw UI (JOSH-44)
- Josh: `openclaw skills install gog`
- Josh: Configure BlueBubbles iMessage path
- Noah: Enable gmailWatch for real-time SEC filing push
- Noah: Set `OPENROUTER_API_KEY` + `MEM0_API_KEY` env vars
- Noah: Evaluate `memory-lancedb-pro` vs Mem0 for catalyst memory
- Noah: Design pre-market + post-close cron automation
- Both: Configure AlphaClaw watchdog crash notification channel

---

*Analysis last updated: 2026-06-10 morning by AlphaClaw Fleet Research daemon.*
