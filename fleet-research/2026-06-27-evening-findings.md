# Fleet Research: Josh (Heather) — Evening Scan
**Date:** 2026-06-27 | **Scan type:** Evening | **Agent:** AlphaClaw Fleet Research

## Summary
Day 98 of Google Workspace downtime — Day 100 arrives in **2 days** (June 29). Upgrade window is green:
2026.6.10-stable is on Day 4 with clean community signal. New finding: PR #96233 in 2026.6.10 directly
fixes `heartbeat_prompt_contribution` — meaning the heartbeat wasn't being properly applied to harness
prompt builds before this version. Noah scope still broken (Day 18). 2026.6.11 remains in beta only.

---

## Findings

### F1 — CRITICAL: Google Workspace OAuth Day 98 — Day 100 Arrives June 29 (2 Days)
**Risk:** CRITICAL
- Email and calendar have been inaccessible since ~March 21, 2026 — **98 days as of June 27 evening**
- Day 100 arrives June 29 — 2 days from now
- Fix is ~5 minutes in a browser: AlphaClaw UI → General tab → Google Workspace OAuth → https://5.78.142.81.sslip.io#general
- No VPS, no upgrade required — can be done right now
- Per MEMORY.md lesson: at Day 100 and every 10 days after, escalate proactively with concrete fix steps
- **Action for Heather:** On next main session, frame as: "We're now at Day 98 — Day 100 is in 2 days. The fix is 5 minutes in the browser."

### F2 — HIGH: Upgrade Window Green — 2026.6.10-stable Day 4
**Risk:** HIGH (staying on 2026.3.22 accumulates version debt and known issues)
- 2026.6.10-stable released June 24, 2026 at 03:01 UTC. Day 4 as of June 27. Clean signal — no new regressions.
- Current version: 2026.3.22 — approximately 3 months behind stable
- Verify before running: `npm show openclaw@latest version` should return `2026.6.10`
- **Skip 2026.6.8 AND 2026.6.9** (confirmed critical regressions in both — see TOOLS.md for details)
- Staged upgrade path: **2026.3.22 → 2026.5.27 → 2026.6.2 → 2026.6.5 → 2026.6.6 → 2026.6.10**
- Run `openclaw update` on VPS at each hop; test Discord + memory after each step before proceeding

### F3 — HIGH: 2026.6.10 Contains Direct Heartbeat Fix (PR #96233) — NEW FINDING
**Risk:** HIGH (heartbeat non-functional for 13+ days; fix is available in upgrade target)
- **PR #96233:** `fix(agents): run heartbeat_prompt_contribution on harness prompt builds` — merged into 2026.6.10
- This means the heartbeat prompt framework was NOT being correctly applied to harness prompt builds prior to 2026.6.10
- Combined with the undeployed cron, this is a double blocker on Heather's proactive monitoring
- Upgrading to 2026.6.10 fixes the prompt-side issue; deploying the cron in openclaw.json fixes the scheduling side
- **Related fix also in 2026.6.10:** PR #93051 `fix(cron): honor configured retry.backoffMs for recurring error backoff floor` — more reliable scheduled task retry behavior
- **Session/channel fix:** PR #96233 area also includes cron delivery awareness staying attached to the target session
- **Action:** After reaching the 2026.6.6 hop, add heartbeat cron to openclaw.json before final hop to 2026.6.10

### F4 — HIGH: iMessage Paused Day 63 — Auto-Fix via Upgrade
**Risk:** MEDIUM-HIGH (63 days without iMessage monitoring)
- iMessage monitoring paused since ~April 27, 2026 (63 days as of June 27 evening)
- inbox-state.json has a malformed duplicate key (`last_email_check_ms` appears twice) — **do NOT manually edit**
- The 2026.6.6 upgrade hop runs a SQLite migration that clears the malformed state
- iMessage monitoring expected to auto-restore after upgrade through 2026.6.6 — no manual intervention needed
- The iMessage status check (reads inbox-state.json locally) does NOT require Google Workspace and should always run

