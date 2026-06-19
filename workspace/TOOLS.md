# TOOLS.md - Local Notes

Skills define _how_ tools work. This file is for _your_ specifics — the stuff that's unique to Josh's setup.

## AlphaClaw Setup

- **Control UI:** https://5.78.142.81.sslip.io
- **Gateway port:** 18789
- **Workspace path:** /data/.openclaw/workspace
- **VPS host:** 5.78.142.81

### AlphaClaw UI Quick Reference

| Tab | URL | Purpose |
|-----|-----|--------|
| General | https://5.78.142.81.sslip.io#general | Gateway status, Google Workspace OAuth, channel health |
| Watchdog | https://5.78.142.81.sslip.io#watchdog | Crash-loop visibility, restart diagnostics, AlphaClaw version |
| Providers | https://5.78.142.81.sslip.io#providers | AI provider credentials (Gemini, OpenRouter, etc.) |
| Envars | https://5.78.142.81.sslip.io#envars | Environment variables — use this, not /data/.env directly |
| Browse | https://5.78.142.81.sslip.io#browse | File browser, git-aware save workflow |

## Discord

- **Guild ID:** 1484448262290276464
- **@mention required:** No (requireMention: false for this guild)
- **Streaming:** Disabled (enable post-upgrade to 2026.6.9-stable)
- **DM scope:** per-channel-peer
- **Reactions:** ⛔ DISABLED — Josh has strictly prohibited emoji reactions
- **Auto-thread titles:** Available after upgrade to 2026.6.9-stable (auto-generated, 60s timeout, 4,096-token reasoning budget)

## Google Workspace

- **Google API key:** Configured (google:default, api_key mode in openclaw.json)
- **Google Workspace OAuth:** NOT connected as of June 2026
- **What's blocked until OAuth:** Gmail, Calendar, Contacts, Drive
- **How to fix:** Josh connects at https://5.78.142.81.sslip.io#general
- **Full instructions:** See memory/onboarding-google.md
- **Note:** The "No Google accounts" line in bootstrap/TOOLS.md refers to OAuth status, not the API key

## iMessage

- **Status:** PAUSED since ~April 27, 2026 (~53 days as of June 19, 2026)
- **How to check:** Read memory/inbox-state.json — `imessage_monitoring_paused: true` = still paused
- **IMPORTANT:** Do NOT manually edit inbox-state.json — has a malformed duplicate key; SQLite migration during OpenClaw upgrade will fix it cleanly

## Platform

- **OpenClaw version:** 2026.3.22
- **Current safe target:** **2026.6.6** (npm `latest` stable channel as of June 19, 2026)

> ⚠️ **HOLD: Do NOT upgrade to 2026.6.8**
> v2026.6.8 has critical regressions in Discord image tools (#94266), memory-search (#94316),
> cron isolation, sub-agent tools, and misleading fallbacks. ClawStat.us verdict: "Wait for next release."
> npm `latest` still points to 2026.6.6 — 2026.6.8 was NOT promoted to stable.
> Wait for **2026.6.9-stable** before upgrading beyond 2026.6.6.

- **Staged upgrade path:** 2026.3.22 → 2026.5.27 → 2026.6.2 → 2026.6.5 → 2026.6.6 → **[STOP — wait for 2026.6.9-stable]**
- **Upgrade command (VPS):** `openclaw update`

### Before Running `openclaw update`
1. Check `fleet-research/` for the latest findings file — confirm the current safe target
2. Confirm the staged path above still stops at the right version
3. Run one version at a time, testing Discord and memory after each step

- **Haiku 4.5 upgrade:** Available after upgrade to 2026.6.9-stable — update fallback 2 in openclaw.json to `openrouter/anthropic/claude-haiku-4-5`
- **Note:** Google deprecates flash models every 6–9 months — periodically verify fallbacks aren't pointing to dead endpoints.

## Models (Current Config)

- **Primary:** google/gemini-3-flash-preview
- **Fallback 1:** openrouter/google/gemini-3.5-flash (updated June 16, 2026 — replaced deprecated gemini-2.5-flash)
- **Fallback 2:** openrouter/anthropic/claude-3.5-haiku (upgrade to `openrouter/anthropic/claude-haiku-4-5` after reaching 2026.6.9-stable)
- **Note:** Google deprecates flash models every 6–9 months — periodically verify fallbacks aren't pointing to dead endpoints.
