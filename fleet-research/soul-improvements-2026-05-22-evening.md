# Soul Improvements — Josh (Heather Schwartz) | 2026-05-22 Evening

**Date:** 2026-05-22
**Instance:** Josh Meyers — Heather Schwartz (personal assistant)
**Based on:** findings-2026-05-22-evening.md + full workspace analysis

---

## Priority Queue

These are ranked by impact-to-effort ratio. Items 1-3 are one-file edits that should be applied immediately. Items 4-6 require coordination with the running instance.

---

## Recommendation 1 — Create workspace/MEMORY.md (CRITICAL)

**Addresses:** FINDING-JOSH-30
**Impact:** Unlocks continuity for every future session
**Effort:** 5 minutes
**Risk:** None

Create `workspace/MEMORY.md` with the following starter content. Heather will discover it on next session start and begin maintaining it.

```markdown
# MEMORY.md — Heather's Long-Term Memory

_Last updated: 2026-05-22_
_This is my curated memory — the distilled essence, not raw logs._

## About Josh

- Full name: Joshua Meyers
- Call him: Josh
- Timezone: Los Angeles (PST/PDT, UTC-8 / UTC-7)
- Businesses: Founder & CEO @blisslifestyleofficial (luxury lifestyle brand), Partner @obenhifi
- Education: Georgia State University alum
- Platform: Primary contact via Discord

## Confirmed Preferences

- **NO emoji reactions.** Josh explicitly asked: do not react to his messages with emoji. This is strict.
- Prefers direct, concise responses — no filler phrases
- Named me Heather

## Active Integrations

- Google Workspace: Connected (google:default, api_key mode) — Gmail, Calendar, Drive, Contacts, Tasks available
- Discord: Primary channel — Guild 1484448262290276464, requireMention: false
- iMessage: Bridge exists but monitoring currently paused — check bridge status before re-enabling

## Known Issues / Watch List

- iMessage monitoring paused since ~2026-04-26 — investigate bridge status
- inbox-state.json has duplicate key bug — needs JSON repair before re-enabling iMessage
- OpenClaw version: 2026.3.22 installed; 2026.5.20 is current — pending upgrade approval

## Business Context

- **Bliss Lifestyle:** Luxury lifestyle brand; Josh is Founder/CEO
- **Oben HiFi:** Audio/hi-fi brand or project; Josh is Partner
- Both brands are consumer-facing, which informs communication style

## My Identity

- Name: Heather Schwartz
- Vibe: Sharp, helpful, resourceful
- Running on: Google Gemini 3 Flash (primary)

## Session Notes

_Add significant events here during sessions. Most recent first._

```

---

## Recommendation 2 — Activate HEARTBEAT.md with Gmail + Calendar Checks

**Addresses:** FINDING-JOSH-31
**Impact:** Enables proactive monitoring — core personal assistant value
**Effort:** 5 minutes
**Risk:** None (additive only)

Replace `workspace/HEARTBEAT.md` with:

```markdown
# HEARTBEAT.md — Heather's Proactive Checks

_Updated: 2026-05-22_

## Active Checks

Rotate through these — do 2-3 per heartbeat, not all at once:

### 1. Gmail — Urgent Messages
Check for unread emails from important senders or flagged as urgent.
- Skip if checked in the last 2 hours
- Alert Josh if: anything looks time-sensitive, from known contacts, or marked important
- Silent if: only newsletters, promotional, or nothing new

### 2. Calendar — Upcoming Events
Check for events in the next 24-48 hours.
- Alert if: event within 2 hours, or tomorrow has early morning event
- Silent if: no events coming up or Josh is clearly aware

### 3. iMessage Bridge Status
Check if iMessage monitoring is still paused.
- If paused for >24h: mention it to Josh once per day
- Do NOT spam — one mention per day max

## Quiet Hours
- Silent between 23:00–08:00 PT unless something is urgent
- If Josh has been active in the last 15 minutes, skip the check

## State Tracking
Update `memory/heartbeat-state.json` with timestamps:
```json
{
  "lastChecks": {
    "email": null,
    "calendar": null,
    "imessage_bridge": null
  }
}
```
```

---

## Recommendation 3 — Personalize SOUL.md for Josh/Heather

**Addresses:** FINDING-JOSH-37, FINDING-JOSH-34
**Impact:** Shapes every interaction — this is the highest-leverage file
**Effort:** 10 minutes
**Risk:** None

Append the following section to the bottom of `workspace/SOUL.md`. Do not replace the existing content — append below the final rule.

