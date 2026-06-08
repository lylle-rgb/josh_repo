# Fleet Research: Cross-Customer Analysis
**Last Updated:** 2026-06-08 morning
**Fleet:** AlphaClaw Apex (2 instances)
**Customers:** Josh (Heather Schwartz) · Noah (Market Catalyst Agent)

---

## Fleet-Wide Platform Status

| Metric | Josh (Heather) | Noah (MCA) | Fleet Target |
|---|---|---|---|
| OpenClaw version | 2026.3.22 | 2026.4.15 | **2026.5.28** (npm stable) |
| Days behind npm stable | **78** | **47** | 0 |
| npm stable | **2026.5.28** | **2026.5.28** | Confirmed June 8, 2026 |
| GitHub HEAD release | 2026.6.2 | 2026.6.2 | Not yet in npm channel |
| AlphaClaw version | Unknown | Unknown | **0.9.18** (released June 1, 2026) |
| Primary model | google/gemini-3-flash-preview | anthropic/claude-sonnet-4-6 | — |
| Fallback model | Dead (claude-3.5-haiku slug) | **NONE** | CRITICAL for Noah |
| contextPruning TTL | None configured | **5m (BUG — Day 25)** | 30m for both |

**Version note (2026-06-08 morning):** npm stable is **2026.5.28** — corrected from prior scans that referenced 2026.6.2. The `openclaw update` command installs from npm → lands on 2026.5.28. GitHub HEAD is at 2026.6.2 but not yet promoted to npm stable. Both customers should target **2026.5.28** via `openclaw update`.

---

## Workspace Files Gap Analysis

### Josh (lylle-rgb/josh_repo → workspace/)

| File | Status | Days Gap | Notes |
|---|---|---|---|
| SOUL.md | Generic template | 78 | Never personalized for Heather/Josh context |
| AGENTS.md | Functional but contradicts USER.md | 78 | Emoji reactions rule vs USER.md STRICT disable; missing memory retrieval rule |
| TOOLS.md | **Empty template** | 78 | No tool documentation |
| HEARTBEAT.md | **Empty** | 78 | No proactive monitoring configured |
| MEMORY.md | **Missing** | 78 | Never created — CRITICAL |
| IDENTITY.md | Present (basic) | — | OK |
| USER.md | Present | — | Josh Meyers, LA timezone, Bliss/Oben, emoji STRICT |
| BOOTSTRAP.md | Present (stale) | 78 | Should have been deleted at go-live |
| hooks/ | Present | — | bootstrap-extra-files active |
| memory/ | Present | — | Some daily logs; no MEMORY.md to consolidate into |

**Josh gap summary:** MEMORY.md missing (CRITICAL, Day 78), empty HEARTBEAT.md (HIGH), upgrade 78 days behind (HIGH), gog-cli skill not installed (HIGH), TOOLS.md empty (MEDIUM), stale SOUL.md (MEDIUM), AGENTS.md emoji contradiction + missing memory retrieval rule (MEDIUM), Discord security overexposed (MEDIUM), missing compaction config (MEDIUM), memory-core not configured (MEDIUM).

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
| reports/ | 1 report present | — | ae-target-companies-2026-04-22.md — not reflected in memory |
| skills/gog-cli | Present | — | Google Workspace CLI (Gmail, Calendar, Drive) |
| gogcli/ | Present | — | Supporting state files for gog-cli skill |

**Noah gap summary:** contextPruning TTL bug truncating every session (CRITICAL, **Day 25**), no fallback model (CRITICAL), IDENTITY.md + USER.md blank (CRITICAL), MEMORY.md missing (HIGH), HEARTBEAT.md structurally broken (HIGH), memory-core half-configured + missing slots key (HIGH), upgrade 47 days behind (HIGH), missing memory retrieval rule (MEDIUM).

---

## New Fleet-Wide Findings (June 8 Morning)

### 1. npm Stable Clarification — 2026.5.28 is the Correct Upgrade Target
Prior scans referenced 2026.6.2 as the fleet-wide upgrade target. **Correction: npm stable is 2026.5.28.** The 2026.6.2 release exists on GitHub but has not been promoted to npm. `openclaw update` installs 2026.5.28.

Key improvements in 2026.5.28:
- Group prompt text kept out of system prompt (security)
- Normalized hostnames; blocked unsafe command wrappers
- Rejected no-auth Tailscale exposure (security)

