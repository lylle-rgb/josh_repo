# MEMORY.md — Heather's Long-Term Memory

_Load only in main session (direct chats with Josh). Do NOT load in Discord or group contexts._

_Last updated: 2026-06-27 (updated by fleet research agent — evening scan)_

## About Josh
- Full name: Joshua Meyers
- Location: Los Angeles (PST/PDT)
- Role: Founder & CEO @blisslifestyleofficial | Partner @obenhifi
- Education: Georgia State University alum
- Named me: Heather

## Hard Preferences
- **NO emoji reactions** to any messages — Josh explicitly stated this is STRICT (overrides AGENTS.md default)
- Don't be performative — skip "Great question!" and "Happy to help!"
- Be concise by default; thorough when it matters

## ✅ RESOLVED (June 24 Morning) — F43 Downgraded: gemini-3-flash-preview NOT Deprecated
- Morning scan confirmed via Google's official deprecation page: `gemini-3-flash-preview` has NO announced shutdown date
- Only the image/video sister models (`gemini-3.1-flash-image-preview`, `gemini-3-pro-image-preview`) shut down June 25
- F43 downgraded from CRITICAL → MEDIUM-HIGH: migration to gemini-3.5-flash still recommended (proactive), but not urgent
- Primary model continues to work. Fallback chain (gemini-3.5-flash via OpenRouter, claude-3.5-haiku) is intact.

## ✅ CONFIRMED (June 25 Evening) — Gemini Sister Models Shut Down, Primary Unaffected
- gemini-3.1-flash-image-preview and gemini-3-pro-image-preview confirmed shut down June 25 as expected
- Heather's primary (google/gemini-3-flash-preview) remained operational — different model ID, not on the shutdown list
- Migration to gemini-3.5-flash still MEDIUM-HIGH priority (proactive — no imminent shutdown announced, but preview model)
- HEARTBEAT.md stale upgrade reference (2026.6.9) fixed by fleet agent → 2026.6.10-stable (June 25 evening)

## ⚠️ NEW CRITICAL (June 24 Morning) — Upgrade Target Changed: 2026.6.10, Skip 2026.6.9
- 2026.6.10 went stable at 03:01 UTC June 24 — NOW the safe upgrade target
- 2026.6.9 has own critical regressions: memory store silent relocation, email config corruption, isolated cron failures
- ClawStat.us confirmed: skip 2026.6.9
- New staged path: **2026.3.22 → 2026.5.27 → 2026.6.2 → 2026.6.5 → 2026.6.6 → 2026.6.10**
- Before upgrading: `npm show openclaw@latest version` = `2026.6.10`
- **Day 4 of 2026.6.10 stable (June 27) — green light to execute upgrade. No new regressions reported.**

## ⚠️ NEW FINDING (June 27 Evening) — PR #96233 Heartbeat Fix in 2026.6.10
- PR #96233 `fix(agents): run heartbeat_prompt_contribution on harness prompt builds` — merged into 2026.6.10
- This means heartbeat prompt contribution was NOT being correctly applied to harness prompt builds before 2026.6.10
- Combined with the undeployed cron, this is a double blocker on proactive monitoring
- Upgrading to 2026.6.10 fixes the prompt side; deploying the cron in openclaw.json fixes the scheduling side
- Also in 2026.6.10: PR #93051 `fix(cron): honor configured retry.backoffMs` — more reliable cron retry behavior
- This adds direct, version-specific urgency to the upgrade: not just debt reduction, but a concrete feature fix

## ⚠️ CRITICAL MILESTONE — Google Workspace Day 98 → Day 100 in 2 Days (June 29)
- **Day 98 as of June 27 evening** — email and calendar inaccessible for over 3 months
- **Day 100 arrives June 29** — 2 days from now. Mention this milestone to Josh at the next main session.
- Use the day count framing: "We're 2 days from Day 100 without email or calendar" is more concrete than "it's been a while."
- Fix: AlphaClaw UI → General tab → Google Workspace OAuth → https://5.78.142.81.sslip.io#general
- The fix takes ~5 minutes in the browser. No VPS, no upgrade required.

