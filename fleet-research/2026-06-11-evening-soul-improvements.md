# Soul Improvements — Josh (Heather Schwartz) | 2026-06-11 Evening

**Based on:** Evening scan findings, Day 81 gap analysis, platform research
**Date:** 2026-06-11
**Instance:** Josh Meyers — Heather Schwartz
**Prior improvements doc:** fleet-research/2026-06-09-evening-soul-improvements.md

---

## Context

The June 9 evening soul-improvements doc remains fully valid and unimplemented. This doc **complements** it with new recommendations derived from tonight's research — specifically: iMessage architecture clarification, the Nylas CLI fallback path, Memory Wiki import bootstrap, cron-based morning briefings, and every-turn group-chat context behavior.

**All June 9 recommendations (MEMORY.md stub, HEARTBEAT.md population, SOUL.md domain section, AGENTS.md emoji fix, USER.md business context) remain the highest-priority actions and should be applied first.**

---

## New Recommendation A: TOOLS.md — Document iMessage Architecture Constraint

**Why:** The current TOOLS.md is a blank template. Tonight's research clarified that iMessage requires macOS (AppleScript bridge) and **will never work on a Linux VPS** without a separate Mac bridge host. This constraint should be documented to prevent future confusion and time spent debugging.

**Replace workspace/TOOLS.md with:**

```markdown
# TOOLS.md — Heather's Setup Notes

## Communication Channels

### Discord
- **Status:** Active ✓
- **Guild:** 1484448262290276464
- **Streaming:** Currently `"off"` — upgrade to `"progress"` recommended (see fleet-research)

### iMessage
- **Status:** PAUSED — architecture constraint
- **Reason:** iMessage integration requires macOS (AppleScript bridge). The current host is a
  Linux VPS (5.78.142.81). iMessage cannot run on Linux — no Apple API is available.
- **To enable:** Would require a macOS machine running OpenClaw's iMessage channel connector,
  forwarding to this VPS gateway. Not yet set up.
- **Alternative:** Email via Gmail (once Google Workspace connected) covers most of the same
  use cases for business communications.

## Email & Calendar

### Google Workspace (gog CLI)
- **Status:** NOT CONNECTED — setup required
- **Setup path:** AlphaClaw UI → General tab → Google Workspace → Connect
  - Requires Google Cloud Console OAuth credentials
  - Services needed: Gmail, Calendar, Contacts, Tasks
  - After connection, use: `gog --client default --account <email> <command>`
- **Skill docs:** `skills/gog-cli/SKILL.md` (when installed)

### Nylas CLI (Alternative Path)
- **Status:** Not installed — available if gog setup remains blocked
- **When to use:** If Google Workspace OAuth setup is blocked for more than 3 days
- **Advantage:** Simpler auth flow (no GCP project required), supports Gmail, Outlook,
  Exchange, Yahoo, iCloud, IMAP — 72+ commands
- **Install:** `openclaw skills install nylas-cli`
- **Docs:** https://cli.nylas.com/guides/nylas-openclaw-personal-assistant

## Environment

- **Host:** VPS at 5.78.142.81.sslip.io (Linux)
- **OpenClaw version:** 2026.3.22 (upgrade to 2026.6.5 pending — 81 days behind)
- **AlphaClaw UI:** https://5.78.142.81.sslip.io
- **Primary model:** google/gemini-3-flash-preview

## Notes

- No TTS/voice preferences configured yet
- No SSH aliases configured yet
- No camera/device integrations configured yet
```

**Risk level:** LOW — documentation only.

---

## New Recommendation B: HEARTBEAT.md — Add Cron-Backed Morning Briefing

**Why:** The June 9 soul-improvements doc recommended populating HEARTBEAT.md with periodic checks. Tonight's research confirmed OpenClaw now supports **natural language cron scheduling** ("Every weekday at 8 AM"). For a personal assistant, cron is more reliable than heartbeat for time-critical tasks (morning briefings must fire at 8 AM sharp, not "sometime in the morning").

**Recommended split:**

Use **cron** for: time-exact morning briefing (8 AM PST weekdays)
Use **heartbeat** for: background monitoring throughout the day (email triage, calendar watch)

**cron.json entry to add (VPS-side via AlphaClaw UI or direct file edit):**
```json
{
  "jobs": [
    {
      "name": "morning-briefing",
      "schedule": "Every weekday at 8:00 AM",
      "timezone": "America/Los_Angeles",
      "prompt": "Generate Josh's morning briefing: (1) Check Gmail for unread messages from the last 12 hours — flag any from business contacts or marked urgent. (2) Check Google Calendar for events in the next 48 hours. (3) Summarize in 3-5 bullet points. Send to Discord guild 1484448262290276464. If Google Workspace not yet connected, skip and reply HEARTBEAT_OK.",
      "channel": "discord:1484448262290276464",
      "model": "google/gemini-3-flash-preview"
    }
  ]
}
```

**Note:** This cron job will silently no-op until Google Workspace is connected, since it handles the "not connected" case gracefully. Safe to add now.

**Revised HEARTBEAT.md for background monitoring (complement to cron, per June 9 Recommendation 3):**

```markdown
# HEARTBEAT.md

## Background Monitoring (active 8 AM – 11 PM PST)

### Email Watch (run when heartbeat fires mid-day)
- Check Gmail inbox for messages since the last check
- Flag: anything from a known business contact or with URGENT subject
- Summarize urgently if warranted; HEARTBEAT_OK otherwise
- Track last check time in memory/heartbeat-state.json

### Calendar Watch (run 1-2x per day)
- Check for events starting within 2 hours — send reminder
- Check for new events added since last check
- Flag any event with missing dial-in or location
- HEARTBEAT_OK if nothing new

## Note
- Google Workspace not yet connected — checks will no-op until setup complete
- Morning briefing runs via cron at 8 AM PST (see cron.json) — do not duplicate here
- Quiet hours: do not initiate contact 11 PM – 8 AM PST
- Track state in memory/heartbeat-state.json
```

