# Fleet Research Findings — Josh / Heather Schwartz
## Morning Scan — June 25, 2026

**Researcher:** AlphaClaw Fleet Agent
**Scan time:** Morning, June 25, 2026 (PDT)
**Previous scan:** June 25, 2026 (evening — F50/F51/F52)

---

## Platform Status

| Item | Current | Target | Gap |
|------|---------|--------|-----|
| OpenClaw | 2026.3.22 | **2026.6.10-stable** | Day 96 outdated, Day 2 upgrade window |
| Primary model | google/gemini-3-flash-preview | google/gemini-3.5-flash | Migration ready (Browse tab, no upgrade needed) |
| Fallback 2 | openrouter/anthropic/claude-3.5-haiku | openrouter/anthropic/claude-haiku-4-5 | After reaching 2026.6.10 |
| Google Workspace OAuth | ❌ Not connected | Connected | Day 96 — 4 days to Day 100 milestone |
| iMessage monitoring | ❌ Paused | Active | Day 61 — auto-fix via upgrade SQLite migration |
| Heartbeat cron | ❌ Not deployed | Active | Day 11 null — bundle with upgrade |
| Discord streaming | Off | Optional enable | Post-upgrade to 2026.6.10 |

---

## New Findings This Scan

### F53 — 2026.6.10-stable Day 2: Green Light to Execute Upgrade

**Priority: HIGH — Window Open, No Regressions Reported**

2026.6.10-stable was released June 24 at 03:01 UTC. This morning is Day 2 of the stable window with no new regressions found in community channels or GitHub issues.

**Why now:**
- Day 2 of stable with clean community signal is the ideal execution window — early enough to not fall further behind, late enough to clear any day-0 regressions
- Josh is now 96 days behind on OpenClaw — the gap is widening every day
- The 2026.6.10 release directly fixes issues already tracked in Josh's open items: cron delivery state (F27), channel switch stale state, faster conversational responses, more reliable fallback routing
- **Day 100 of Google Workspace disconnection arrives June 29** — connecting OAuth is faster if OpenClaw is on a healthy, current version first

**Staged upgrade path (VPS — SSH required):**
```
2026.3.22 → 2026.5.27 → 2026.6.2 → 2026.6.5 → 2026.6.6 → 2026.6.10
```
Run `openclaw update` at each step. Test Discord response and memory search after each hop.

**Skip 2026.6.8 and 2026.6.9** — both have confirmed critical regressions (memory store relocation, email config corruption, isolated cron failures). Jump directly from 2026.6.6 → 2026.6.10.

**Smoke test checklist for 2026.6.10 (after final hop):**
- [ ] Short Discord message to Heather → confirm fast response
- [ ] Longer agent task ("search for X and summarize") → confirm normal mode executes
- [ ] Check `openclaw doctor` output — no errors
- [ ] Verify memory search still works (was broken in 2026.6.8)
- [ ] Confirm Discord channel switch doesn't carry stale context
- [ ] If heartbeat cron is deployed: confirm cron delivery reaches Discord
- [ ] Run `openclaw skill list` — confirm no unexpected skills installed (ClawHavoc risk)

**Pre-upgrade config changes to bundle (can do NOW via Browse tab — no upgrade needed):**
```json
"model": {
  "primary": "google/gemini-3.5-flash",
  "fallbacks": [
    "openrouter/anthropic/claude-haiku-4-5",
    "openrouter/google/gemini-3.5-flash"
  ]
}
```
Migrate model first so Heather isn't on a preview model during the upgrade. See F48.

**Post-upgrade actions to bundle:**
- Set `userTimezone: America/Los_Angeles` (F28)
- Enable `memory-core` plugin + configure Dreaming (F22/F24)
- Add compaction/memoryFlush config (F4)
- Deploy heartbeat cron (F27)
- Update fallback 2 to `openrouter/anthropic/claude-haiku-4-5` (if not done pre-upgrade)

**Risk level:** LOW for the upgrade itself. Staged path is well-tested. Main risk is skipping a step — go one version at a time.

---

### F54 — 2026.6.10 Feature Digest: What Changes for Heather

**Priority: INFO — Unlocks on Upgrade**

Consolidated detail on what 2026.6.10 means specifically for Josh's personal assistant use case:

**1. Auto Fast Mode for Short Turns (no config needed)**
OpenClaw automatically enables fast mode for short conversational turns and returns to standard mode for longer agent runs. For Josh's casual Discord messages to Heather — "what's on my calendar?" or "remind me in 30 min" — responses will be noticeably faster without any quality loss on complex tasks. Bounded fallback behavior means fast-mode failures don't cascade.

**2. More Reliable Model Routing**
Zai model synthesis, GLM overload failover, and native reasoning-level selection now follow the active model catalog more consistently. For Heather's fallback chain (Gemini → Claude Haiku → OpenRouter Gemini), this means failover happens cleanly when a provider has an outage instead of silently stalling.

**3. Channel State Reset on Switches**
Channel switches reset stale origin fields — eliminates a class of out-of-order or echoed reply bugs. Josh's Discord DM sessions won't carry ghost context from previous channel interactions.

**4. Cron Delivery Awareness Persists Through Restarts**
Cron delivery awareness now stays attached to the target session. This is critical for Josh: when the heartbeat cron is deployed (F27), scheduled checks will reliably deliver to Josh's Discord channel even after gateway restarts — no more silent cron drops.

**5. Discord Auto-Thread Titles (opt-in)**
Post-upgrade, Discord auto-thread titles can be enabled: AI-generated, 60s timeout, 4,096-token reasoning budget. Makes longer Heather → Josh conversations easier to navigate in Discord. Enable via `channels.discord` config post-upgrade.