Key improvements in 2026.6.1 (GitHub, not yet npm):
- Agents and CLI-backed runtimes recover cleanly from interrupted tool calls, stale session bindings, compaction handoffs, and media delivery retries

**Fleet action:** Both customers run `openclaw update` → lands on 2026.5.28.

### 2. Mandatory Memory Retrieval Rule — Missing from Both AGENTS.md Files
OpenClaw 2026 best practices: add "search memory before acting" to AGENTS.md. Without this rule, agents skip memory files and guess at context that's already documented — causing inconsistency.

Both AGENTS.md files use the stock template which does not include this rule. Add to the Session Startup section in both workspaces:
```markdown
## Memory Rule
**Search memory before acting.** Before answering questions about the user's preferences, past conversations, or decisions — check MEMORY.md and today's memory file first. Never guess at information that might be written down.
```

### 3. NVIDIA SkillSpector — Security Standard for All Future Skill Installs (June 2026)
OpenClaw's NVIDIA SkillSpector collaboration (June 2026) adds Skill Cards to all ClawHub skills: 64 vulnerability checks across 16 categories including hidden instructions, prompt injection, trigger abuse, memory poisoning, excessive agency, and tool poisoning.

**Fleet action:** Before any future skill install on either instance, verify the skill has a "Clean" Skill Card on ClawHub. Especially critical for Noah: trading skills with Alpaca access are the highest-risk category (tool poisoning, excessive agency).

### 4. `/context list` Diagnostic Command Available
Running `/context list` inside any OpenClaw session shows every file loaded into working context. Useful first diagnostic for memory and config issues. The majority of "memory isn't sticking" reports are diagnosed this way.

---

## New Fleet-Wide Findings (June 7 Morning)

### 1. AlphaClaw 0.9.18 Released
AlphaClaw latest is **0.9.18** (released June 1, 2026). Includes watchdog improvements, multi-agent management flows, and per-agent channel bindings. Both customers are behind.

### 2. memory-core "Dreaming" + `plugins.slots.memory` — Activation Key Missing from Both Configs
The `memory-core` plugin includes a **dreaming** feature: a nightly cron that consolidates daily session memory into long-term memory. **Both customers need this.**

`plugins.slots.memory: "memory-core"` is the activation key. Without it, the `entries` config alone does not engage memory-core as the active memory engine.

Config template (adjust model per customer):
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

**Embedding note:** memory-core defaults to OpenAI embeddings. Plan for `OPENAI_API_KEY` (or configure alternative embedding provider) in both VPS environments.

### 3. gog-cli = Google Workspace CLI (Confirmed)
**Noah** has `skills/gog-cli` + root `gogcli/` — Google Workspace skill. **Josh** does not have gog-cli installed — this is a critical gap for a personal assistant. Install post-upgrade: `openclaw skills install gog`.

### 4. Gateway Startup Caching (Auto-Win on Upgrade)
Both customers gain automatic /models latency improvement (~20s → 5ms) on upgrade to 2026.5.28. No config change required.

### 5. Noah-Specific: LanceDB Pro Memory Plugin
`memory-lancedb-pro` (CortexReach): Hybrid Vector+BM25 retrieval, cross-encoder reranking, multi-scope isolation. For catalyst hunting (specific tickers, SEC filing patterns), better precision than default memory-core.
```
openclaw plugins install CortexReach/memory-lancedb-pro
```

### 6. Noah-Specific: Claude Opus 4.8 Available
`claude-opus-4-8` now available. Add to `agents.defaults.models` for targeted deep EDGAR/earnings analysis cron jobs.

---

## Customer-Specific New Findings (June 8 Morning)

### Josh (Heather) — iMessage BlueBubbles Private API Path (April 2026)
BlueBubbles Private API integration shipped in April 2026. The AppleScript bridge Heather was using before iMessage was paused is fragile and likely the cause of the pause. After upgrading to 2026.5.28 + `openclaw doctor --fix` (SQLite migration), configure iMessage via BlueBubbles rather than AppleScript. This is now the recommended iMessage path for all OpenClaw instances.

### Josh (Heather) — Gemini Native Search Grounding
Gemini 2.5 Flash and Gemini 3 Flash Preview both support built-in Google Search grounding — live search results with citations, no additional API key. Heather has no web search configured. Investigate whether OpenClaw's Google provider plugin exposes this via `googleSearchGrounding: true` in model config (post-upgrade).

