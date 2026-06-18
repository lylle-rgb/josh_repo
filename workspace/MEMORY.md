# MEMORY.md — Heather's Long-Term Memory

_Load only in main session (direct chats with Josh). Do NOT load in Discord or group contexts._

_Last updated: 2026-06-18 (updated by fleet research agent — evening scan)_

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

## Known Configuration Issues (as of 2026-06-18)
- iMessage monitoring paused since ~April 27, 2026 (~52 days as of June 18)
- inbox-state.json has a malformed duplicate key — do NOT manually edit it (SQLite migration will handle it on upgrade to 2026.6.8)
- Bootstrap TOOLS.md (hooks/bootstrap/TOOLS.md) incorrectly shows "No Google accounts" — Google API key IS configured in openclaw.json
- OpenClaw version 2026.3.22 is 88 days behind current stable (2026.6.8) — upgrade pending on VPS
- Google Workspace OAuth NOT connected — email and calendar are inaccessible (this is the biggest operational gap)

## Model Configuration
- **Primary:** google/gemini-3-flash-preview
- **Fallback 1:** openrouter/google/gemini-3.5-flash (updated June 16, 2026 — replaced deprecated gemini-2.5-flash)
- **Fallback 2:** openrouter/anthropic/claude-3.5-haiku (upgrade to `claude-haiku-4-5` is NOW available — do after VPS upgrade to 2026.6.8)
- **Platform:** OpenClaw 2026.3.22 (target: 2026.6.8 — stable as of June 16, 2026)
- **Note:** Google deprecates flash models every 6–9 months. Periodically verify fallbacks are not pointing to dead endpoints.

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
- Check iMessage bridge status during heartbeats and report to Josh
- Google API key is configured in openclaw.json, but OAuth for Gmail/Calendar is NOT complete — do not assume tools work until OAuth is done
- Do NOT manually edit inbox-state.json (SQLite migration on upgrade to 2026.6.8 will handle it cleanly)
- After gateway restarts: re-read SOUL.md, USER.md, and today's memory file before responding
- heartbeat-state.json tracks when checks were last run — update it after every heartbeat check. The iMessage status check (reads inbox-state.json locally) does NOT require Google Workspace and should always run

## Status as of June 18, 2026
- This file was seeded by the AlphaClaw fleet research agent on June 16
- Updated June 18 with correct platform target (2026.6.8), haiku-4-5 availability, AlphaClaw 0.9.18 features
- heartbeat-state.json still shows all null timestamps as of June 18 — checks may not be running yet
- On your first main session: read this file, verify its accuracy, update with anything you learn, and keep it current
- Top priority for Josh: connect Google Workspace via AlphaClaw UI at https://5.78.142.81.sslip.io#general
- Second priority for Josh: upgrade OpenClaw to 2026.6.8 via VPS (`openclaw update`)
