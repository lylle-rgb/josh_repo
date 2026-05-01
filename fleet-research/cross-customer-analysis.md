# Cross-Customer Fleet Analysis

> Original scan: 2026-04-21 | Updated: 2026-05-01 (morning) | Agent: AlphaClaw Fleet Research
> Fleet: Josh (Heather Schwartz) • Ricky (Pedro/Mikuna) • Noah (Market Catalyst Agent)

---

## UPDATE — MORNING SCAN 2026-05-01

### Version Status Update

Latest stable OpenClaw is now `2026.4.29` (released April 30, 2026). Previous latest referenced in this document (`2026.4.14`) is 15 patch versions stale.

| Customer | Bot Name | OpenClaw Version | Latest Stable | Behind By |
|----------|----------|-----------------|--------------|----------|
| Josh | Heather Schwartz | 2026.3.22 | 2026.4.29 | **38 days, 37+ patch versions — longest gap on fleet** |
| Noah | Market Catalyst Agent | 2026.4.15 | 2026.4.29 | 16 patch versions |
| Ricky | Pedro | Not scanned this cycle | 2026.4.29 | Unknown |

---

### Fleet-Wide New Capability: Memory People-Aware Wiki (2026.4.29)

OpenClaw 2026.4.29 ships a major memory architecture upgrade relevant to every instance on the fleet. Memory is now a "people-aware wiki with provenance views" — organizing stored knowledge around people and entities rather than flat text chunks.

**Impact by use case:**
- **Josh (personal assistant):** Highest fleet impact. Heather would build a contact wiki for every person Josh interacts with via iMessage, email, and calendar. Provenance tracking means context like "Josh mentioned the Oben HiFi board meeting last Tuesday" persists and is retrievable.
- **Noah (career research):** Critical. The agent tracks companies, founders, investors, and hiring managers across research sessions. The people-aware wiki makes every AE target company a persistent, structured record that accumulates context across sessions — directly addresses the finding that the agent resets all research context on every restart.
- **Ricky (brand ops):** High impact. Pedro would retain structured knowledge about team members, suppliers, platform contacts, and recurring partners.

**Install sequence (same for all):** `memory-lancedb` plugin → update to OpenClaw 2026.4.29 → configure Active Memory filters per session type.

---

### Noah Use Case Clarification: Career Research, Not Stock Trading

