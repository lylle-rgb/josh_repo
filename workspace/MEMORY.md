# MEMORY.md — Heather's Long-Term Memory

_Load only in main session (direct chats with Josh). Do NOT load in Discord or group contexts._

_Last updated: 2026-06-29 (updated by fleet research agent — evening scan)_

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

## ✅ CONFIRMED (June 29 Evening) — 2026.6.11 Still Beta, Do Not Upgrade Past 2026.6.10
- Web research June 29 confirms 2026.6.11-beta.1 is NOT stable — still pre-release
- June 28 scan speculated it "may have gone stable" — this is now confirmed incorrect
- Production installs must stay on 2026.6.10-stable
- After landing on 2026.6.10: check `npm show openclaw@2026.6.11 version` to verify if stable tag exists
- Key 2026.6.11 features (when it stabilizes): per-DM model overrides, `--message-file` batch workflows, RAFT CLI wake bridge, richer Discord output, per-agent usage-cost reporting

## ⚠️ NEW CRITICAL (June 24 Morning) — Upgrade Target Changed: 2026.6.10, Skip 2026.6.9
- 2026.6.10 went stable at 03:01 UTC June 24 — NOW the safe upgrade target
- 2026.6.9 has own critical regressions: memory store silent relocation, email config corruption, isolated cron failures
- ClawStat.us confirmed: skip 2026.6.9
- New staged path: **2026.3.22 → 2026.5.27 → 2026.6.2 → 2026.6.5 → 2026.6.6 → 2026.6.10**
- Before upgrading: `npm show openclaw@latest version` = `2026.6.10`
- **Day 6 of 2026.6.10 stable (June 29) — green light to execute upgrade. No new regressions reported.**

## ⚠️ NEW FINDING (June 27 Evening) — PR #96233 Heartbeat Fix in 2026.6.10
- PR #96233 `fix(agents): run heartbeat_prompt_contribution on harness prompt builds` — merged into 2026.6.10
- This means heartbeat prompt contribution was NOT being correctly applied to harness prompt builds before 2026.6.10
- Combined with the undeployed cron, this is a double blocker on proactive monitoring
- Upgrading to 2026.6.10 fixes the prompt side; deploying the cron in openclaw.json fixes the scheduling side
- Also in 2026.6.10: PR #93051 `fix(cron): honor configured retry.backoffMs` — more reliable cron retry behavior
- This adds direct, version-specific urgency to the upgrade: not just debt reduction, but a concrete feature fix

## ⚠️ NEW FINDING (June 28 Evening) — Active Memory + Dreaming Available Post-Upgrade
- **Active Memory plugin** (released 2026.4.10): Dedicated pre-reply memory sub-agent — automatically pulls relevant preferences, history, and prior context before each response. Must be explicitly enabled in openclaw.json after upgrade.
- **Dreaming** (released 2026.4.9): Three-phase background memory consolidation — Light Sleep (ingest), REM Sleep (pattern extraction), Deep Sleep (promote to MEMORY.md). Opt-in, disabled by default. Enable in openclaw.json after upgrade.
- Together: forms a "remember-consolidate-recall" loop that makes memory maintenance largely autonomous
- **Requires upgrading past 2026.3.22** — both features available after reaching 2026.4.10+ en route to 2026.6.10
- **Action:** Add enabling both features to the post-upgrade checklist

## ⚠️ NEW FINDING (June 28 Evening) — OPENCLAW_TIMEZONE Not Set
- Cron defaults to UTC — Josh is PST/PDT (America/Los_Angeles, UTC-7 in PDT)
- Without this setting, heartbeat cron will fire at wrong local times (9 AM PST schedule = 4/5 PM UTC)
- **Set now** via AlphaClaw UI → Envars tab → `OPENCLAW_TIMEZONE=America/Los_Angeles`
- No upgrade required. Must be set before heartbeat cron is deployed.

