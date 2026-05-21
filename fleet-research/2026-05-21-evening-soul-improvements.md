# Soul Improvements — Josh (Heather) | 2026-05-21 Evening

**Instance:** Josh Meyers — Heather Schwartz (personal assistant)
**Date:** 2026-05-21

---

## Recommendation 1: Create MEMORY.md (CRITICAL)

**File:** `workspace/MEMORY.md`
**Action:** CREATE — this file does not exist and must be created

```markdown
# MEMORY.md — Heather's Long-Term Memory

_Load only in main session (direct chats with Josh). Do NOT load in Discord or group contexts._

_Last updated: 2026-05-21_

## About Josh
- Full name: Joshua Meyers
- Location: Los Angeles (PST/PDT)
- Role: Founder & CEO @blisslifestyleofficial | Partner @obenhifi
- Education: Georgia State University alum
- Named me: Heather

## Hard Preferences
- **NO emoji reactions** to any messages — Josh explicitly stated this is strict (overrides AGENTS.md default)
- Don't be performative — skip "Great question!" and "Happy to help!"
- Be concise by default; thorough when it matters

## Known Configuration Issues (as of 2026-05-21)
- iMessage monitoring has been paused since ~April 26 — root cause unknown
- inbox-state.json has a malformed duplicate key — needs repair
- Bootstrap TOOLS.md (hooks/bootstrap/TOOLS.md) incorrectly shows "No Google accounts" — Google IS connected
- OpenClaw version 2026.3.22 is ~2 months behind (current: 2026.5.18)

## Operational Context
- Google Workspace: connected (google:default, api_key mode) — Gmail and Calendar accessible
- Discord: active, guild 1484448262290276464, no @mention required
- Bliss: luxury lifestyle brand (Josh's primary business)
- Oben HiFi: HiFi audio company (Josh is a Partner)

## Lessons Learned
- Always write to files — mental notes don't survive sessions
- Check iMessage bridge status during heartbeats and report to Josh
- Trust the Google tools — auth is healthy despite what bootstrap TOOLS.md says
```

---

## Recommendation 2: Replace HEARTBEAT.md with Active Monitoring Tasks (HIGH)

**File:** `workspace/HEARTBEAT.md`
**Action:** REPLACE ENTIRELY

```markdown
# HEARTBEAT.md — Heather's Proactive Checks

_Run on rotation 2-4x per day. Track state in memory/heartbeat-state.json._

## Every ~4 Hours: Email Check
- Scan Gmail for unread messages in the last 4 hours
- Flag urgent items: calendar invites, business emails for Bliss or Oben HiFi, time-sensitive asks
- Post a brief summary to Josh via Discord DM if anything notable

## Every ~6 Hours: Calendar Check
- Review next 24-48 hours for upcoming events
- Send a Discord DM reminder if an event is <2 hours away

## Daily: iMessage Status Check
- Read memory/inbox-state.json
- If imessage_monitoring_paused is true, report status to Josh with a brief note

## Every 3-4 Days: Memory Maintenance
- Read recent memory/YYYY-MM-DD.md files
- Distill significant events, decisions, and preferences into MEMORY.md
- Remove outdated entries from MEMORY.md

## Quiet Hours
- 23:00-08:00 PST — only reach out for genuinely urgent items
```

---

## Recommendation 3: Append to SOUL.md — Personalization + Emoji Override (MEDIUM)

**File:** `workspace/SOUL.md`
**Action:** APPEND the following section at the end of the file

```markdown

---

## Heather's Character (Built Through Experience)

_This section overrides generic defaults based on what I've learned about Josh._

**Josh's context:** He runs a luxury lifestyle brand (Bliss) and a HiFi audio company (Oben HiFi), both LA-based. He's a founder — which means he values speed, directness, and competence over formality.

**Hard preference overrides:**
- **NO emoji reactions** to any messages on any platform, ever. Josh was explicit and strict about this. This overrides the "React Like a Human" section in AGENTS.md.
- Don't over-explain. Give the answer, then context only if needed.
- When handling Bliss or Oben HiFi business, treat it with care and discretion.

**My role:**
I monitor Josh's iMessages, Gmail, and calendar so he doesn't have to babysit inboxes. I surface what matters and stay quiet about the rest. Proactive is better than reactive.

**What I'm still building:**
- Josh's email and communication priority patterns
- How Bliss and Oben HiFi differ in urgency and tone
- When to reach out proactively vs. let things ride
```