**Risk level:** LOW — additive; cron no-ops gracefully until Google Workspace is connected.

---

## New Recommendation C: SOUL.md — Add Continuity Note on Memory Wiki

**Why:** MEMORY.md is Day 81 missing. The June 9 doc recommended creating a manual stub. Tonight's research revealed a faster path: OpenClaw's Memory Wiki import feature can pull in prior AI conversation history from ChatGPT or other platforms. If Josh has prior AI assistant history, this could populate MEMORY.md much faster than manual writing.

**Append to workspace/SOUL.md (under the Continuity section):**

```markdown
## Seeding Your Memory

Creating MEMORY.md from scratch is slow. There are two faster paths:

**Option 1 — Memory Wiki Import:**
If Josh has prior ChatGPT or AI assistant conversations containing context about Bliss,
Oben HiFi, preferences, or personal context, those can be imported via:
AlphaClaw UI → OpenClaw dashboard → Memory → Import (or Memory Palace tab)
This imports insights directly into the memory system.

**Option 2 — Manual Stub (immediate):**
Create workspace/MEMORY.md with:
- Josh's basic context (name, role, timezone, communication rules)
- Platform state (what's connected, what's broken)
- What you know from onboarding and prior sessions
Then let Dreaming and daily notes enrich it over time.

Either way — MEMORY.md must exist before Dreaming can run. Create it today.
```

**Risk level:** LOW — documentation only.

---

## New Recommendation D: AGENTS.md — Document Every-Turn Context Injection

**Why:** As of OpenClaw v2026.2.15+, workspace context (SOUL.md, USER.md, AGENTS.md rules) is injected on **every turn** in group chats — not just the first message. This is a significant behavioral guarantee that Heather should know about and rely on, especially for:
- The NO emoji reactions rule re-enforcing itself on every Discord message
- USER.md preferences staying active throughout long conversations
- SOUL.md vibe instructions not drifting mid-session

**Append to workspace/AGENTS.md group-chats section:**

```markdown
### 🔄 Context Stays Fresh

As of OpenClaw 2026.2.15+, your workspace context (SOUL.md, USER.md, AGENTS.md) is
re-injected on every turn in group chats — not just at session start.

**What this means for you:**
- You can rely on your instructions being active throughout a long Discord thread
- The NO emoji reaction rule from USER.md re-enforces every message — no drift
- If you feel confused mid-conversation, your context files are being re-applied on the next reply

**What this does NOT mean:**
- It doesn't replace reading MEMORY.md and daily notes on session start
- It doesn't persist new information — still write significant things to files
- It doesn't replace your own judgment about when to speak vs. stay quiet
```

**Risk level:** LOW — documentation only.

---

## New Recommendation E: USER.md — Add iMessage Architecture Note

**Why:** USER.md notes that iMessage is part of Josh's expected setup but there's no explanation of why it's paused. Adding a factual note prevents future sessions from repeatedly investigating the pause.

**Append to workspace/USER.md after the Notes line:**

```markdown
## Platform Notes

**iMessage:** Currently paused (inbox-state.json: `imessage_monitoring_paused: true`).
Architecture constraint: the VPS host (5.78.142.81) is Linux; iMessage requires macOS.
Not currently resolvable without a Mac bridge machine. Focus communication on Discord + email.

**Google Workspace:** Not yet connected. Setup requires OAuth credentials in Google Cloud
Console. Once connected, gog-cli provides Gmail, Calendar, Contacts, Tasks.
```

**Risk level:** LOW — documentation only.

---

## Implementation Priority (Tonight's Additions Only)

**First, apply all June 9 recommendations** (MEMORY.md stub, HEARTBEAT.md, SOUL.md domain section, AGENTS.md emoji fix, USER.md business context). Those remain higher priority.

Then apply tonight's:

| Priority | Action | File | Effort | VPS? |
|----------|--------|------|--------|------|
| 6 | Document TOOLS.md with real setup state | workspace/TOOLS.md | 10 min | No |
| 7 | Add SOUL.md memory seeding note | workspace/SOUL.md | 5 min | No |
| 8 | Update AGENTS.md with every-turn context note | workspace/AGENTS.md | 5 min | No |
| 9 | Add iMessage architecture note to USER.md | workspace/USER.md | 5 min | No |
| 10 | Add cron morning briefing (cron.json) | cron.json | 5 min | VPS/GitHub |
| 11 | Revise HEARTBEAT.md for background-only (cron handles morning) | workspace/HEARTBEAT.md | 5 min | No |
| 12 | Install Nylas CLI (if Google Workspace still blocked in 3+ days) | VPS | 10 min | Yes |

---

## Cumulative Open Workspace Issues

| File | Status | Days Outstanding |
|------|--------|-----------------|
| MEMORY.md | MISSING | **81** |
| HEARTBEAT.md | Empty (no tasks) | **81** |
| SOUL.md | Generic default, no domain section | **81** |
| AGENTS.md | Emoji rule contradicts USER.md | **81** |
| USER.md | Missing business context depth | **81** |
| TOOLS.md | Template only (no real environment docs) | 3 |
| BOOTSTRAP.md | Should be deleted | 3 |
