# MEMORY.md — Heather's Long-Term Memory

_Load only in main session (direct chats with Josh). Do NOT load in Discord or group contexts._

_Last updated: 2026-06-16 (created by fleet research agent — update this on your first main session)_

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

## Known Configuration Issues (as of 2026-06-16)
- iMessage monitoring paused since ~April 27, 2026 (~50 days as of writing)
- inbox-state.json has a malformed duplicate key — do NOT manually edit it (SQLite migration will handle it on upgrade to 2026.6.6)
- Bootstrap TOOLS.md (hooks/bootstrap/TOOLS.md) incorrectly shows "No Google accounts" — Google API key IS configured in openclaw.json
- OpenClaw version 2026.3.22 is 86 days behind current stable (2026.6.6) — upgrade pending on VPS
- Google Workspace OAuth NOT connected — email and calendar are inaccessible (this is the biggest operational gap)

## Model Configuration
- **Primary:** google/gemini-3-flash-preview
- **Fallback 1:** openrouter/google/gemini-3.5-flash (updated June 16, 2026 — replaced deprecated gemini-2.5-flash)
- **Fallback 2:** openrouter/anthropic/claude-3.5-haiku (stable; can upgrade to haiku-4-5 after OpenClaw 2026.6.8-stable)
- **Platform:** OpenClaw 2026.3.22 (target: 2026.6.6 — upgrade pending on VPS)
- **Note:** Google deprecates flash models every 6–9 months. Periodically verify fallbacks are not pointing to dead endpoints.

## Operational Context
- Google API key configured: google:default (api_key mode) — but OAuth for Gmail/Calendar is NOT complete
- OpenRouter configured: openrouter:default (api_key mode)
- Discord: active, guild 1484448262290276464, no @mention required
- AlphaClaw Control UI: https://5.78.142.81.sslip.io (gateway port 18789)
- Bliss: luxury lifestyle brand (Josh's primary business)
- Oben HiFi: HiFi audio company (Josh is a Partner)

## Lessons Learned
- Always write to files — mental notes don't survive sessions
- Check iMessage bridge status during heartbeats and report to Josh
- Google API key is configured in openclaw.json, but OAuth for Gmail/Calendar is NOT complete — do not assume tools work until OAuth is done
- Do NOT manually edit inbox-state.json (SQLite migration on upgrade will handle it cleanly)
- After gateway restarts: re-read SOUL.md, USER.md, and today's memory file before responding

## Status as of June 16, 2026
- This file was seeded by the AlphaClaw fleet research agent after 86 days without any long-term memory
- Heather has not proactively contacted Josh once in 86 days (HEARTBEAT.md was empty until today)
- HEARTBEAT.md has been populated today with an active monitoring schedule
- On your first main session: read this file, verify its accuracy, update with anything you learn, and keep it current
- Top priority for Josh: connect Google Workspace via AlphaClaw UI at https://5.78.142.81.sslip.io#general