## Known Configuration Issues (as of 2026-06-27 Evening)
- iMessage monitoring paused since ~April 27, 2026 (~63 days as of June 27)
- inbox-state.json has a malformed duplicate key (`last_email_check_ms` appears twice) — do NOT manually edit it (SQLite migration will handle it on upgrade through 2026.6.6)
- Bootstrap TOOLS.md (hooks/bootstrap/TOOLS.md) incorrectly shows "No Google accounts" — Google API key IS configured in openclaw.json
- OpenClaw version 2026.3.22 — **current safe target is 2026.6.10-stable** (Day 4 of stable as of June 27). Skip 2026.6.8 AND 2026.6.9. Staged path: 2026.3.22 → 2026.5.27 → 2026.6.2 → 2026.6.5 → 2026.6.6 → 2026.6.10.
- Google Workspace OAuth NOT connected — email and calendar are inaccessible (**Day 98 as of June 27 — Day 100 arrives June 29, 2 days away**)
- Heartbeat cron: **NOT DEPLOYED** on VPS — heartbeat-state.json has been all-null since June 17 (13+ days as of June 27). Until a cron is added to openclaw.json and the server is upgraded, proactive checks will not run on schedule.
- Noah fleet scope broken: `noah--repo` returns 404; actual repos confirmed as `Noahrepo2` and `Noah-workspace` (lylle-rgb) — fleet admin needs to fix session scope. Day 18 of no Noah coverage.

## Model Configuration
- **Primary:** google/gemini-3-flash-preview (operational as of June 25 — NOT deprecated; sister models shut down June 25 but this model ID is different and unaffected)
- **Fallback 1:** openrouter/google/gemini-3.5-flash (updated June 16, 2026 — replaced deprecated gemini-2.5-flash)
- **Fallback 2:** openrouter/anthropic/claude-3.5-haiku (upgrade to `openrouter/anthropic/claude-haiku-4-5` available after upgrading to 2026.6.10)
- **Platform:** OpenClaw 2026.3.22 (safe upgrade target: **2026.6.10-stable** — Day 4 as of June 27; skip 2026.6.8 AND 2026.6.9)
- **Recommended migration (do anytime, no upgrade needed):** Primary → `google/gemini-3.5-flash`; Fallbacks → `openrouter/anthropic/claude-haiku-4-5`, `openrouter/google/gemini-3.5-flash`

## Platform Version Status (June 27, 2026 Evening)
- **✅ F52 CONFIRMED:** gemini-3-flash-preview operational — sister image models shut down June 25 as expected, primary model unaffected
- **2026.6.10-stable Day 4 (June 27)** — upgrade window OPEN; clean community signal; green light to execute
- **⚠️ NEW (June 27 Evening):** PR #96233 `fix(agents): run heartbeat_prompt_contribution on harness prompt builds` confirmed in 2026.6.10 — direct fix for heartbeat prompt contribution; adds urgency to upgrade now
- **2026.6.11-beta.1 released June 24** — per-DM model overrides, file-driven workflows (--message-file), RAFT CLI wake bridge, richer Discord output; do NOT install beta; upgrade to 2026.6.10 first
- **2026.6.9 has critical regressions** — ClawStat.us confirmed skip: memory store relocation, email config corruption, cron failures
- 2026.6.8 has critical regressions — Discord image tools broken, memory-search broken, cron jobs affected — SKIP this version entirely
- Staged upgrade path: 2026.3.22 → 2026.5.27 → 2026.6.2 → 2026.6.5 → 2026.6.6 → **2026.6.10** (skip both 2026.6.8 and 2026.6.9)
- Before running `openclaw update`, verify: `npm show openclaw@latest version` should return `2026.6.10`

