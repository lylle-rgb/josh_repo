# Fleet Research: Cross-Customer Analysis
**Last Updated:** 2026-06-07 morning
**Fleet:** AlphaClaw Apex (2 instances)
**Customers:** Josh (Heather Schwartz) · Noah (Market Catalyst Agent)

---

## Fleet-Wide Platform Status

| Metric | Josh (Heather) | Noah (MCA) | Fleet Target |
|---|---|---|---|
| OpenClaw version | 2026.3.22 | 2026.4.15 | **2026.6.2** |
| Days behind stable | **77** | **53** | 0 |
| Latest stable | **2026.6.2** | **2026.6.2** | Confirmed June 5–6, 2026 |
| AlphaClaw version | Unknown | Unknown | **0.9.18** (released June 1, 2026) |
| Primary model | google/gemini-3-flash-preview | anthropic/claude-sonnet-4-6 | — |
| Fallback model | Dead (claude-3.5-haiku) | **NONE** | CRITICAL for Noah |
| contextPruning TTL | None configured | **5m (BUG — Day 24)** | 30m for both |

**Version note (2026-06-07 morning):** OpenClaw **2026.6.2 remains the latest stable** (confirmed June 6). AlphaClaw **0.9.18** is now the latest — prior analysis referenced 0.9.16 (2 more releases). Both customers should target 2026.6.2 + AlphaClaw 0.9.18.

---

## Workspace Files Gap Analysis

### Josh (lylle-rgb/josh_repo → workspace/)

| File | Status | Days Gap | Notes |
|---|---|---|---|
| SOUL.md | Generic template | 77 | Never personalized for Heather/Josh context |
| AGENTS.md | Functional but contradicts USER.md | 77 | Emoji reactions rule vs USER.md STRICT disable |
| TOOLS.md | **Empty template** | 77 | No tool documentation |
| HEARTBEAT.md | **Empty** | 77 | No proactive monitoring configured |
| MEMORY.md | **Missing** | 77 | Never created — CRITICAL |
| IDENTITY.md | Present (basic) | — | OK |
| USER.md | Present | — | Josh Meyers, LA timezone, Bliss/Oben, emoji STRICT |
| BOOTSTRAP.md | Present (stale) | 77 | Should have been deleted at go-live |
| hooks/ | Present | — | bootstrap-extra-files active |
| memory/ | Present | — | Some daily logs; no MEMORY.md to consolidate into |

**Josh gap summary:** MEMORY.md missing (CRITICAL, Day 77), empty HEARTBEAT.md (HIGH), upgrade 77 days behind (HIGH), Google Workspace not connected (CRITICAL), gog-cli skill not installed (HIGH, new), TOOLS.md empty (MEDIUM), stale SOUL.md (MEDIUM), AGENTS.md emoji contradiction (MEDIUM), Discord security overexposed (MEDIUM), missing compaction config (MEDIUM), memory-core not configured including slots (MEDIUM).

---

### Noah (lylle-rgb/noah--repo → workspace/)

| File | Status | Days Gap | Notes |
|---|---|---|---|
| SOUL.md | Generic template | 53 | Never personalized for trading agent |
| AGENTS.md | Generic — no trading rules | 53 | No paper-only guardrail, no market hours awareness |
| TOOLS.md | **Blank template** | 53 | gog-cli, Alpaca, SEC sources all undocumented |
| HEARTBEAT.md | **Structurally broken** | 53 | Fenced code block wraps content — agent reads as code |
| MEMORY.md | **Missing** | 53 | Never created |
| IDENTITY.md | **Blank template** | 53 | Agent has no name or self-concept |
| USER.md | **Blank template** | 53 | Agent does not know Noah's context |
| BOOTSTRAP.md | Present (stale) | 53 | Should have been deleted at go-live |
| hooks/ | Present | — | bootstrap-extra-files active |
| reports/ | 1 report present | — | ae-target-companies-2026-04-22.md — not in memory |
| skills/gog-cli | Present | — | **Confirmed = Google Workspace CLI (not trading)** |
| gogcli/ | Present | — | **Supporting files for gog-cli skill** |

**Noah gap summary:** contextPruning TTL bug truncating every session (CRITICAL, Day 24), no fallback model (CRITICAL), IDENTITY.md + USER.md blank (CRITICAL), MEMORY.md missing (HIGH), HEARTBEAT.md structurally broken (HIGH), memory-core half-configured + missing slots key (HIGH), upgrade 53 days behind (HIGH).

---

## New Fleet-Wide Findings (June 7 Morning)

