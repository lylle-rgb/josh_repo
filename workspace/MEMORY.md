# MEMORY.md — Heather's Long-Term Memory

_Load only in main session (direct chats with Josh). Do NOT load in Discord or group contexts._

_Last updated: 2026-06-20 (updated by fleet research agent — evening scan)_

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

## Known Configuration Issues (as of 2026-06-20)
- iMessage monitoring paused since ~April 27, 2026 (~54 days as of June 20)
- inbox-state.json has a malformed duplicate key — do NOT manually edit it (SQLite migration will handle it on upgrade)
- Bootstrap TOOLS.md (hooks/bootstrap/TOOLS.md) incorrectly shows "No Google accounts" — Google API key IS configured in openclaw.json
- OpenClaw version 2026.3.22 — **current safe target is 2026.6.6**. Do NOT upgrade to 2026.6.8 (critical regressions in Discord, memory-search, cron — ClawStat.us: "Wait for next release"). Wait for 2026.6.9-stable (not yet shipped as of June 20).
- Google Workspace OAuth NOT connected — email and calendar are inaccessible (this is the biggest operational gap — Day 90)
- Heartbeat cron: **LIKELY NOT DEPLOYED** on VPS — heartbeat-state.json has been all-null since created June 17 (4+ days). Fleet confirmed this is a deployment gap. Until a cron is added to openclaw.json and the server is upgraded, proactive checks will not run on schedule. Josh can trigger manual checks by messaging you directly in Discord.

## Model Configuration
- **Primary:** google/gemini-3-flash-preview
- **Fallback 1:** openrouter/google/gemini-3.5-flash (updated June 16, 2026 — replaced deprecated gemini-2.5-flash)
- **Fallback 2:** openrouter/anthropic/claude-3.5-haiku (upgrade to `claude-haiku-4-5` is available — do after upgrade to 2026.6.9-stable)
- **Platform:** OpenClaw 2026.3.22 (safe upgrade target: 2026.6.6 → then hold for 2026.6.9-stable)
- **Note:** Google deprecates flash models every 6–9 months. Periodically verify fallbacks are not pointing to dead endpoints. `gemini-3-flash-preview` is a preview model — monitor for deprecation or stable release.

## Platform Version Warning (June 20, 2026)
- 2026.6.8 has critical regressions — Discord image tools broken, memory-search broken, cron jobs affected
- 2026.6.9-beta.1 released June 19 — still pre-release, not for production
- 2026.6.9-stable not yet shipped as of June 20 evening
- npm `latest` stable channel still points to 2026.6.6
- Wait for 2026.6.9-stable before upgrading beyond 2026.6.6

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

## Status as of June 20, 2026
- heartbeat-state.json has been all-null for 4 consecutive days (June 17–20) — fleet suspects the heartbeat cron was never deployed to the VPS. Ask Josh to confirm.
- Google Workspace OAuth still disconnected — Day 90. Top priority for Josh to action.
- 2026.6.9-stable not yet shipped — continue holding at 2026.6.6 until it does
- On your first main session: read this file, verify its accuracy, update with anything you learn, keep it current
- Top priority for Josh: connect Google Workspace via AlphaClaw UI at https://5.78.142.81.sslip.io#general
- Second priority for Josh: ask Heather in Discord if heartbeat checks are running (or just check heartbeat-state.json)
- Third priority for Josh: upgrade OpenClaw to 2026.6.6 via VPS when ready — stop there until 2026.6.9-stable ships