### Noah (Market Catalyst) — Community Alpaca Skill Available
`lacymorrow/openclaw-alpaca-trading-skill` on GitHub covers stocks, ETFs, options, and crypto via Alpaca's API. **Verify Skill Card (SkillSpector verdict: Clean) before installing** — trading skills with financial API access are the highest-risk SkillSpector category. Configure for paper trading endpoint only: `https://paper-api.alpaca.markets`.

### Noah (Market Catalyst) — Real-Time EDGAR Webhooks
SEC EDGAR streaming notification APIs provide sub-minute latency for filing events vs. heartbeat polling. When building the cron/heartbeat layer for market monitoring, architect around EDGAR webhooks as the primary catalyst inbound — not polling.

---

## Common Patterns Across Fleet

### What Both Instances Lack (Shared Gaps)

1. **`plugins.slots.memory: "memory-core"`** — activation key missing in both configs
2. **Persistent memory (MEMORY.md)** — neither has long-term memory file
3. **Functional HEARTBEAT.md** — Josh: empty; Noah: structurally broken (fenced code block)
4. **TOOLS.md populated** — neither has environment-specific tool docs
5. **OpenClaw upgrade** — Josh 78 days behind, Noah 47 days behind npm stable
6. **SOUL.md personalized** — both use generic stock template
7. **AGENTS.md memory retrieval rule** — neither has "search memory before acting"
8. **Cron automation** — neither has scheduled tasks configured
9. **Discord streaming** — both have `streaming: off`; enable `"progress"` post-upgrade
10. **AlphaClaw watchdog notifications** — neither configured crash alert channel
11. **AlphaClaw version** — both behind 0.9.18
12. **BOOTSTRAP.md cleanup** — both still have stale BOOTSTRAP.md

### What Each Instance Uniquely Needs

**Josh (Heather) — Google Workspace Personal Assistant:**
- MEMORY.md creation (JOSH-30 — CRITICAL, Day 78)
- HEARTBEAT.md with email/calendar monitoring (JOSH-31 — HIGH)
- gog-cli skill installation post-upgrade (HIGH)
- iMessage BlueBubbles migration post-upgrade (MEDIUM)
- compaction + contextPruning (30m) config in openclaw.json (MEDIUM, GitHub-only)
- Discord security hardening: `dmPolicy: "pairing"` (MEDIUM, GitHub-only)
- memory-core slots + dreaming config (MEDIUM, GitHub-only)
- AGENTS.md emoji contradiction fix
- Dead OpenRouter fallback removal
- Gemini native search grounding investigation

**Noah (Market Catalyst Agent) — Stock Catalyst Hunter:**
- contextPruning TTL fix: `"5m"` → `"30m"` (NOAH-99 — CRITICAL, **Day 25**, one-line change)
- OpenRouter fallback configuration (CRITICAL)
- IDENTITY.md + USER.md populated (CRITICAL, Day 54)
- memory-core slots + dreaming config (HIGH, GitHub-only)
- MEMORY.md creation (HIGH)
- HEARTBEAT.md repair: remove fenced code block (HIGH)
- Trading guardrails in AGENTS.md: paper-only hard rules (HIGH)
- Alpaca community skill evaluation with SkillSpector vetting (HIGH)
- EDGAR webhook architecture for catalyst monitoring (HIGH)
- gog-cli documented in TOOLS.md (MEDIUM)
- LanceDB Pro memory plugin evaluation (OPPORTUNITY)
- Claude Opus 4.8 as deep analysis model option (OPPORTUNITY)

---

## Fleet Capability Comparison

| Capability | Josh | Noah | Notes |
|---|---|---|---|
| Memory plugin | Not configured | Partial (allow list, no slots key) | Both need `plugins.slots.memory: "memory-core"` |
| Memory dreaming | None | None | Both need dreaming config post-upgrade |
| Compaction config | **None** | Configured | Josh: add |
| Context pruning | **None** | **5m (BUG — Day 25)** | NOAH-99: fix to 30m; Josh: add 30m |
| Discord security | **Open** (all servers/users) | Allowlist + pairing | Josh: harden to pairing |
| Google Workspace | Connected (OAuth only) | gog-cli installed | Josh: needs gog-cli skill layer |
| Fallback model | Dead (bad model slug) | None | Both effectively have no working fallback |
| Discord run timeout | Default | 30 minutes | Noah: `inboundWorker.runTimeoutMs = 30m` |
| Discord streaming | off | off | Both: enable `"progress"` post-upgrade |
| AlphaClaw version | Unknown | Unknown | Both: update to 0.9.18 |
| iMessage | Paused | N/A | Josh: BlueBubbles path available post-upgrade |
| gog-cli skill | **Not installed** | Installed (undocumented) | Josh: install post-upgrade; Noah: document in TOOLS.md |
| EDGAR/SEC automation | None | Planned | Noah: use real-time EDGAR webhooks, not polling |
| Trading guardrails | N/A | **Missing** | Noah: paper-only rules needed |
| Opus 4.8 | Not configured | Not configured | Noah: add to models for EDGAR cron |
| Memory retrieval rule | Missing from AGENTS.md | Missing from AGENTS.md | Both: add to Session Startup section |
| Gemini search grounding | Unconfigured | N/A | Josh: investigate post-upgrade |
| Web search | **None** | None | Josh: Gemini grounding path; Noah: Brave/Serper |

