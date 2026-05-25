# Soul Improvements — Josh (Heather Schwartz) | 2026-05-25 Evening

**Date:** 2026-05-25
**Instance:** Josh Meyers — Heather Schwartz (personal assistant)
**Based on:** findings-2026-05-25-evening.md + full workspace analysis

---

## Overview

This is the third consecutive soul improvements document recommending the same four structural fixes — all of which remain unapplied. Today's new additions:
- A concrete guide for integrating the meeting capture plugin when the upgrade lands
- Exact config to clean the dead OpenRouter fallback (30-second fix, no restart)
- Reinforced priority ordering with explicit Day counts to convey urgency

All four core fixes (MEMORY.md, HEARTBEAT.md, SOUL.md personalization, dead fallback) can be applied without a platform upgrade. The meeting capture guide is post-upgrade prep.

---

## Recommendation 1 — Create MEMORY.md (CRITICAL — addresses JOSH-30, Day 37+)

Heather is instructed every session startup to read `workspace/MEMORY.md`. It does not exist. 37+ days of context is being lost on every restart.

**File to create:** `workspace/MEMORY.md`

```markdown
# MEMORY.md — Heather's Long-Term Memory

_Last reviewed: 2026-05-25_
_Load ONLY in main sessions (direct chat with Josh). Do NOT load in Discord or group channels — this file contains personal context._

---

## About Josh

- **Full name:** Joshua Meyers
- **Call him:** Josh
- **Location:** Los Angeles, California (PST/PDT)
- **Companies:** Founder & CEO @blisslifestyleofficial (luxury lifestyle brand); Partner @obenhifi (audio)
- **Education:** Georgia State University alum
- **Discord:** Guild 1484448262290276464; requireMention: false

## Josh's Explicit Preferences — Non-Negotiable

- **NO emoji reactions.** Josh explicitly asked. Not in Discord, not in any channel. Ever. This overrides AGENTS.md's "React Like a Human" section.
- **Concise over verbose.** Get to the point. No filler phrases.
- **No half-formed drafts to messaging surfaces.** If uncertain, confirm before sending.

## My Setup (Heather)

- **Name:** Heather — named by Josh during onboarding.
- **Primary human:** Josh (confirmed by L X).
- **Google:** Connected via api_key mode (google:default). Services: Gmail, Calendar, Drive, Contacts, Tasks. Use `--client google --account default` for gog commands.
- **iMessage:** Bridge previously operational; monitoring currently paused (`imessage_monitoring_paused: true` in inbox-state.json). Verify bridge status before re-enabling.
- **Discord:** Guild 1484448262290276464. requireMention: false.
- **Model:** google/gemini-3-flash-preview (primary); openrouter/google/gemini-2.5-flash as fallback.
- **OpenClaw version:** 2026.3.22 (as of last check; target is 2026.5.20 — pending upgrade)

## Business Context

- **Bliss:** Luxury lifestyle brand. Premium positioning. Content should reflect high taste — thoughtful, not cheap or spammy.
- **Oben HiFi:** Audio company. Josh is a partner.
- When helping with content, emails, or communications for these companies, match the premium brand voice.

## Technical Notes

- Josh gave onboarding feedback: search bar should come first in Google UI; button placement on OAuth consent screen.
- inbox-state.json has a duplicate key issue (malformed JSON) — be aware when reading/writing it.
- Bootstrap hook files at `hooks/bootstrap/` — verify they exist on VPS before relying on them.

## Open Items (as of 2026-05-25)

- OpenClaw upgrade to 2026.5.20 pending (3 days overdue)
- iMessage monitoring paused — reason unknown, verify bridge health before resuming
- HEARTBEAT.md is empty — no proactive monitoring configured
- Dead fallback `openrouter/anthropic/claude-3.5-haiku` in openclaw.json — remove
- Bootstrap hook files at hooks/bootstrap/ — verify exist on VPS
```

**Risk level:** None — configuration file, no external actions.

---

## Recommendation 2 — Add HEARTBEAT.md Content (HIGH — addresses JOSH-31, Day 37+)

HEARTBEAT.md contains only template comments. Every heartbeat fires and returns HEARTBEAT_OK. No proactive monitoring occurs.

**Replace `workspace/HEARTBEAT.md` with:**

```markdown
# HEARTBEAT.md — Heather's Proactive Checks

## Active Checks (rotate through, 2 per heartbeat max)

### Gmail
Check for urgent or important unread emails in the last 2 hours.
- Skip if last email check was <2 hours ago (see memory/heartbeat-state.json)
- Flag: anything requiring Josh's response within 24h
- Flag: calendar invites, Bliss/Oben partner emails, time-sensitive messages
- Do NOT flag: newsletters, promotional emails, notifications

### Google Calendar
Check for events starting in the next 2 hours.
- Alert Josh if event starts in <1 hour and he hasn't been notified yet
- Note: skip if no events upcoming today

## Rules

- **Quiet hours:** No alerts 23:00–08:00 PST unless urgent
- **Throttle:** Don't repeat the same check within 2 hours
- **Default:** If nothing actionable → reply HEARTBEAT_OK
- **Track state:** Update memory/heartbeat-state.json with unix timestamps after each check
- **NO emoji reactions** in any heartbeat output — Josh's explicit preference

## Heartbeat State Template

Create `memory/heartbeat-state.json` if it doesn't exist:
```json
{
  "lastChecks": {
    "gmail": null,
    "calendar": null
  },
  "lastAlert": null
}
```
```

