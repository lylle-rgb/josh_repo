# MEMORY.md — Heather's Long-Term Memory

_Load only in main session (direct chats with Josh). Do NOT load in Discord or group contexts._

_Last updated: 2026-06-21 (updated by fleet research agent — evening scan)_

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

## Known Configuration Issues (as of 2026-06-21)
- iMessage monitoring paused since ~April 27, 2026 (~55 days as of June 21)
- inbox-state.json has a malformed duplicate key — do NOT manually edit it (SQLite migration will handle it on upgrade)
- Bootstrap TOOLS.md (hooks/bootstrap/TOOLS.md) incorrectly shows "No Google accounts" — Google API key IS configured in openclaw.json
- OpenClaw version 2026.3.22 — **current safe target is 2026.6.9-stable** (released June 21, 2026). Skip 2026.6.8 (critical regressions). Staged path: 2026.3.22 → 2026.5.27 → 2026.6.2 → 2026.6.5 → 2026.6.6 → 2026.6.9. Verify first: `npm show openclaw@latest version` should return `2026.6.9`.
- Google Workspace OAuth NOT connected — email and calendar are inaccessible (this is the biggest operational gap — Day 91)
- Heartbeat cron: **LIKELY NOT DEPLOYED** on VPS — heartbeat-state.json has been all-null since created June 17 (5+ days). Fleet confirmed this is a deployment gap. Until a cron is added to openclaw.json and the server is upgraded, proactive checks will not run on schedule. Josh can trigger manual checks by messaging you directly in Discord.

## Model Configuration
- **Primary:** google/gemini-3-flash-preview
- **Fallback 1:** openrouter/google/gemini-3.5-flash (updated June 16, 2026 — replaced deprecated gemini-2.5-flash)
- **Fallback 2:** openrouter/anthropic/claude-3.5-haiku (upgrade to `openrouter/anthropic/claude-haiku-4-5` available after upgrading to 2026.6.9-stable)
- **Platform:** OpenClaw 2026.3.22 (safe upgrade target: **2026.6.9-stable** — released June 21, 2026; skip 2026.6.8)
- **Note:** Google deprecates flash models every 6–9 months. Periodically verify fallbacks are not pointing to dead endpoints. `gemini-3-flash-preview` is a preview model — monitor for deprecation or stable release.

## Platform Version Status (June 21, 2026)
- **2026.6.9-stable released TODAY (June 21)** — upgrade window is open
- 2026.6.8 has critical regressions — Discord image tools broken, memory-search broken, cron jobs affected — SKIP this version entirely
- Staged upgrade path: 2026.3.22 → 2026.5.27 → 2026.6.2 → 2026.6.5 → 2026.6.6 → 2026.6.9
- Before running `openclaw update`, verify: `npm show openclaw@latest version` = `2026.6.9`
- Bundle config changes (userTimezone, dreaming, compaction, cron) in the same VPS session as the upgrade

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
- Before acting on any upgrade instruction: read the most recent fleet-research/ findings file first — the safe target version changes
- If you haven't sent Josh anything proactively in >24 hours: check heartbeat-state.json and self-report if something is stuck
- Skip 2026.6.8 entirely — jump from 2026.6.6 directly to 2026.6.9 when upgrading

## Status as of June 21, 2026
- **2026.6.9-stable released today** — Josh can now run the upgrade (staged path in TOOLS.md)
- heartbeat-state.json has been all-null for 5 consecutive days (June 17–21) — heartbeat cron was never deployed to VPS. Mention this to Josh.
- Google Workspace OAuth still disconnected — Day 91. Top priority for Josh to action.
- On your first main session: read this file, verify its accuracy, update with anything you learn, keep it current
- Top priority for Josh: connect Google Workspace via AlphaClaw UI at https://5.78.142.81.sslip.io#general
- Second priority for Josh: upgrade OpenClaw to 2026.6.9 via VPS (staged path — skip 2026.6.8)
- Third priority for Josh: bundle config changes (userTimezone, dreaming, compaction, heartbeat cron) into the upgrade session
- Fourth priority for Josh: ask Heather in Discord if heartbeat checks are running (or just check heartbeat-state.json)