### F5 — MEDIUM: Heartbeat Cron Not Deployed — Day 13+
**Risk:** MEDIUM (Heather has no scheduled proactive monitoring — only responds when Josh messages)
- heartbeat-state.json has been all-null since June 17 (13+ days as of June 27)
- openclaw.json currently has no cron entries for heartbeat
- Requires: upgrade to 2026.6.10 (for PR #96233 fix) + add cron entry to openclaw.json
- **Sample cron config to add to openclaw.json after upgrade:**
```json
"cron": {
  "heartbeat": {
    "schedule": "0 */4 * * *",
    "prompt": "Read HEARTBEAT.md if it exists (workspace context). Follow it strictly. Do not infer or repeat old tasks from prior chats. If nothing needs attention, reply HEARTBEAT_OK.",
    "channel": "discord:1484448262290276464"
  }
}
```

### F6 — MEDIUM: Model Migration Available Now (No Upgrade Required)
**Risk:** MEDIUM (gemini-3-flash-preview is a preview model — no shutdown announced but proactive migration reduces risk)
- Primary: `google/gemini-3-flash-preview` — operational and NOT on the deprecation list as of June 27
- Preview models retire on rolling schedule with minimal notice — migrate proactively to GA stable
- **Can do now** via AlphaClaw Browse tab → openclaw.json → edit model block → save → restart:
```json
"model": {
  "primary": "google/gemini-3.5-flash",
  "fallbacks": [
    "openrouter/anthropic/claude-haiku-4-5",
    "openrouter/google/gemini-3.5-flash"
  ]
}
```
- This moves to GA stable (no shutdown risk), fixes same-provider fallback redundancy, and enables Haiku 4.5

### F7 — LOW: SOUL.md Version References Stale
**Risk:** LOW (creates confusion about which features are available now vs. after upgrade)
- SOUL.md references "OpenClaw 2026.6.6+" for gateway self-recovery and Discord duplicate handling
- Current upgrade target is 2026.6.10 — Heather is at 2026.3.22 and won't reach 2026.6.6 behavior until the upgrade
- References should be updated to "2026.6.10+" to clarify: these behaviors apply after the upgrade, not now
- See soul-improvements.md for exact wording

### F8 — LOW: BRAVE_API_KEY Missing — Web Search Unavailable
**Risk:** LOW / Capability gap
- Setting `BRAVE_API_KEY` in AlphaClaw Envars tab enables web search for Heather immediately — no upgrade needed
- Allows Heather to proactively research Bliss brand mentions, Oben HiFi news, business contacts during heartbeats
- Set via: AlphaClaw UI → Envars tab → add `BRAVE_API_KEY` = [Brave Search API key]
- Already noted in MEMORY.md as fifth priority action

### F9 — INFO: 2026.6.11-beta.1 Feature Preview (Do NOT Install)
**Status:** BETA — DO NOT INSTALL. Wait for stable.
- 2026.6.11 remains in beta as of June 27 evening. Do not install.
- Key upcoming features (for awareness):
  - Per-DM model overrides — tune per-user model without affecting guild-wide config
  - `--message-file` batch workflows — file-driven agent tasks (useful for Josh's complex requests)
  - RAFT CLI wake bridge — remote wake-up paths via CLI (reduces need for SSH for one-shot triggers)
  - Richer Discord output — improved rendering of progress drafts and command output
  - Slack relay mode, native Mattermost /oc_queue, stronger channel control
- Upgrade path: first reach 2026.6.10-stable, then monitor 2026.6.11 for stable promotion

### F10 — INFO: Noah Fleet Scope Broken — Day 18
**Status:** Fleet admin issue — no Noah coverage for 18 days
- Configured scope: `lylle-rgb/noah--repo` → returns 404 Not Found
- Actual repos confirmed: `lylle-rgb/Noahrepo2` and `lylle-rgb/Noah-workspace`
- Cannot read or write Noah's workspace files from this session
- Resolution: Fleet admin (lylle@lxrcap.com) needs to update session allowed repositories to include `lylle-rgb/Noahrepo2`

---

## Priority Action Table for Josh

| Priority | Action | Where | Effort | Impact |
|----------|--------|--------|--------|--------|
| 1 | Connect Google Workspace OAuth (Day 98) | Browser → AlphaClaw General tab | ~5 min | Restores email + calendar immediately |
| 2 | Upgrade OpenClaw to 2026.6.10 (staged) | VPS SSH → `openclaw update` | 1–2 hrs | Fixes heartbeat fix, iMessage, model routing |
| 3 | Deploy heartbeat cron in openclaw.json | Browse tab → openclaw.json edit | 10 min | Restores proactive scheduled monitoring |
| 4 | Migrate model to gemini-3.5-flash | Browse tab → openclaw.json edit | 5 min | Eliminates preview deprecation risk |
| 5 | Set BRAVE_API_KEY in Envars tab | AlphaClaw Envars tab | 2 min | Enables web search for Heather |
| 6 | Fix fleet scope: noah--repo → Noahrepo2 | Fleet admin session config | 5 min | Restores Noah monitoring (Day 18) |

---

## What Changed Since Last Scan (June 26 Evening)
- **New:** PR #96233 heartbeat_prompt_contribution fix confirmed in 2026.6.10 — direct fix for known heartbeat issue
- **Updated:** Day counts incremented (Google Workspace: 97→98, iMessage: 62→63, Noah scope: 17→18)
- **Confirmed:** 2026.6.10-stable Day 4 — no new regressions. Green light unchanged.
- **Confirmed:** 2026.6.11 still in beta only — do not install
- **No change:** Noah scope still inaccessible; all other open issues persist

---

_Generated by AlphaClaw Fleet Research Agent — 2026-06-27 Evening_
_Scan scope: josh_repo workspace files, OpenClaw changelog (2026.6.10/2026.6.11), community reports, AI assistant improvements_