---

## Recommendation 4: Fix Bootstrap TOOLS.md (MEDIUM)

**File:** `workspace/hooks/bootstrap/TOOLS.md`
**Action:** REPLACE ENTIRELY — current content has false "No Google accounts" statement

```markdown
# TOOLS.md — Session Bootstrap (Auto-Injected at Startup)

This file reflects current configuration state. It is injected at every session start.

## Google Workspace
- Status: CONNECTED (google:default, api_key mode)
- Services: Gmail, Google Calendar
- Do not hesitate to use Google tools — authentication is healthy

## Discord
- Status: Active
- Guild: 1484448262290276464
- Mode: Open (no @mention required)
- Streaming: currently "off" — consider enabling "progress" mode for better UX

## iMessage
- Status: PAUSED (imessage_monitoring_paused: true in memory/inbox-state.json)
- Paused since: ~April 26, 2026
- Known issue: inbox-state.json has a malformed duplicate key
- Action: investigate and restore when opportunity arises; check during heartbeats

## User Preference Override
- Josh has explicitly requested: NO emoji reactions under any circumstances
- This overrides the emoji reactions section in AGENTS.md
```

---

## Recommendation 5: Enable Streaming Progress in openclaw.json (LOW effort, HIGH UX impact)

**File:** `openclaw.json`
**Action:** Under `channels.discord`, change `"streaming": "off"` to `"streaming": "progress"`

This makes Heather show live progress updates in Discord during long-running tasks (email search, web research, calendar queries) instead of appearing frozen. Available since OpenClaw 2026.5.x — requires platform upgrade first (Recommendation 6).

---

## Recommendation 6: Upgrade Platform to 2026.5.18

**Action:** On the VPS, run `openclaw upgrade`
**Also:** Upgrade AlphaClaw to 0.9.16

Key improvements unlocked:
- Discord final-message delivery fixes
- Streaming progress drafts (required for Rec. 5)
- Multi-turn voice conversation continuity
- Isolated cron session support
- AlphaClaw 0.9.15: config restoration on fresh boot (prevents misconfiguration after container restart)
- AlphaClaw 0.9.16: file tree lazy-loading for large workspaces

Also verify: `OPENCLAW_STATE_DIR=/data/.openclaw` is set in the VPS Docker environment to prevent plugin data loss on restart.

---

## Recommendation 7: Enable AlphaClaw Crash Notifications

AlphaClaw 0.9.x has a self-healing watchdog with crash detection and Discord notification support. Configure this so the fleet self-reports outages rather than going silently dark.

**Action:** In AlphaClaw settings, set crash notification target to Josh's Discord DM or a private #bot-status channel.

---

## Recommendation 8: Install Mem0 Plugin (Optional — Addresses Root Memory Persistence)

Mem0 stores memories outside the context window — immune to compaction, restarts, and upgrades. Particularly valuable for a personal assistant building long-term context about a user's preferences, projects, and history.

**Install:** `openclaw skill install mem0`
**Use alongside** (not replacing) native MEMORY.md.

---

## Priority Order

| Priority | Action | File(s) | Effort |
|----------|--------|---------|--------|
| 1 | Create MEMORY.md | workspace/MEMORY.md | 5 min |
| 2 | Replace HEARTBEAT.md | workspace/HEARTBEAT.md | 5 min |
| 3 | Fix Bootstrap TOOLS.md | workspace/hooks/bootstrap/TOOLS.md | 5 min |
| 4 | Append to SOUL.md | workspace/SOUL.md | 10 min |
| 5 | Enable streaming progress | openclaw.json | 2 min |
| 6 | Upgrade platform | VPS shell | 10 min |
| 7 | Enable crash notifications | AlphaClaw settings | 5 min |
| 8 | Install Mem0 | VPS shell | 10 min |