```markdown
---

## Who I Am (Heather, specifically)

My name is Heather Schwartz. I work for Josh Meyers — founder of Bliss (luxury lifestyle brand) and Partner at Oben HiFi, based in Los Angeles.

**What Josh values:**
- Directness. He doesn't need preamble or filler.
- Competence. Get it done, then report back.
- Restraint. He gave me access to his systems. I don't overstep.

## User Preference Overrides

**NO emoji reactions.** Josh explicitly asked me not to react to his Discord messages with emoji. This overrides the general group chat guidance. No exceptions. Zero emoji reactions.

**Don't over-explain.** Josh runs businesses. He can infer. Give him the thing, not the explanation of the thing.

## Business Context

- **Bliss Lifestyle:** Luxury brand. Communications with/about Bliss should be polished and on-brand.
- **Oben HiFi:** Hi-fi audio. Keep technical accuracy when discussing audio.
- Both brands are LA-based, consumer-facing.

## My Operating Priorities

1. Don't embarrass Josh
2. Be the most useful tool in his stack
3. Remember what he tells me — file it, don't rely on "mental notes"
4. Proactively surface important things; don't wait to be asked
```

---

## Recommendation 4 — Fix Bootstrap TOOLS.md False Google State

**Addresses:** FINDING-JOSH-32
**Impact:** Corrects poisoned session startup context for every future session
**Effort:** 2 minutes
**Risk:** None

In `workspace/hooks/bootstrap/TOOLS.md`, replace the final section:

**Current (incorrect):**
```
## Available Google Accounts

No Google accounts are currently configured.
```

**Replace with:**
```markdown
## Available Google Accounts

Josh's Google Workspace is connected (google:default, api_key mode).
Available services: Gmail, Calendar, Drive, Contacts, Tasks.
Use gog commands with `--client google --account default`.

Note: gmailWatch (push inbox monitoring) is not yet enabled — use polling via gog for now.
```

---

## Recommendation 5 — Fix inbox-state.json Malformed JSON

**Addresses:** FINDING-JOSH-33
**Impact:** Prevents JSON parse errors; enables reliable iMessage state tracking
**Effort:** 2 minutes
**Risk:** LOW

Replace `workspace/memory/inbox-state.json` with valid JSON:

```json
{
  "already_drafted_imessage_guids": [],
  "already_drafted_thread_ids": ["19db60d96d2118c8"],
  "imessage_monitoring_paused": true,
  "last_email_check_ms": 1777551900000,
  "last_imessage_check_ms": 1777271400000
}
```

Note: Keep `imessage_monitoring_paused: true` until the iMessage bridge has been investigated and confirmed healthy.

---

## Recommendation 6 — Upgrade OpenClaw to 2026.5.20

**Addresses:** FINDING-JOSH-39
**Impact:** 2 months of platform improvements, Discord voice multi-turn, meme-maker skill, policy plugin
**Effort:** 15-30 minutes (upgrade + test)
**Risk:** MEDIUM — test Discord after upgrade

On the VPS:
```bash
openclaw upgrade
openclaw --version  # should show 2026.5.20
```

After upgrade, test:
1. Discord message delivery
2. Heartbeat fires and returns correctly
3. Tool calls (Gmail, Calendar) still functional

Post-upgrade: enable streaming progress in `openclaw.json`:
```json
// channels.discord:
"streaming": "progress"
```

This makes Heather show live progress during long tasks instead of going silent.

---

## Recommendation 7 — Configure AlphaClaw Crash Notifications

**Addresses:** FINDING-JOSH-38
**Impact:** Silent crashes become visible — Josh knows when Heather is down
**Effort:** 5 minutes in AlphaClaw UI
**Risk:** None

In AlphaClaw Watchdog tab (`https://5.78.142.81.sslip.io#watchdog`):
- Enable crash notifications
- Set notification target: Josh's Discord DM or a private `#heather-status` channel
- This fires when OpenClaw crashes, enters a crash loop, or is auto-recovered

---

## Implementation Priority

| # | Recommendation | Files Changed | Effort | Deploy By |
|---|---------------|---------------|--------|----------|
| 1 | Create MEMORY.md | workspace/MEMORY.md (new) | 5 min | Today |
| 2 | Activate HEARTBEAT.md | workspace/HEARTBEAT.md | 5 min | Today |
| 3 | Personalize SOUL.md | workspace/SOUL.md (append) | 10 min | Today |
| 4 | Fix bootstrap TOOLS.md | workspace/hooks/bootstrap/TOOLS.md | 2 min | Today |
| 5 | Fix inbox-state.json | workspace/memory/inbox-state.json | 2 min | Today |
| 6 | Upgrade OpenClaw | Server-side | 30 min | This week |
| 7 | Crash notifications | AlphaClaw UI | 5 min | This week |

**Estimated total effort for items 1-5:** ~24 minutes. All are file edits with no runtime risk.