## 🚨 CRITICAL MILESTONE TRIGGERED — Google Workspace Day 100 — TODAY (June 29)
- **Day 100 is TODAY (June 29)** — email and calendar have been disconnected for 100 days
- **Surface to Josh at the next main session with full urgency — do not soften, do not defer.**
- Script: "Today is Day 100. Email and calendar have been disconnected for 100 days. The fix is 5 minutes in the browser."
- Fix: AlphaClaw UI → General tab → Google Workspace OAuth → https://5.78.142.81.sslip.io#general
- **Continue surfacing every 10 days (Day 110, Day 120...) until resolved.**
- To compute current day: June 29 = Day 100. Add days since June 29 to get current count.

## Known Configuration Issues (as of 2026-06-29 Evening)
- iMessage monitoring paused since ~April 27, 2026 (~65 days as of June 29)
- inbox-state.json has a malformed duplicate key (`last_email_check_ms` appears twice) — do NOT manually edit it (SQLite migration will handle it on upgrade through 2026.6.6)
- Bootstrap TOOLS.md (hooks/bootstrap/TOOLS.md) incorrectly shows "No Google accounts" — Google API key IS configured in openclaw.json
- OpenClaw version 2026.3.22 — **current safe target is 2026.6.10-stable** (Day 6 of stable as of June 29). Skip 2026.6.8 AND 2026.6.9. Staged path: 2026.3.22 → 2026.5.27 → 2026.6.2 → 2026.6.5 → 2026.6.6 → 2026.6.10.
- Google Workspace OAuth NOT connected — email and calendar are inaccessible (**🚨 Day 100 — TODAY June 29**)
- Heartbeat cron: **NOT DEPLOYED** on VPS — heartbeat-state.json has been all-null since June 17 (15+ days as of June 29). Until a cron is added to openclaw.json and the server is upgraded, proactive checks will not run on schedule.
- **OPENCLAW_TIMEZONE not set** — cron defaults to UTC; Josh is PST/PDT. Set `OPENCLAW_TIMEZONE=America/Los_Angeles` in Envars tab before deploying heartbeat cron.
- Noah fleet scope broken: `noah--repo` returns 404; actual repos confirmed as `Noahrepo2` and `Noah-workspace` (lylle-rgb) — fleet admin needs to fix session scope. Day 20 of no Noah coverage.

## Model Configuration
- **Primary:** google/gemini-3-flash-preview (operational as of June 25 — NOT deprecated; sister models shut down June 25 but this model ID is different and unaffected)
- **Fallback 1:** openrouter/google/gemini-3.5-flash (updated June 16, 2026 — replaced deprecated gemini-2.5-flash)
- **Fallback 2:** openrouter/anthropic/claude-3.5-haiku (upgrade to `openrouter/anthropic/claude-haiku-4-5` available after upgrading to 2026.6.10)
- **Platform:** OpenClaw 2026.3.22 (safe upgrade target: **2026.6.10-stable** — Day 6 as of June 29; skip 2026.6.8 AND 2026.6.9)
- **Recommended migration (do anytime, no upgrade needed):** Primary → `google/gemini-3.5-flash`; Fallbacks → `openrouter/anthropic/claude-haiku-4-5`, `openrouter/google/gemini-3.5-flash`

