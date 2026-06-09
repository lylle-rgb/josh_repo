# Fleet Research: Cross-Customer Analysis
**Last Updated:** 2026-06-09 morning
**Fleet:** AlphaClaw Apex (2 instances)
**Customers:** Josh (Heather Schwartz) · Noah (Market Catalyst Agent)

---

## Fleet-Wide Platform Status

| Metric | Josh (Heather) | Noah (MCA) | Fleet Target |
|---|---|---|---|
| OpenClaw version | 2026.3.22 | 2026.4.15 | **2026.6.2** (npm stable) |
| Days behind npm stable | **79** | **54** | 0 |
| npm stable | **2026.6.2** | **2026.6.2** | Confirmed June 9, 2026 |
| GitHub beta release | 2026.6.5-beta.5 | 2026.6.5-beta.5 | Not yet npm stable |
| AlphaClaw version | Unknown | Unknown | **0.9.18** |
| Primary model | google/gemini-3-flash-preview | anthropic/claude-sonnet-4-6 | — |
| Fallback model | Dead (bad haiku slug) | **NONE (NOAH-102)** | Working fallback required |
| contextPruning TTL | None configured | **5m (BUG — Day 26)** | 30m for both |
| cron jobs-state.json | Not available (pre-2026.4.20) | **Missing by 5 days** | Both get on 2026.6.2 upgrade |

**Version note (2026-06-09 morning):** npm stable advanced to **2026.6.2** (up from 2026.5.28 per prior morning scans). The 2026.6.5-beta.5 track shipped June 8 with QQBot reasoning tag stripping, MCP tool result coercion, Parallel web search, SQLite state storage, and Anthropic extended-thinking session recovery.

---

## Workspace Files Gap Analysis

### Josh (lylle-rgb/josh_repo → workspace/)

| File | Status | Days Gap | Notes |
|---|---|---|---|
| SOUL.md | Generic template | 79 | Never personalized for Heather/Josh context |
| AGENTS.md | Functional but has emoji contradiction | 79 | USER.md says STRICT NO; AGENTS.md says yes; missing memory retrieval rule |
| TOOLS.md | **Empty template** | 79 | No tool documentation |
| HEARTBEAT.md | **Empty** | 79 | No proactive monitoring configured |
| MEMORY.md | **Missing** | 79 | Never created — CRITICAL |
| IDENTITY.md | Present (basic) | — | OK — Heather identity set |
| USER.md | Present | — | Josh Meyers, LA timezone, Bliss/Oben, emoji STRICT |
| BOOTSTRAP.md | Present (stale) | 79 | Should have been deleted at go-live |
| hooks/ | Present | — | bootstrap-extra-files active |
| memory/ | Present | — | Some daily logs; no MEMORY.md to consolidate into |

**Josh gap summary:** MEMORY.md missing (CRITICAL, Day 79), empty HEARTBEAT.md (HIGH), upgrade 79 days behind (HIGH), Google Workspace not connected (CRITICAL Day 6), gog-cli skill not installed (HIGH), TOOLS.md empty (MEDIUM), stale SOUL.md (MEDIUM), AGENTS.md emoji contradiction + missing memory retrieval rule (MEDIUM), Discord security overexposed (MEDIUM), missing compaction + contextPruning config (MEDIUM), memory-core not configured (MEDIUM), BOOTSTRAP.md stale (LOW).

---

### Noah (lylle-rgb/noah--repo → workspace/)

| File | Status | Days Gap | Notes |
|---|---|---|---|
| SOUL.md | Generic template | 54 | Never personalized for trading agent |
| AGENTS.md | Generic — no trading rules | 54 | No paper-only guardrail, no market hours awareness, missing memory retrieval rule |
| TOOLS.md | **Blank template** | 54 | gog-cli, Alpaca, SEC sources all undocumented |
| HEARTBEAT.md | **Structurally broken** | 54 | Fenced code block wraps content — agent reads as code, not instructions |
| MEMORY.md | **Missing** | 54 | Never created |
| IDENTITY.md | **Blank template** | 54 | Agent has no name or self-concept |
| USER.md | **Blank template** | 54 | Agent does not know Noah's context |
| BOOTSTRAP.md | Present (stale) | 54 | Should have been deleted at go-live |
| hooks/ | Present | — | bootstrap-extra-files active |
| reports/ | 1 report (49 days old) | — | ae-target-companies-2026-04-22.md — stale, not reflected in memory |
| skills/gog-cli | Present | — | Google Workspace CLI (Gmail, Calendar, Drive) |
| gogcli/ | Present | — | Supporting state files for gog-cli skill |

