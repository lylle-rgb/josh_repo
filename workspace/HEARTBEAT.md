# HEARTBEAT.md — Heather's Proactive Checks

_Run on rotation 2–4x per day. Track state in memory/heartbeat-state.json._
_Last updated: 2026-06-27 (fleet research agent — updated day counts for June 27 evening)_

> ⚠️ **CRON NOT DEPLOYED (as of June 27, 2026):** The heartbeat cron is NOT running on the VPS.
> heartbeat-state.json has been all-null since June 17 (13 days as of June 27). You do not receive
> scheduled heartbeat triggers until Josh adds the cron to `openclaw.json` and upgrades to
> **2026.6.10-stable** (released June 24, 2026 — see fleet-research/findings.md for staged path).
> Until then: run checks manually when Josh messages you. Remind Josh **once per main session** that
> proactive monitoring is not running on schedule.

## Every ~4 Hours: Email Check
- Scan Gmail for unread messages in the last 4 hours
- If Google Workspace not connected: skip silently, but mention it to Josh once per day at morning check
- Flag urgent items: calendar invites, business emails for Bliss or Oben HiFi, time-sensitive asks
- Post a brief Discord DM summary if anything notable

## Every ~6 Hours: Calendar Check
- Review next 24–48 hours for upcoming events
- Send a Discord DM reminder if an event is <2 hours away
- If Google Workspace not connected: skip silently

## Daily: iMessage Status Check
- Read memory/inbox-state.json
- If imessage_monitoring_paused is true: report status to Josh with a brief note
- Do NOT manually edit inbox-state.json (SQLite migration on upgrade to 2026.6.6 will handle it)

## Every 3–4 Days: Memory Maintenance
- Read recent memory/YYYY-MM-DD.md files
- Distill significant events, decisions, and preferences into MEMORY.md
- Remove outdated entries from MEMORY.md

## Josh's Heartbeat Schedule (Approximate — LA Time, PST/PDT)
- **Morning (~9:00 AM PST):** Email + calendar day preview
- **Midday (~1:00 PM PST):** Urgent emails, afternoon events
- **Evening (~6:00 PM PST):** Today's summary, prep for tomorrow
- **Overnight (11:00 PM–8:00 AM PST):** Silent unless genuinely urgent

## State Tracking
Update memory/heartbeat-state.json after each check:

```json
{
  "lastChecks": {
    "email": null,
    "calendar": null,
    "imessage": null,
    "memory_maintenance": null,
    "contacts": null
  }
}
```

## Quiet Hours
- 23:00–08:00 PST — only reach out for genuinely urgent items

## Google Workspace Status Note
As of June 2026: Google Workspace OAuth is NOT connected (Day 98 as of June 27 — **Day 100 arrives June 29 (2 days)**).
Josh needs to authorize at https://5.78.142.81.sslip.io#general (AlphaClaw General tab) for Gmail and
Calendar to work. Until then, email and calendar checks will silently no-op. Remind Josh once at morning check.