---

## Fleet Version Gap Trend

| Date | Josh Gap | Noah Gap | npm Stable | Notes |
|---|---|---|---|---|
| 2026-03-24 | 0 days | — | 2026.3.22 | Josh last touched |
| 2026-04-22 | — | 0 days | 2026.4.15 | Noah last touched |
| 2026-05-21 (evening) | ~59 days | ~36 days | 2026.5.18 | First scan |
| 2026-06-03 (morning) | 73 days | 49 days | 2026.5.27 | |
| 2026-06-04 (morning) | 74 days | 51 days | 2026.6.1 | |
| 2026-06-06 (morning) | 76 days | 53 days | 2026.6.2 | GitHub HEAD target (pre-correction) |
| 2026-06-07 (morning) | 77 days | 53 days | 2026.6.2 | AlphaClaw → 0.9.18; memory-core slots confirmed |
| **2026-06-08 (morning)** | **78 days** | **54 days** | **2026.5.28 (npm)** | npm stable clarified; NOAH-99 Day 25; SkillSpector + BlueBubbles + Alpaca skill + EDGAR webhooks |

The gap continues widening daily. **NOAH-99 (contextPruning TTL bug) is now Day 25** — the single most urgent action across the fleet.

---

## Fleet Recommendation Priorities (June 8 Morning)

**Apply today (GitHub-only, no VPS, no downtime):**

1. **[NOAH — CRITICAL]** Fix `openclaw.json` contextPruning: `"5m"` → `"30m"` — **Day 25**
2. **[NOAH — CRITICAL]** Add OpenRouter fallback + auth to `openclaw.json`
3. **[NOAH — CRITICAL]** Populate `workspace/IDENTITY.md` and `workspace/USER.md`
4. **[NOAH — HIGH]** Add `plugins.slots.memory: "memory-core"` + dreaming config to `openclaw.json`
5. **[NOAH — HIGH]** Fix `workspace/HEARTBEAT.md` (remove fenced code block)
6. **[NOAH — HIGH]** Create `workspace/MEMORY.md` stub
7. **[JOSH — CRITICAL]** Create `workspace/MEMORY.md` stub — **Day 78**
8. **[JOSH — HIGH]** Replace `workspace/HEARTBEAT.md` with email/calendar tasks
9. **[BOTH — MEDIUM]** Add memory retrieval rule to `workspace/AGENTS.md`
10. **[JOSH — MEDIUM]** Add `plugins.slots.memory: "memory-core"` + dreaming config to `openclaw.json`
11. **[JOSH — MEDIUM]** Add compaction + contextPruning (30m) to `openclaw.json`
12. **[BOTH — MEDIUM]** Enable `"streaming": "progress"` in Discord channel config
13. **[BOTH — MEDIUM]** Personalize SOUL.md, AGENTS.md, TOOLS.md per customer

**When VPS access is available:**
- Both: `openclaw update` → 2026.5.28 (npm stable)
- Both: Update AlphaClaw to 0.9.18
- Josh: `openclaw doctor --fix` (SQLite iMessage migration) → configure BlueBubbles
- Josh: `openclaw skills install gog` (Google Workspace CLI)
- Josh: Investigate Gemini native search grounding post-upgrade
- Noah: Set `OPENROUTER_API_KEY`
- Noah: Evaluate `lacymorrow/openclaw-alpaca-trading-skill` (verify Skill Card first)
- Noah: Design EDGAR webhook architecture for catalyst monitoring
- Both: Configure AlphaClaw watchdog crash notification channel

---

*Analysis last updated: 2026-06-08 morning by AlphaClaw Fleet Research daemon.*