### 1. AlphaClaw 0.9.18 — 2 More Releases Since Last Analysis
AlphaClaw latest is **0.9.18** (released June 1, 2026). Prior analysis referenced 0.9.16. Includes watchdog improvements, multi-agent management flows, and per-agent channel bindings.

### 2. memory-core "Dreaming" + `plugins.slots.memory` — Critical Config Detail Confirmed
The `memory-core` plugin includes a **dreaming** feature: a nightly cron that consolidates daily session memory into long-term memory. **Both customers need this.**

**Critical config detail:** `plugins.slots.memory: "memory-core"` is the official activation key. Without it, the `entries` config alone does not engage memory-core as the active memory engine. **Both customers are missing this key.**

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

**Embedding note:** memory-core defaults to OpenAI embeddings. Plan for `OPENAI_API_KEY` (or configure an alternative embedding provider) in both VPS environments.

### 3. gog-cli = Google Workspace CLI (Confirmed)
**Noah** has `skills/gog-cli` + root `gogcli/` — confirmed as Google Workspace skill (Gmail, Calendar, Drive, Contacts). Not trading-related as previously unclear.

**Josh** does not have gog-cli installed — this is a critical gap for a personal assistant bot. Install post-upgrade: `openclaw skills install gog`.

### 4. Gateway Startup Caching (Auto-Win on Upgrade)
Both customers gain automatic /models latency improvement (20s → 5ms) on upgrade to 2026.6.2. No config change required.

### 5. Noah-Specific: LanceDB Pro Memory Plugin
`memory-lancedb-pro` (CortexReach): Hybrid Vector+BM25 retrieval, cross-encoder reranking, multi-scope isolation. For catalyst hunting (specific tickers, SEC filing patterns), better precision than default memory-core.
```
openclaw plugins install CortexReach/memory-lancedb-pro
```

### 6. Noah-Specific: Claude Opus 4.8 Available
`claude-opus-4-8` now available. Add to `agents.defaults.models` for targeted deep EDGAR/earnings analysis cron jobs.

---

## Common Patterns Across Fleet

### What Both Instances Lack (Shared Gaps)

1. **`plugins.slots.memory: "memory-core"`** — activation key missing in both configs
2. **Persistent memory (MEMORY.md)** — neither has long-term memory file
3. **Functional HEARTBEAT.md** — Josh: empty; Noah: structurally broken
4. **TOOLS.md populated** — neither has environment-specific tool docs
5. **OpenClaw upgrade** — 77 days (Josh) and 53 days (Noah) behind. Target: 2026.6.2
6. **SOUL.md personalized** — both use generic stock template
7. **Cron automation** — neither has scheduled tasks configured
8. **Discord streaming** — both have `streaming: off`; enable `"progress"` post-upgrade
9. **AlphaClaw watchdog notifications** — neither configured crash alert channel
10. **AlphaClaw version** — both likely behind 0.9.18

### What Each Instance Uniquely Needs

**Josh (Heather) — Google Workspace Personal Assistant:**
- MEMORY.md creation (JOSH-30 — CRITICAL, Day 77)
- HEARTBEAT.md with email/calendar monitoring (JOSH-31 — HIGH)
- gog-cli skill installation post-upgrade (JOSH-NEW-02 — HIGH)
- compaction + contextPruning (30m) config (JOSH-46 — MEDIUM, GitHub-only)
- Discord security hardening: `dmPolicy: "pairing"` (JOSH-45 — MEDIUM, GitHub-only)
- memory-core slots + dreaming config (JOSH-47/NEW-03 — MEDIUM, GitHub-only)
- iMessage recovery post-upgrade via SQLite migration (JOSH-33)
- AGENTS.md emoji contradiction fix (JOSH-34)
- Dead OpenRouter fallback removal (JOSH-50)

**Noah (Market Catalyst Agent) — Stock Catalyst Hunter:**
- contextPruning TTL fix: `"5m"` → `"30m"` (NOAH-99 — CRITICAL, Day 24, one line)
- OpenRouter fallback configuration (NOAH-102 — CRITICAL)
- IDENTITY.md + USER.md populated (NOAH-91 — CRITICAL, Day 53)
- memory-core slots + dreaming config (NOAH-NEW-02 — HIGH, GitHub-only)
- MEMORY.md creation (NOAH-34 — HIGH)
- HEARTBEAT.md repair: remove fenced code block (NOAH-33 — HIGH)
- Trading guardrails in AGENTS.md: paper-only hard rules (NOAH-60 — HIGH)
- gog-cli documented in TOOLS.md (NOAH-76/NEW-06 — MEDIUM)
- LanceDB Pro memory plugin evaluation (NOAH-NEW-04 — OPPORTUNITY)
- Claude Opus 4.8 as analysis model option (NOAH-NEW-05 — OPPORTUNITY)