**Risk level:** LOW — read-only checks; no email sent, no calendar events created without Josh asking.

---

## Recommendation 3 — Add Josh's Rules to SOUL.md (MEDIUM — addresses JOSH-34, JOSH-37)

SHA `792306ac60f6c600b8ded97899354557ce900f40` = upstream generic template. The emoji contradiction (USER.md: no emoji; AGENTS.md: React Like a Human) is unresolved and will cause incorrect behavior.

**Add this section after `## Vibe` in `workspace/SOUL.md`:**

```markdown
## Who I Am For Josh — Non-Negotiable Rules

These override any general guidance elsewhere in this file or in AGENTS.md:

1. **No emoji reactions.** Josh explicitly asked. Not in Discord, not in any channel. The "React Like a Human" section in AGENTS.md does **not** apply to Josh's sessions.
2. **LA timezone always.** Josh is in Los Angeles. All times are PST/PDT.
3. **Premium voice.** Josh runs Bliss (luxury lifestyle) and Oben HiFi (audio). Communications should reflect premium taste — thoughtful, concise, never cheap or spammy.
4. **Extra caution on external actions.** Josh has given iMessage, email, and calendar access. Always confirm intent before sending anything from those surfaces.
5. **Competence over charm.** Heather was chosen to be sharp and resourceful. Demonstrate it through action, not personality performance.
```

**Risk level:** LOW — refines existing soul guidance; the behavioral change is enforcing the no-emoji rule more explicitly.

---

## Recommendation 4 — Remove Dead OpenRouter Fallback (MEDIUM — addresses JOSH-50, Day 17)

The dead fallback `openrouter/anthropic/claude-3.5-haiku` has been in the fallback chain for 17+ days. After the 2026.5.20 upgrade, model failover exports diagnostic OTLP events — the dead fallback will generate persistent diagnostic noise.

**Change in `openclaw.json`** — update `agents.defaults.model.fallbacks`:

**Current:**
```json
"fallbacks": [
  "openrouter/google/gemini-2.5-flash",
  "openrouter/anthropic/claude-3.5-haiku"
]
```

**After:**
```json
"fallbacks": [
  "openrouter/google/gemini-2.5-flash"
]
```

Optional: add a live Claude fallback (e.g., `openrouter/anthropic/claude-3.5-sonnet`) if Claude-quality fallback is desired.

**No restart required.** OpenClaw reads the fallback config at routing time.

**Risk level:** LOW — minor config edit, no downtime.

---

## Recommendation 5 — Meeting Capture Plugin Integration Guide (INFO — post-upgrade prep, addresses JOSH-46)

Once Josh upgrades to 2026.5.20, the meeting capture plugin is available as an external install. This is the highest-value new capability for Heather given Josh's Discord voice usage.

**Step-by-step activation plan (post-upgrade):**

### Step 1: Verify memory-core is active
Add to `openclaw.json` `plugins.entries`:
```json
"memory-core": {
  "enabled": true,
  "config": {
    "scope": "admin"
  }
}
```
Also add `"memory-core"` to `plugins.allow` array.

### Step 2: Install the meeting-notes plugin
```bash
openclaw plugins install @openclaw/meeting-notes
```
(Package name TBC when stable — verify against 2026.5.20 release notes)

### Step 3: Configure Discord voice as capture source
In `openclaw.json`, add to `plugins.entries`:
```json
"meeting-notes": {
  "enabled": true,
  "config": {
    "sources": ["discord-voice"],
    "autoStart": true,
    "retention": "30d"
  }
}
```

### Step 4: Update MEMORY.md to reference voice context
Add to MEMORY.md:
```markdown
## Voice Session Context
Meeting capture enabled for Discord voice. Josh's voice sessions are
automatically transcribed and available in memory. Reference transcript
context when Josh follows up on topics discussed in voice.
```

### Step 5: Test
Join a brief Discord voice session with Josh. After the session ends, run:
```bash
openclaw meeting-notes list
```
Verify a transcript entry was created.

**Risk level of preparation:** None — this is a planning document for post-upgrade. No changes needed today.

---

## Priority Execution Order

| # | Action | Impact | Risk | Requires Upgrade? |
|---|--------|--------|------|-------------------|
| 1 | Remove dead fallback from openclaw.json (JOSH-50) | MEDIUM | None | No — 30 seconds |
| 2 | Create workspace/MEMORY.md (JOSH-30) | CRITICAL | None | No |
| 3 | Update workspace/HEARTBEAT.md (JOSH-31) | HIGH | Low | No |
| 4 | Add Josh's Rules to workspace/SOUL.md (JOSH-34, JOSH-37) | MEDIUM | Low | No |
| 5 | Verify bootstrap hook files on VPS (JOSH-41) | HIGH | None | No |
| 6 | Upgrade OpenClaw to 2026.5.20 (JOSH-39) | HIGH | Medium | — |
| 7 | Activate memory-core plugin post-upgrade | HIGH | Low | Yes |
| 8 | Install meeting-notes plugin + configure Discord voice | HIGH | Low | Yes |

Items 1–5 can all be done **right now** with zero risk. No upgrade needed. The longer these wait, the more context accumulates that will never be recovered.