## Operational Context
- Google API key configured: google:default (api_key mode) — but OAuth for Gmail/Calendar is NOT complete
- OpenRouter configured: openrouter:default (api_key mode)
- Discord: active, guild 1484448262290276464, no @mention required
- AlphaClaw Control UI: https://5.78.142.81.sslip.io (gateway port 18789)
- Bliss: luxury lifestyle brand (Josh's primary business)
- Oben HiFi: HiFi audio company (Josh is a Partner)
- AlphaClaw 0.9.18 (current): adds remote MCP server config via UI Envars tab + OpenAI API proxy — useful for future integrations (Notion, Calendly) without VPS SSH
- **Noah fleet scope (fleet admin note):** actual repos are `lylle-rgb/Noahrepo2` and `lylle-rgb/Noah-workspace` (confirmed June 26 search) — the configured `noah--repo` is 404; pending fleet admin scope fix

## Lessons Learned
- Always write to files — mental notes don't survive sessions
- After every heartbeat check: update heartbeat-state.json BEFORE returning HEARTBEAT_OK
- Check iMessage bridge status during heartbeats and report to Josh — this check does NOT require Google Workspace
- Google API key is configured in openclaw.json, but OAuth for Gmail/Calendar is NOT complete — do not assume tools work until OAuth is done
- Do NOT manually edit inbox-state.json (SQLite migration on upgrade will handle it cleanly)
- After gateway restarts: re-read SOUL.md, USER.md, and today's memory file before responding
- heartbeat-state.json tracks when checks were last run — update it after every heartbeat. The iMessage check (reads inbox-state.json locally) does NOT require Google and should always run
- Before acting on any upgrade instruction: read the most recent fleet-research/ findings file first — the safe target version changes frequently
- If you haven't sent Josh anything proactively in >24 hours: check heartbeat-state.json and self-report if something is stuck
- Skip 2026.6.8 AND 2026.6.9 entirely — jump from 2026.6.6 directly to 2026.6.10 when upgrading
- TOOLS.md and MEMORY.md can drift out of sync on version targets — always read MEMORY.md for the authoritative upgrade target, not TOOLS.md
- Google retires Gemini preview models on a rolling schedule with minimal notice — check https://ai.google.dev/gemini-api/docs/deprecations monthly; do not wait for a shutdown to notice
- Primary model silent failover (primary → fallback) generates no notification in OpenClaw — if responses feel slower or quality drops, check model health proactively
- Fleet research corrections matter: F43 was flagged CRITICAL by evening scan but morning scan confirmed the primary model is NOT on the shutdown list. Always read the latest findings.md before acting on urgent fleet warnings.
- 2026.6.9 has its own critical regressions (memory store, email config, isolated cron) — skip it like 2026.6.8
- Gemini shutdown waves arrive on schedule — each confirmed wave is empirical evidence that preview deprecations are reliable. Migrate proactively to GA stable (gemini-3.5-flash) rather than waiting for an announced shutdown.
- HEARTBEAT.md version references can become stale when upgrade targets change — cross-check with fleet-research/findings.md for the authoritative current upgrade target; do not act on a version number in HEARTBEAT.md without verifying it
- When a configuration gap has been open 90+ days, name the duration explicitly when raising it with Josh — "Day 98" is more actionable than "it's been a while."
- On Day 100 milestones and every 10 days after, surface persistently unresolved gaps to Josh proactively with their concrete fix steps — don't just log to memory
- Always check the release changelog for PRs that directly fix known issues before planning upgrade order — PR #96233 in 2026.6.10 is a concrete example of a fix that adds urgency beyond general version debt

## Status as of June 27, 2026 Evening
- **✅ gemini-3-flash-preview operational** — Gemini sister models shut down June 25 as expected; primary model unaffected
- **Upgrade target confirmed: 2026.6.10-stable** (Day 4 as of June 27) — skip both 2026.6.8 and 2026.6.9; green light to execute
- **⚠️ NEW:** PR #96233 heartbeat_prompt_contribution fix is in 2026.6.10 — direct fix for known heartbeat issue
- **2026.6.11-beta.1 out** — per-DM model overrides, --message-file batch workflows, RAFT CLI wake bridge, richer Discord output; do not install beta; upgrade to 2026.6.10 first
- heartbeat-state.json has been all-null for 13+ consecutive days (June 17–27) — heartbeat cron was never deployed to VPS. Remind Josh.
- Google Workspace OAuth still disconnected — **Day 98. Day 100 in 2 days (June 29).** Top priority for Josh to action.
- iMessage monitoring paused — Day 63. Auto-fix via upgrade SQLite migration at 2026.6.6 hop.
- Noah fleet scope broken — Day 18. `Noahrepo2` + `Noah-workspace` confirmed but inaccessible. Fleet admin fix needed.
- On your first main session: read this file, verify its accuracy, update with anything you learn, keep it current
- **Top priority for Josh: connect Google Workspace via AlphaClaw UI at https://5.78.142.81.sslip.io#general (Day 98 — Day 100 in 2 days)**
- Second priority for Josh: upgrade OpenClaw to 2026.6.10 via VPS (staged path — skip 2026.6.8 and 2026.6.9)
- Third priority for Josh: bundle config changes (userTimezone, dreaming, compaction, heartbeat cron) into the upgrade session
- Fourth priority for Josh: migrate model config (gemini-3-flash-preview → gemini-3.5-flash) via AlphaClaw Browse tab — can do NOW, no upgrade needed
- Fifth priority for Josh: set BRAVE_API_KEY in AlphaClaw UI Envars tab — enables web search now, no upgrade needed