**Noah gap summary:** contextPruning TTL bug (CRITICAL, Day 26), no fallback model (CRITICAL), IDENTITY.md + USER.md + TOOLS.md blank (CRITICAL), MEMORY.md missing (HIGH), HEARTBEAT.md structurally broken (HIGH), memory-core half-configured (HIGH), upgrade 54 days behind (HIGH), Discord pairing restart bug (HIGH), gmailWatch disabled (HIGH), missing trading guardrails (HIGH), research report 49 days stale (MEDIUM).

---

## New Fleet-Wide Findings (June 9 Morning)

### 1. Cron jobs-state.json Isolation (2026.4.20+) — Both Customers Missing This
OpenClaw 2026.4.20 isolated cron runtime state into `jobs-state.json`. Cron jobs now survive session restarts without state corruption. `openclaw cron run --wait` gains timeout + poll-interval controls for precise run management.

- **Josh:** Has no cron jobs — but the morning email/calendar digest (JOSH-31) should be cron, not heartbeat. Gets this on 2026.6.2 upgrade.
- **Noah:** Just missed this by 5 days (is on 2026.4.15; shipped in 2026.4.20). Every VPS restart has been a potential silent research run corruption. Gets this immediately on upgrade.

**Fleet action:** Both customers benefit from `jobs-state.json` isolation at 2026.6.2 upgrade. Plan cron automation after upgrade.

### 2. Cron Rate-Limit Retry + Fallback Preflight (2026.6.1+) — Only Works with Fallback Configured
OpenClaw 2026.6.1+ added rate-limit retry with fallback preflight for cron jobs. The primary model hitting a rate limit during a scheduled run can now auto-retry with a configured fallback.

**Critical dependency:** Both customers need working fallback models configured (Josh: fix dead haiku slug; Noah: NOAH-102 fix to add OpenRouter). Without fallbacks, cron rate-limit failures are still silent.

**Fleet config (apply to both):**
- Josh: Fix dead `openrouter/anthropic/claude-3.5-haiku` slug → `openrouter/anthropic/claude-haiku-4-5`
- Noah: Add `openrouter:default` auth profile + OpenRouter fallback models (NOAH-102)

### 3. Interrupted Tool Call Recovery (2026.6.1) — Both Customers Get This on Upgrade
OpenClaw 2026.6.1 added clean session recovery from interrupted tool calls. When a tool call is interrupted mid-execution, history is no longer poisoned.

- **Josh:** Benefits for Google Calendar/Gmail tool calls after gog-cli is installed
- **Noah:** Directly critical — EDGAR fetch → Alpaca check chains are multi-step; any interruption was previously unrecoverable

**Fleet action:** Both customers get this automatically at 2026.6.2 upgrade.

---

## New Fleet-Wide Findings (June 8 Morning — Carried)

### 1. npm Stable Clarification — Now 2026.6.2
Stable target is **2026.6.2** as of June 9. Key improvements:
- 2026.6.2: Operator install policy replaces dangerous-code scanner, Discord/Telegram safety hardening, strengthened config security, hardened agent/provider recovery
- 2026.6.1: Interrupted tool call recovery, cron rate-limit retry + fallback preflight
- 2026.5.28 (included): Group prompt text out of system prompt (security), blocked unsafe command wrappers

**Fleet action:** Both customers run `openclaw update` → lands on 2026.6.2. Or upgrade via AlphaClaw UI.

### 2. Mandatory Memory Retrieval Rule — Missing from Both AGENTS.md Files
OpenClaw 2026 best practices: add "search memory before acting" to AGENTS.md. Both AGENTS.md files use the stock template without this rule.

Add to Session Startup section in both workspaces:
```markdown
## Memory Rule
**Search memory before acting.** Before answering questions about the user's preferences, past conversations, or decisions — check MEMORY.md and today's memory file first. Never guess at information that might already be written down.
```

### 3. NVIDIA SkillSpector — Security Standard for All Future Skill Installs
OpenClaw's NVIDIA SkillSpector (June 2026): Skill Cards on all ClawHub skills with 64 vulnerability checks (hidden instructions, prompt injection, trigger abuse, memory poisoning, excessive agency, tool poisoning).

**Fleet action:** Before any future skill install on either instance, verify "Clean" Skill Card. Especially critical for Noah: trading skills with Alpaca access are highest-risk (tool poisoning, excessive agency).

---

## New Fleet-Wide Findings (June 7 Morning — Carried)

### 1. memory-core "Dreaming" + `plugins.slots.memory` — Activation Key Missing from Both Configs
The `memory-core` plugin's dreaming feature consolidates daily session memory into long-term memory nightly. Both customers need this.

`plugins.slots.memory: "memory-core"` is the activation key. Without it, the `entries` config alone does not engage memory-core as the active memory engine.

Config template:
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

**Embedding note:** memory-core defaults to OpenAI embeddings. Plan for `OPENAI_API_KEY` in both VPS environments.