**6. Discord Streaming (opt-in)**
Streaming is now enableable post-upgrade (TOOLS.md already notes `streaming: off`, set in openclaw.json). Options: `partial` (edits preview message as tokens arrive) or `block` (draft-sized chunks). Recommend testing `partial` post-upgrade for a more responsive feel on longer Heather responses.

**Risk level:** N/A — these are post-upgrade automatic behaviors, not new risks.

---

### F55 — Noah Scope Broken: Day 16 — Escalation

**Priority: FLEET OPS — ESCALATING**

**Current state:** `lylle-rgb/noah--repo` continues to return 404. Confirmed again this morning scan. The correct repo appears to be `lylle-rgb/Noahrepo2` (last git sync March 2026, ~109 days ago) — but it remains outside this session's authorized scope.

**Why this is escalating:**
- Day 16 of zero fleet coverage for the highest-risk customer in the fleet
- Noah (Market Catalyst Agent) has: Alpaca paper trading API access, SEC EDGAR monitoring, live market data — all external APIs that could be exploited via stale skills
- The ClawHavoc attack specifically targeted trading and financial skill packages
- Last known OpenClaw version: estimated early 2026.3.x (no git sync since March)
- Skills installed on Noah are completely unknown — could include compromised packages from before SkillSpector scanning became standard
- Noah's Alpaca integration may be using old v1 MCP tools (43 tools vs 65 in v2)

**New this morning — SEC Filing Watcher skill now on marketplace:**
`sec-filing-watcher` is available on the skills marketplace (lobehub.com/skills/openclaw-skills-sec-filing-watcher). Directly relevant to Noah's use case:
- Auto-monitors SEC EDGAR for new filings on a configurable ticker watchlist
- Filters by form type (8-K, 10-Q, S-1, etc.)
- Delivers alerts via Discord with filing summaries
- JSON-configured watchlist with environment-driven setup
- Seeds initial state to avoid notification spam on first run

This skill would meaningfully upgrade Noah's catalyst detection pipeline once scope is restored and Noah is audited. Noah likely lacks this (pre-dates SkillSpector standard).

**Action required (fleet admin):**
> Fix session scope: replace `lylle-rgb/noah--repo` (404) with `lylle-rgb/Noahrepo2`
> On next scan after scope fix: run full Noah audit — version, skills, model config, Alpaca integration, Discord security

**Risk level:** HIGH (inaction risk — not a change risk). Every day without coverage is exposure.

---

## Day Count Updates (June 25 Morning)

*Note: Morning and evening scans on the same calendar day share the same day count.*

| Metric | Yesterday (June 24) | Today (June 25) |
|--------|---------------------|------------------|
| Google Workspace OAuth disconnected | Day 95 | **Day 96** |
| OpenClaw outdated (2026.3.22) | Day 95 | **Day 96** |
| iMessage monitoring paused | Day 60 | **Day 61** |
| Heartbeat-state.json all-null | Day 10 | **Day 11** |
| 2026.6.10-stable window open | Day 1 | **Day 2** |
| Noah scope broken | Day 15 | **Day 16** |

> ⚠️ **Day 100 milestone:** Google Workspace OAuth reaches Day 100 on June 29 — 4 days away. Email and calendar have been inaccessible for over 3 months. Worth surfacing to Josh directly at next main session.

---

## Open Item Status (June 25 Morning)

| Finding | Priority | Status |
|---------|----------|--------|
| **F2. Google Workspace OAuth (Day 96 — Day 100 in 4 days)** | **CRITICAL** | ⏳ Connect at AlphaClaw UI General tab |
| **F53. Upgrade to 2026.6.10 (Day 2 — green light)** | **HIGH** | ⏳ Execute staged upgrade via SSH |
| F27. Heartbeat cron not deployed (Day 11) | HIGH | ⏳ Bundle with upgrade |
| F22/24. Dreaming not enabled | HIGH | ⏳ Bundle with upgrade |
| F4. compaction/memoryFlush missing | HIGH | ⏳ Bundle with upgrade |
| F48. Migrate primary → gemini-3.5-flash | MEDIUM-HIGH | ⏳ Browse tab NOW (pre-upgrade recommended) |
| F28. userTimezone not set | MEDIUM-HIGH | ⏳ Bundle with upgrade |
| F30. BRAVE_API_KEY not set | MEDIUM-HIGH | ⏳ AlphaClaw Envars tab anytime |
| F20. Discord security open | MEDIUM-HIGH | ⏳ Post-upgrade |
| **F55. Noah scope broken (Day 16 — escalating)** | **FLEET OPS** | ⏳ Fix scope: noah--repo → Noahrepo2 |
| F50. 2026.6.11-beta.1 (per-DM overrides, file workflows) | INFO | 🔬 Monitor — do not install |
| F52. Gemini primary model safe | INFO | ✅ Primary unaffected — migration still recommended |
| F54. 2026.6.10 feature digest (fast mode, routing, cron state) | INFO | ✅ Documents post-upgrade improvements |
| F39. Discord Components V2 | INFO | Post-upgrade capability |
| F31. Same-provider fallback gap | MEDIUM | ⏳ Bundle with F48 model migration |

---

*Sources: [OpenClaw GitHub Releases](https://github.com/openclaw/openclaw/releases) · [OpenClaw 2026.6.10 Release Notes](https://releasebot.io/updates/openclaw) · [OpenClaw Changelog](https://www.remoteopenclaw.com/blog/openclaw-changelog) · [SEC Filing Watcher Skill](https://lobehub.com/skills/openclaw-skills-sec-filing-watcher) · [AlphaClaw](https://alphaclaw.md/) · [Google Gemini Deprecations](https://ai.google.dev/gemini-api/docs/deprecations) · [OpenClaw Discord Docs](https://docs.openclaw.ai/channels/discord)*