---

## Fleet Capability Comparison

| Capability | Josh | Noah | Notes |
|---|---|---|---|
| Memory plugin | Not configured | Partial (allow list, no slots key) | Both need `plugins.slots.memory: "memory-core"` |
| Memory dreaming | None | None | Both need dreaming config post-upgrade |
| Compaction config | **None** | Configured | JOSH-46: add |
| Context pruning | **None** | **5m (BUG — Day 24)** | NOAH-99: fix; Josh: add 30m |
| Discord security | **Open** (all servers/users) | Allowlist + pairing | JOSH-45: harden |
| Google Workspace | Connected (OAuth only) | gog-cli installed | Josh: needs gog-cli skill layer |
| Fallback model | Dead (claude-3.5-haiku) | None | Both effectively have no working fallback |
| Discord run timeout | Default | 30 minutes | Noah: `inboundWorker.runTimeoutMs = 30m` |
| Discord streaming | off | off | Both: enable `"progress"` post-upgrade |
| AlphaClaw version | Unknown | Unknown | Both: update to 0.9.18 |
| iMessage | Paused (broken JSON) | N/A | Fix via SQLite migration post-upgrade |
| gog-cli skill | **Not installed** | Installed (undocumented) | Josh: install post-upgrade; Noah: document |
| EDGAR/SEC automation | None | Planned (cron) | Noah: install EDGAR skill post-upgrade |
| Trading guardrails | N/A | **Missing** | NOAH-60: paper-only rules |
| Opus 4.8 | Not configured | Not configured | Noah: add to models for EDGAR cron |

---

## Fleet Version Gap Trend

| Date | Josh Gap | Noah Gap | Stable Target | Notes |
|---|---|---|---|---|
| 2026-03-24 | 0 days | — | 2026.3.22 | Josh last touched |
| 2026-04-22 | — | 0 days | 2026.4.15 | Noah last touched |
| 2026-05-21 (evening) | ~59 days | ~36 days | 2026.5.18 | First scan |
| 2026-06-03 (morning) | 73 days | 49 days | 2026.5.27 | |
| 2026-06-04 (morning) | 74 days | 51 days | 2026.6.1 | |
| 2026-06-06 (morning) | 76 days | 53 days | 2026.6.2 | Target updated |
| **2026-06-07 (morning)** | **77 days** | **53 days** | **2026.6.2** | AlphaClaw → 0.9.18; memory-core slots config confirmed |

The gap continues widening daily. NOAH-99 (contextPruning TTL bug) is now **Day 24** — the single most urgent action across the fleet.

---

## Fleet Recommendation Priorities (June 7 Morning)

**Apply today (GitHub-only, no VPS, no downtime):**

1. **[NOAH — CRITICAL]** Fix `openclaw.json` contextPruning: `"5m"` → `"30m"` — **Day 24**
2. **[NOAH — CRITICAL]** Add OpenRouter fallback + auth to `openclaw.json`
3. **[NOAH — CRITICAL]** Populate `workspace/IDENTITY.md` and `workspace/USER.md`
4. **[NOAH — HIGH]** Add `plugins.slots.memory: "memory-core"` + dreaming config to `openclaw.json`
5. **[NOAH — HIGH]** Fix `workspace/HEARTBEAT.md` (remove fenced code block)
6. **[NOAH — HIGH]** Create `workspace/MEMORY.md` stub
7. **[JOSH — CRITICAL]** Create `workspace/MEMORY.md` stub — **Day 77**
8. **[JOSH — HIGH]** Replace `workspace/HEARTBEAT.md` with email/calendar tasks
9. **[JOSH — MEDIUM]** Add `plugins.slots.memory: "memory-core"` + dreaming config to `openclaw.json`
10. **[JOSH — MEDIUM]** Add compaction + contextPruning (30m) to `openclaw.json`
11. **[BOTH — MEDIUM]** Enable `"streaming": "progress"` in Discord channel config
12. **[BOTH — MEDIUM]** Personalize SOUL.md, AGENTS.md, TOOLS.md per customer

**When VPS access is available:**
- Both: `openclaw upgrade` to 2026.6.2
- Both: Update AlphaClaw to 0.9.18
- Josh: `openclaw doctor --fix` (SQLite iMessage migration)
- Josh: Connect Google Workspace + `openclaw skills install gog`
- Noah: Set `OPENROUTER_API_KEY`
- Both: Configure AlphaClaw watchdog crash notification channel

---

*Analysis last updated: 2026-06-07 morning by AlphaClaw Fleet Research daemon.*