### 2. gog-cli = Google Workspace CLI
**Noah** has `skills/gog-cli` + root `gogcli/` — Google Workspace skill. **Josh** does not have gog-cli installed — critical gap for a personal assistant. Install post-upgrade: `openclaw skills install gog`.

### 3. Noah-Specific: LanceDB Pro Memory Plugin
`memory-lancedb-pro` (CortexReach): Hybrid Vector+BM25 retrieval, cross-encoder reranking, multi-scope isolation. For catalyst hunting (specific tickers, SEC filing patterns), better precision than default memory-core.

### 4. Noah-Specific: Claude Opus 4.8 Available
`claude-opus-4-8` now available via AlphaClaw 0.9.17+. Add to `agents.defaults.models` for targeted deep EDGAR/earnings analysis cron jobs.

---

## Common Patterns Across Fleet

### What Both Instances Lack (Shared Gaps)

1. **`plugins.slots.memory: "memory-core"`** — activation key missing in both configs
2. **Persistent memory (MEMORY.md)** — neither has long-term memory file
3. **Functional HEARTBEAT.md** — Josh: empty; Noah: structurally broken (fenced code block)
4. **TOOLS.md populated** — neither has environment-specific tool docs
5. **OpenClaw upgrade** — Josh 79 days behind, Noah 54 days behind npm stable 2026.6.2
6. **SOUL.md personalized** — both use generic stock template
7. **AGENTS.md memory retrieval rule** — neither has "search memory before acting"
8. **Cron automation** — neither has scheduled tasks configured (needed post-upgrade)
9. **Working fallback model** — Josh: dead slug; Noah: NONE
10. **Discord streaming** — both have `streaming: off`; enable `"progress"` post-upgrade
11. **AlphaClaw version** — both behind 0.9.18
12. **BOOTSTRAP.md cleanup** — both still have stale BOOTSTRAP.md

### What Each Instance Uniquely Needs

**Josh (Heather) — Google Workspace Personal Assistant:**
- MEMORY.md creation (JOSH-30 — CRITICAL, Day 79)
- HEARTBEAT.md with email/calendar monitoring (JOSH-31 — HIGH)
- Google Workspace connection (JOSH-44 — CRITICAL, VPS)
- gog-cli skill installation post-upgrade (HIGH)
- iMessage BlueBubbles migration post-upgrade (MEDIUM)
- compaction + contextPruning (30m) config in openclaw.json (MEDIUM)
- Discord security hardening: `dmPolicy: "pairing"` (MEDIUM)
- AGENTS.md emoji contradiction fix (LOW)
- Dead OpenRouter fallback slug fix (MEDIUM)
- Cron morning-digest after Google Workspace connected (MEDIUM, post-upgrade)

**Noah (Market Catalyst Agent) — Stock Catalyst Hunter:**
- contextPruning TTL fix: `"5m"` → `"30m"` (NOAH-99 — CRITICAL, Day 26, one-line change)
- OpenRouter fallback configuration (NOAH-102 — CRITICAL)
- IDENTITY.md + USER.md populated (CRITICAL, Day 54)
- memory-core slots + dreaming config (HIGH)
- MEMORY.md creation (HIGH)
- HEARTBEAT.md repair: remove fenced code block, add Gmail 8-K monitoring (HIGH)
- Trading guardrails in AGENTS.md: paper-only hard rules (HIGH)
- Discord pairing restart fix: AlphaClaw 0.9.17 upgrade (HIGH)
- gmailWatch enabled for real-time SEC filing push (HIGH)
- Alpaca community skill evaluation with SkillSpector vetting (HIGH)
- EDGAR webhook architecture for catalyst monitoring (HIGH)
- gog-cli documented in TOOLS.md (MEDIUM)
- Pre-market + post-close cron automation (MEDIUM, post-upgrade)
- LanceDB Pro memory plugin evaluation (OPPORTUNITY)
- Claude Opus 4.8 as deep analysis model option (OPPORTUNITY)

---

## Fleet Capability Comparison