Evening scan 2026-05-01 (Noah's findings.md, E2) confirmed that Noah's agent is performing **career research and job search assistance**, not stock catalyst hunting. The agent produced a detailed 21KB AE target companies report on April 22 — ranking 10 startups in construction tech, PropTech, and field service for Noah Katz's next AE role after PermitFlow.

**Fleet implications:**
- The "Market Catalyst Agent" label in the fleet overview is misleading; the actual use case is job search intelligence
- Noah's `gog-cli` skill (Google Workspace CLI) makes sense for this use case: email hiring managers via Gmail, track interview calendar events, manage research docs in Drive
- The weekly cron recommendation in Noah's findings.md should target career research delivery (weekly AE target update) rather than market close debrief

---

### gog-cli Skill (Noah): Audited Clean ✓ — Google Workspace Access Confirmed

Noah's `skills/gog-cli` skill has been audited. It is a legitimate Google Workspace CLI providing access to Gmail, Calendar, Drive, Sheets, Docs, Tasks, Contacts, and Meet — connected to `Ngkatz.ai@gmail.com`. No security concerns. The skill is well-structured, comprehensive, and appropriate for the career research use case.

**Cross-fleet gap:** Josh also uses Google (google:default auth, Google Calendar, Gmail) but does not have the gog-cli skill installed. Heather currently accesses Google services via the native API integration. Adding gog-cli would give Heather scripted, batch-capable access to Gmail search, calendar management, Drive, and Contacts — significantly expanding what she can automate on behalf of Josh.

**Recommendation:** Consider installing gog-cli on Josh's instance and connecting it to Josh's Google account. The skill has zero security concerns based on Noah's audit and would unlock capabilities like batch email operations, Drive file management, and Sheets integration for Josh's Bliss Lifestyle / Oben HiFi operations.

---

### Opt-In Follow-Up Commitments (2026.4.29) — Fleet-Wide Opportunity

OpenClaw 2026.4.29 ships opt-in follow-up commitments for heartbeat-delivered reminders. Agents can register time-bound commitments that persist across session restarts.

- **Josh:** Directly addresses E9/E14 — Heather is running proactive checks without any documented commitment tracking. This feature formalizes her behavior.
- **Noah:** Directly addresses E3 — the agent set a self-deadline of April 29 for a weekly report that was missed. With persistent commitments, this would have fired a heartbeat reminder.
- **Ricky:** Low hanging fruit — Pedro's Shopify cron jobs could register explicit follow-through commitments for monthly report deadlines.

---

### Updated Fleet-Wide Action Checklist

- [ ] Update all instances to OpenClaw 2026.4.29
- [ ] Install `memory-lancedb` on all instances (people-aware wiki unlocked in 2026.4.29)
- [ ] Create `workspace/MEMORY.md` on all repos (pre-seeded with known context)
- [ ] Fill in `workspace/TOOLS.md` on all repos (environment-specific notes)
- [ ] Josh: Enable Discord streaming + add cron.json + populate HEARTBEAT.md
- [ ] Josh: Investigate iMessage monitoring pause (E10)
- [ ] Josh: Fix emoji contradiction in SOUL.md (E2)
- [ ] Josh: Consider installing gog-cli for expanded Google Workspace automation
- [ ] Noah: Fill USER.md + IDENTITY.md + create MEMORY.md (critical — all blank)
- [ ] Noah: Trigger overdue AE target companies report update
- [ ] Noah: Add cron.json for weekly career research report delivery
- [ ] Noah: Configure HEARTBEAT.md with follow-up commitments (E11)
- [ ] Noah: Increase contextPruning TTL from 5m to 15m (E7)
- [ ] Ricky: Audit + update (not scanned this cycle)

---

## ORIGINAL ANALYSIS — 2026-04-21

---

## Fleet Version Status

| Customer | Bot Name | OpenClaw Version | Latest Stable | Behind By |
|----------|----------|-----------------|--------------|----------|
| Josh | Heather Schwartz | 2026.3.22 | 2026.4.14 | ~3 weeks |
| Ricky | Pedro | **2026.3.13** | 2026.4.14 | **~5 weeks (most outdated)** |
| Noah | Market Catalyst Agent | 2026.4.9 | 2026.4.14 | ~2 weeks |

**Fleet action:** All 3 instances need an update. Ricky is priority #1. The jump from 2026.3.x to 2026.4.x is the most impactful — it includes cron reliability fixes and the Model Auth Status card.

---

## Workspace File Inventory

| File | Josh | Ricky | Noah | Notes |
|------|------|-------|------|-------|
| SOUL.md | ✓ Generic default | ✓ Custom (Pedro persona) | ✓ Generic default | Ricky has the best custom SOUL |
| IDENTITY.md | ✓ Filled in | ✓ Via SOUL.md | **✗ BLANK TEMPLATE** | Noah's bot has no identity |
| USER.md | ✓ Filled in (Josh's profile) | ⚠️ Sparse (name/tz only) | **✗ BLANK TEMPLATE** | Noah has zero user context |
| TOOLS.md | ⚠️ Generic template | ⚠️ Generic template | ⚠️ Generic template | All 3 are unfilled boilerplate |
| AGENTS.md | ✓ Present | ✓ Present | ✓ Present | Same SHA across all — fleet-shared |
| MEMORY.md | ✗ Missing | ✗ Missing | ✗ Missing | None of the 3 have a MEMORY.md |
| cron.json | **✗ Missing** | ✓ Present (3 jobs) | **✗ Missing** | Josh and Noah have no automation |
| BOOTSTRAP.md | ✓ Present | ✗ Missing | ✓ Present | Ricky missing bootstrap context |
| MIKUNA_BRAND.md | ✗ N/A | ✓ Present | ✗ N/A | Ricky's unique brand context file |

---

## Model Provider Comparison

| Customer | Primary Model | Fallbacks | Provider |
|----------|-------------|-----------|----------|
| Josh | gemini-3-flash-preview | openrouter/gemini-2.5-flash, openrouter/claude-3.5-haiku | Google + OpenRouter |
| Ricky | gemini-3.1-pro-preview (main agent) | **NONE** | Google only — **single point of failure** |
| Noah | claude-sonnet-4-6 | claude-opus-4-6 (in models, not fallbacks) | Anthropic only |

**Key risks:**
- **Ricky** has no fallbacks — any Google API issue silences Pedro entirely during business operations
- **Josh's** claude-3.5-haiku fallback is retired — should upgrade to claude-haiku-4-5
- **Noah** has claude-opus-4-6 in the models catalog but it's not wired as a fallback in the model config

---

## Memory Plugin Status

**All 3 customers: No memory plugin configured.**

This is the single biggest capability gap across the entire fleet. Every instance wakes up with no persistent memory. Relevant context from past sessions (user preferences, ongoing projects, learned behaviors, business knowledge) is lost on every restart.

Memory impact by use case:
- **Josh (personal assistant):** High impact — should remember contacts, preferences, ongoing projects, communication style
- **Ricky (brand ops):** High impact — should remember Mikuna brand context, Shopify nuances, team member names, recurring tasks
- **Noah (trading bot):** Critical impact — should remember active watchlist, catalyst thesis threads, past trade accuracy, Noah's risk rules

**Recommended plugin:** `@openclaw/plugin-memory-lancedb`  
Install + enable on all 3 instances. As of OpenClaw 2026.4.15 beta, LanceDB memory now supports cloud/remote object storage — highly relevant for cloud-hosted instances where local disk isn't persistent.

---

## Automation (Cron) Comparison

| Customer | Has cron.json | # Jobs | Assessment |
|----------|--------------|--------|------------|
| Josh | No | 0 | **Gap** — personal assistant with no proactive actions |
| Ricky | Yes | 3 | Good foundation. Can expand to Amazon + daily ops |
| Noah | No | 0 | **Critical gap** — trading bot should never be reactive-only |

Ricky's cron.json is the only one on the fleet. It covers Shopify weekly and monthly report delivery. No Amazon monitoring jobs, no daily ops check.

---

## Workspace File Gaps: Detailed

### MEMORY.md — Missing Across All 3
None of the bots have a `workspace/MEMORY.md` file. This is a lightweight but valuable companion to the memory plugin — a human-readable scratch pad for the most important persistent facts (user preferences, key context, ongoing work). Even without the memory plugin installed, a manually maintained MEMORY.md acts as session continuity.

**Recommended:** Add a `workspace/MEMORY.md` to all 3 repos, pre-seeded with known context, and have each bot maintain it.

### TOOLS.md — Unfilled Boilerplate Across All 3
All 3 TOOLS.md files contain only the default example content. This file is injected at bootstrap and wastes context window with generic non-information. Each bot should populate it with their actual setup:
- **Josh:** iMessage contacts, email accounts, calendar IDs, home automation endpoints
- **Ricky:** Shopify store details, Amazon seller account, Slack channel IDs, brand asset locations
- **Noah:** Alpaca account alias, SEC EDGAR watchlist tickers, preferred data sources, Discord channel routing

### BOOTSTRAP.md — Ricky Missing
Josh and Noah have BOOTSTRAP.md; Ricky does not. This file gives the agent startup instructions. Worth checking if Ricky's alphaclaw hooks include equivalent bootstrap behavior.

---

## Channel Configuration Comparison

| Customer | Channel | Streaming | Group Policy | DM Policy |
|----------|---------|-----------|-------------|----------|
| Josh | Discord | **off** | open | open |
| Ricky | Slack | partial (nativeStreaming) | open | open |
| Noah | Discord | not set (off) | **allowlist** | pairing |

- Josh's Discord streaming being `off` is the most impactful quick fix — set to `partial`
- Noah's security posture (allowlist + pairing) is appropriate for a trading bot
- Ricky has the best streaming config on the fleet

---

## Security Notes

1. **Noah's exec security** is set to `"full"` with `strictInlineEval: false` — the `false` on strictInlineEval is worth auditing. It allows inline JS eval in tools, which could be a vector if a malicious skill is installed.

2. **ClawHub malware warning (2026 Q1):** 2,419 suspicious skills purged; 1,184 distributed wallet/credential-stealing malware. Noah has a `skills/` directory that needs auditing. Josh and Ricky have no custom skills directories.

3. **Exposed instances:** ~21,639 OpenClaw instances remain publicly accessible on the internet as of March 2026. All 3 fleet instances use `gateway.bind: loopback` + `trustedProxies: ["127.0.0.1"]` — this is good practice. Confirm the `allowedOrigins` in `controlUi` are still accurate.

---

## Customer-Specific Improvement Summary

### Josh (Heather Schwartz) — Personal Assistant
**Top priorities:**
1. Memory plugin — critical for a personal assistant that handles email/calendar/contacts
2. Cron automation — add morning briefing and Friday wrap
3. Discord streaming on
4. Update OpenClaw
5. Fill in TOOLS.md with Josh's actual setup

**Unique gap:** No proactive automation at all. Heather is fully reactive.

---

### Ricky (Pedro) — Mikuna Foods Brand Ops
**Top priorities:**
1. Add fallback models — critical reliability gap
2. Update OpenClaw (most outdated on fleet)
3. Memory plugin — for brand/team knowledge retention
4. Expand cron.json — add Amazon monitoring + daily ops check
5. Fill in USER.md and TOOLS.md

**Unique asset:** MIKUNA_BRAND.md is excellent context grounding. This is a model for the other fleet members — Noah should consider a similar MARKET_CONTEXT.md with trading strategy and universe.

---

### Noah (Market Catalyst Agent) — Stock Catalyst Hunter
**Top priorities:**
1. Add cron.json with market schedule — critical for a trading bot
2. Fill in USER.md and IDENTITY.md — most fundamental gap on the fleet
3. Memory plugin — for persistent watchlist and thesis tracking
4. Update OpenClaw (includes security fix for exec.security setups)
5. Upgrade to Claude Opus 4.7 for deep SEC/filing analysis tasks
6. Audit `skills/` directory for security

**Unique asset:** `gogcli/` and `skills/` directories suggest a more built-out technical setup. Highest potential ceiling of the 3 bots once gaps are closed.

---

## Fleet-Wide Action Checklist

- [ ] Update all 3 instances to OpenClaw 2026.4.14
- [ ] Install `memory-lancedb` on all 3 instances
- [ ] Create `workspace/MEMORY.md` on all 3 repos (pre-seeded)
- [ ] Fill in `workspace/TOOLS.md` on all 3 (environment-specific notes)
- [ ] Josh: Enable Discord streaming + add cron.json
- [ ] Ricky: Add fallback models + expand cron.json
- [ ] Noah: Add cron.json (market schedule) + fill in USER.md + IDENTITY.md
- [ ] Noah: Audit skills/ directory for malicious packages
- [ ] Noah: Review strictInlineEval: false in exec security config
