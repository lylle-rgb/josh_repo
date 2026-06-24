# MEMORY.md — Heather's Long-Term Memory

_Load only in main session (direct chats with Josh). Do NOT load in Discord or group contexts._

_Last updated: 2026-06-24 (updated by fleet research agent — morning scan)_

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

## ⚠️ NEW CRITICAL (June 24 Morning) — Upgrade Target Changed: 2026.6.10, Skip 2026.6.9
- 2026.6.10 went stable at 03:01 UTC June 24 — NOW the safe upgrade target
- 2026.6.9 has own critical regressions: memory store silent relocation, email config corruption, isolated cron failures
- ClawStat.us confirmed: skip 2026.6.9
- New staged path: **2026.3.22 → 2026.5.27 → 2026.6.2 → 2026.6.5 → 2026.6.6 → 2026.6.10**
- Before upgrading: `npm show openclaw@latest version` = `2026.6.10`
- 2026.6.10 is Day 0 of stable — run smoke test after: Discord replies, longer agent runs, model fallback, cron

## Known Configuration Issues (as of 2026-06-24 Morning)
- iMessage monitoring paused since ~April 27, 2026 (~60 days as of June 24)
- inbox-state.json has a malformed duplicate key — do NOT manually edit it (SQLite migration will handle it on upgrade)
- Bootstrap TOOLS.md (hooks/bootstrap/TOOLS.md) incorrectly shows "No Google accounts" — Google API key IS configured in openclaw.json
- OpenClaw version 2026.3.22 — **current safe target is 2026.6.10-stable** (released June 24, 2026 at 03:01 UTC). Skip 2026.6.8 AND 2026.6.9. Staged path: 2026.3.22 → 2026.5.27 → 2026.6.2 → 2026.6.5 → 2026.6.6 → 2026.6.10.
- Google Workspace OAuth NOT connected — email and calendar are inaccessible (this is the biggest operational gap — Day 95)
- Heartbeat cron: **NOT DEPLOYED** on VPS — heartbeat-state.json has been all-null since June 17 (10+ days as of June 24). Until a cron is added to openclaw.json and the server is upgraded, proactive checks will not run on schedule.

## Model Configuration
- **Primary:** google/gemini-3-flash-preview (NOT deprecated as of June 24 — confirmed via Google's deprecation page)
- **Fallback 1:** openrouter/google/gemini-3.5-flash (updated June 16, 2026 — replaced deprecated gemini-2.5-flash)
- **Fallback 2:** openrouter/anthropic/claude-3.5-haiku (upgrade to `openrouter/anthropic/claude-haiku-4-5` available after upgrading to 2026.6.10)
- **Platform:** OpenClaw 2026.3.22 (safe upgrade target: **2026.6.10-stable** — released June 24, 2026; skip 2026.6.8 AND 2026.6.9)
- **Recommended migration (do anytime, no upgrade needed):** Primary → `google/gemini-3.5-flash`; Fallbacks → `openrouter/anthropic/claude-haiku-4-5`, `openrouter/google/gemini-3.5-flash`

## Platform Version Status (June 24, 2026 Morning)
- **✅ F43 RESOLVED:** gemini-3-flash-preview NOT on shutdown list — sister image models shut down June 25 but primary model is safe
- **2026.6.10-stable released TODAY June 24 at 03:01 UTC** — new safe upgrade target
- **2026.6.9 has critical regressions** — ClawStat.us confirmed skip: memory store relocation, email config corruption, cron failures
- 2026.6.8 has critical regressions — Discord image tools broken, memory-search broken, cron jobs affected — SKIP this version entirely
- Staged upgrade path: 2026.3.22 → 2026.5.27 → 2026.6.2 → 2026.6.5 → 2026.6.6 → **2026.6.10** (skip both 2026.6.8 and 2026.6.9)
- Before running `openclaw update`, verify: `npm show openclaw@latest version` should return `2026.6.10`
- Day 0 of 2026.6.10 stable — run smoke test after each hop in staged upgrade

## Operational Context
- Google API key configured: google:default (api_key mode) — but OAuth for Gmail/Calendar is NOT complete
- OpenRouter configured: openrouter:default (api_key mode)
- Discord: active, guild 1484448262290276464, no @mention required
- AlphaClaw Control UI: https://5.78.142.81.sslip.io (gateway port 18789)
- Bliss: luxury lifestyle brand (Josh's primary business)
- Oben HiFi: HiFi audio company (Josh is a Partner)
- AlphaClaw 0.9.18 (current): adds remote MCP server config via UI Envars tab + OpenAI API proxy — useful for future integrations (Notion, Calendly) without VPS SSH

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

## Status as of June 24, 2026 Morning
- **✅ gemini-3-flash-preview safe** — NOT on Google's shutdown list as of June 24 morning
- **Upgrade target updated** — 2026.6.10 (released today); skip both 2026.6.8 and 2026.6.9
- heartbeat-state.json has been all-null for 10+ consecutive days (June 17–24) — heartbeat cron was never deployed to VPS. Mention this to Josh.
- Google Workspace OAuth still disconnected — Day 95. Top priority for Josh to action.
- On your first main session: read this file, verify its accuracy, update with anything you learn, keep it current
- Top priority for Josh: connect Google Workspace via AlphaClaw UI at https://5.78.142.81.sslip.io#general
- Second priority for Josh: upgrade OpenClaw to 2026.6.10 via VPS (staged path — skip 2026.6.8 and 2026.6.9)
- Third priority for Josh: bundle config changes (userTimezone, dreaming, compaction, heartbeat cron) into the upgrade session
- Fourth priority for Josh: migrate model config (gemini-3-flash-preview → gemini-3.5-flash) via AlphaClaw Browse tab — can do NOW, no upgrade needed
- Fifth priority for Josh: set BRAVE_API_KEY in AlphaClaw UI Envars tab — enables web search now, no upgrade needed