| Capability | Josh | Noah | Notes |
|---|---|---|---|
| Memory plugin | Not configured | Partial (allow, no slots key) | Both need `plugins.slots.memory: "memory-core"` |
| Memory dreaming | None | None | Both need dreaming config post-upgrade |
| Compaction config | **None** | Configured | Josh: add |
| Context pruning | **None** | **5m (BUG — Day 26)** | NOAH-99: fix to 30m; Josh: add 30m |
| Discord security | **Open** (all servers/users) | Allowlist + pairing | Josh: harden to pairing |
| Google Workspace | Connected (OAuth, no gog-cli) | gog-cli installed | Josh: needs gog-cli skill + connection |
| Fallback model | Dead (bad model slug) | **None (NOAH-102)** | Both effectively have no working fallback |
| Discord run timeout | Default | 30 minutes | Noah: `inboundWorker.runTimeoutMs = 30m` |
| Discord streaming | off | off | Both: enable `"progress"` post-upgrade |
| AlphaClaw version | Unknown | Unknown | Both: update to 0.9.18 |
| iMessage | Paused | N/A | Josh: BlueBubbles path available post-upgrade |
| gog-cli skill | **Not installed** | Installed (undocumented in TOOLS.md) | Josh: install post-upgrade; Noah: document |
| EDGAR/SEC automation | None | Planned (gmailWatch disabled) | Noah: gmailWatch + cron post-upgrade |
| Trading guardrails | N/A | **Missing** | Noah: paper-only rules needed |
| Opus 4.8 | Not configured | Not configured | Noah: add for EDGAR deep analysis cron |
| Memory retrieval rule | Missing from AGENTS.md | Missing from AGENTS.md | Both: add to Session Startup section |
| Gemini search grounding | Unconfigured | N/A | Josh: investigate post-upgrade |
| Web search | **None** | None | Josh: Gemini grounding path; Noah: Brave/Serper |
| Cron automation | None | None | Both: build post-upgrade using jobs-state.json |
| Interrupted tool call recovery | No (pre-2026.6.1) | No (pre-2026.6.1) | Both get on 2026.6.2 upgrade |
| Cron rate-limit retry | No | No | Both get on 2026.6.1+ upgrade + fallback fix |

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
| 2026-06-08 (morning) | 78 days | 54 days | **2026.5.28→6.2** | NOAH-99 Day 25; npm stable clarified |
| **2026-06-09 (morning)** | **79 days** | **54 days** | **2026.6.2** | NOAH-99 Day 26; cron isolation + tool recovery findings; beta at 2026.6.5-beta.5 |

The gap continues widening daily. **NOAH-99 (contextPruning TTL bug) is now Day 26** — the single most urgent action across the fleet.

---

## Fleet Recommendation Priorities (June 9 Morning)

**Apply today (GitHub-only, no VPS, no downtime):**

1. **[NOAH — CRITICAL]** Fix `openclaw.json` contextPruning: `"5m"` → `"30m"` — **Day 26**
2. **[NOAH — CRITICAL]** Add OpenRouter fallback + auth to `openclaw.json` (NOAH-102)
3. **[NOAH — CRITICAL]** Populate `workspace/IDENTITY.md` and `workspace/USER.md`
4. **[NOAH — CRITICAL]** Populate `workspace/TOOLS.md` (Google Workspace, Alpaca, EDGAR)
5. **[NOAH — HIGH]** Add `plugins.slots.memory: "memory-core"` + dreaming config to `openclaw.json`
6. **[NOAH — HIGH]** Fix `workspace/HEARTBEAT.md` (remove fenced code block)
7. **[NOAH — HIGH]** Create `workspace/MEMORY.md` stub
8. **[NOAH — HIGH]** Add paper-trading guardrail to `workspace/AGENTS.md`
9. **[JOSH — CRITICAL]** Create `workspace/MEMORY.md` stub — **Day 79**
10. **[JOSH — HIGH]** Replace `workspace/HEARTBEAT.md` with email/calendar tasks
11. **[JOSH — LOW]** Delete `workspace/BOOTSTRAP.md` — onboarding complete
12. **[BOTH — MEDIUM]** Add memory retrieval rule to `workspace/AGENTS.md`
13. **[JOSH — MEDIUM]** Add `plugins.slots.memory: "memory-core"` + dreaming config
14. **[JOSH — MEDIUM]** Add compaction + contextPruning (30m) to `openclaw.json`
15. **[BOTH — MEDIUM]** Enable `"streaming": "progress"` in Discord channel config

**When VPS access is available:**
- Both: Upgrade OpenClaw → 2026.6.2 (gets cron isolation, tool call recovery, rate-limit retry, Discord safety, config hardening)
- Both: Upgrade AlphaClaw → 0.9.18 (watchdog fix, security hardening, remote MCP support)
- Josh: Connect Google Workspace via AlphaClaw UI (JOSH-44)
- Josh: `openclaw skills install gog` (Google Workspace CLI)
- Josh: `openclaw doctor --fix` (SQLite iMessage migration) → configure BlueBubbles
- Noah: Enable gmailWatch for real-time SEC filing push (AlphaClaw UI)
- Noah: Set `OPENROUTER_API_KEY` env var
- Noah: Evaluate `lacymorrow/openclaw-alpaca-trading-skill` (verify SkillSpector Skill Card first)
- Noah: Design pre-market + post-close cron automation (templates in FINDING-NOAH-59)
- Both: Configure AlphaClaw watchdog crash notification channel

---

*Analysis last updated: 2026-06-09 morning by AlphaClaw Fleet Research daemon.*