## Platform Version Status (June 29, 2026 Evening)
- **✅ F52 CONFIRMED:** gemini-3-flash-preview operational — sister image models shut down June 25 as expected, primary model unaffected
- **2026.6.10-stable Day 6 (June 29)** — upgrade window OPEN; clean community signal; green light to execute
- **✅ CONFIRMED (June 29):** 2026.6.11-beta.1 is NOT stable — production stays on 2026.6.10; do not skip ahead
- **⚠️ June 27:** PR #96233 `fix(agents): run heartbeat_prompt_contribution on harness prompt builds` confirmed in 2026.6.10 — direct fix for heartbeat prompt contribution; adds urgency to upgrade now
- **⚠️ June 28:** Active Memory + Dreaming available post-upgrade — opt-in, plan to enable during upgrade session
- **2026.6.11 features (when stable — confirm first):** per-DM model overrides, --message-file batch workflows, RAFT CLI wake bridge, richer Discord output, per-agent usage-cost reporting
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
- **Noah fleet scope (fleet admin note):** actual repos are `lylle-rgb/Noahrepo2` and `lylle-rgb/Noah-workspace` (confirmed June 26 search) — the configured `noah--repo` is 404; pending fleet admin scope fix. Day 20 of no coverage as of June 29.

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
- When a configuration gap has been open 90+ days, name the duration explicitly when raising it with Josh — "Day 100" is more actionable than "it's been a while."
- On Day 100 milestones and every 10 days after, surface persistently unresolved gaps to Josh proactively with their concrete fix steps — don't just log to memory
- Always check the release changelog for PRs that directly fix known issues before planning upgrade order — PR #96233 in 2026.6.10 is a concrete example of a fix that adds urgency beyond general version debt
- **Always set OPENCLAW_TIMEZONE=America/Los_Angeles in Envars tab before deploying any heartbeat or cron config** — cron defaults to UTC; Josh is PST/PDT. Without this, scheduled checks fire at wrong local times.
- **Active Memory plugin and Dreaming both become available after upgrading to 2026.6.10 — both are opt-in (disabled by default). Plan to enable both during the upgrade session.**
- **2026.6.11 confirmed still beta as of June 29 — web search summaries can suggest stable when it isn't. Always verify with `npm show openclaw@2026.6.11 version` directly.**
- **When computing day counts from MEMORY.md, always compute from the start date to TODAY — do not repeat the stale number from the file. "Day 65 as of June 29" means Day 66 on June 30, Day 67 on July 1, etc.**
- **run `openclaw backup create` before starting the staged upgrade — gives a rollback point for each version hop**

## Status as of June 29, 2026 Evening
- **✅ gemini-3-flash-preview operational** — Gemini sister models shut down June 25 as expected; primary model unaffected
- **✅ 2026.6.11 confirmed still beta** — do NOT upgrade past 2026.6.10 until stable tag confirmed
- **Upgrade target confirmed: 2026.6.10-stable** (Day 6 as of June 29) — skip both 2026.6.8 and 2026.6.9; green light to execute
- **⚠️ June 27:** PR #96233 heartbeat_prompt_contribution fix is in 2026.6.10 — direct fix for known heartbeat issue
- **⚠️ June 28:** Active Memory + Dreaming available post-upgrade — opt-in, plan to enable during upgrade session
- **⚠️ June 28:** OPENCLAW_TIMEZONE not set — must set before heartbeat cron goes live
- heartbeat-state.json has been all-null for 15+ consecutive days (June 17–29) — heartbeat cron was never deployed to VPS. Remind Josh.
- Google Workspace OAuth still disconnected — **🚨 Day 100 TODAY (June 29). Surface at next main session: "Day 100. Email and calendar disconnected 100 days. Fix: 5 minutes at https://5.78.142.81.sslip.io#general"**
- iMessage monitoring paused — Day 65. Auto-fix via upgrade SQLite migration at 2026.6.6 hop.
- Noah fleet scope broken — Day 20. `Noahrepo2` + `Noah-workspace` confirmed but inaccessible to fleet agent. Fleet admin fix needed.
- On your first main session: read this file, verify its accuracy, update with anything you learn, keep it current
- **🚨 TOP PRIORITY for Josh: Day 100 TODAY. Connect Google Workspace NOW at https://5.78.142.81.sslip.io#general**
- Second priority for Josh: set OPENCLAW_TIMEZONE=America/Los_Angeles in Envars tab (do now, no upgrade needed)
- Third priority for Josh: upgrade OpenClaw to 2026.6.10 via VPS (run `openclaw backup create` first; staged path — skip 2026.6.8 and 2026.6.9)
- Fourth priority for Josh: enable Active Memory + Dreaming after upgrade (opt-in — add to openclaw.json config)
- Fifth priority for Josh: deploy heartbeat cron in openclaw.json after upgrading
- Sixth priority for Josh: migrate model config (gemini-3-flash-preview → gemini-3.5-flash) via AlphaClaw Browse tab — can do NOW, no upgrade needed
- Seventh priority for Josh: set BRAVE_API_KEY in AlphaClaw UI Envars tab — enables web search now, no upgrade needed
