# Soul Improvements — Josh / Heather Schwartz — Evening Scan

**Scan Date:** 2026-05-17 (Evening — Day 30)
**Agent:** AlphaClaw Apex Fleet Research Agent
**Based On:** findings-2026-05-17-evening.md (Findings 61–65) + 30-day backlog

---

## Status: All Prior Recommendations Still Unimplemented (Day 30)

No soul improvement recommendation from any scan (Day 12–29) has been applied. Thirteen recommendations, zero implementations, thirty days. This document adds new items driven by today's findings and provides additional ready-to-paste content.

---

## NEW: HEARTBEAT.md — First-Time Configuration (Ready to Paste)

**File:** `workspace/HEARTBEAT.md`
**Effort:** 5 minutes to paste; activation after OpenClaw upgrade + Google account connected
**Urgency:** Design now — best time to prepare given new retry-aware cron in 2026.5.10

Replace the current empty HEARTBEAT.md content with:

```
# HEARTBEAT.md — Heather Active Tasks

# Josh's timezone: America/Los_Angeles (PDT/PST)
# Quiet hours: 10 PM – 7 AM PDT. Reply HEARTBEAT_OK during quiet hours unless something is genuinely urgent.

## Email Scan
- Check Gmail INBOX for unread messages since last check.
- Flag anything from known business contacts (Bliss, Oben HiFi, direct partners) or time-sensitive subject lines.
- Update workspace/memory/heartbeat-state.json with current timestamp after check.
- Alert Josh only if something is genuinely urgent or time-sensitive.
- If nothing urgent: HEARTBEAT_OK.

## Calendar Scan
- Check for events in the next 24–48 hours.
- Alert if: event in next 2 hours with no reminder, conflicts exist, or an event was recently changed.
- Keep alerts to one bullet: "Heads up: you have [thing] at [time] today."
- If calendar is clear: HEARTBEAT_OK.

## Inbox-State Health Check
- Verify workspace/memory/inbox-state.json parses cleanly (no syntax errors).
- Verify imessage_monitoring_paused is false.
- If paused: notify Josh and ask whether to resume.

## State Tracking
After each check cycle, update workspace/memory/heartbeat-state.json:
{
  "lastChecks": {
    "email": [unix_ms],
    "calendar": [unix_ms],
    "inboxStateHealth": [unix_ms]
  }
}

## Quiet Rule
If all checks return nothing new, reply HEARTBEAT_OK.
Do not manufacture updates. Do not reach out between 10 PM and 7 AM PDT.
```

---

## NEW: TOOLS.md — Actual Tool Inventory (Needed Before Permission Preflights Work)

**File:** `workspace/TOOLS.md`
**Effort:** 10 minutes
**Urgency:** Before OpenClaw upgrade to 2026.5.9+ (permission preflight reads from TOOLS.md)

Replace the entire current TOOLS.md content with:

```markdown
# TOOLS.md — Heather's Tool Notes

## gog-cli (Google Workspace CLI)
- **Account:** Josh's Google account (OAuth NOT YET CONNECTED as of 2026-05-17)
- **Connection URL:** https://5.78.142.81.sslip.io#general (AlphaClaw UI — connect here)
- **Once connected — capabilities:** Gmail read/write, Calendar read/write, Contacts read/write, Drive, Meet
- **Gmail:** Primary inbox monitoring. Check for urgent messages 2–3x/day via heartbeat.
- **Calendar:** Check for upcoming events. Alert Josh if event in next 2h or conflict detected.
- **Contacts:** Read freely. Edit only with Josh's explicit confirmation.
- **STATUS:** NOT CONNECTED. All gog tool calls are currently non-functional.

## iMessage (Mac integration)
- **Status:** Monitoring PAUSED (inbox-state.json: imessage_monitoring_paused: true as of ~2026-05-07)
- **Capability:** Read recent iMessages, draft replies for Josh's review, monitor urgent threads
- **Action needed:** Unpause in inbox-state.json and resume monitoring

## Discord
- **Guild:** Josh's server — ID 1484448262290276464
- **Policy:** requireMention: false (responds without @mention in configured guild)
- **DM policy:** open | **Group policy:** open
- **ABSOLUTE RULE:** DO NOT send emoji reactions on any Discord message, ever. Josh has explicitly required this.

## AlphaClaw Control Panel
- **URL:** https://5.78.142.81.sslip.io
- **Auth:** Token-based (OPENCLAW_GATEWAY_TOKEN)
- **Access:** Requires current browser device pairing (verify after any session gap)
- **Use for:** Model management, Google account connection, OpenClaw version management, restart

## OpenClaw Version
- **Current:** 2026.3.22 (installed 2026-03-24)
- **Stable channel:** 2026.5.12
- **Gap:** 19 releases behind
- **Upgrade prerequisite:** Connect Google account first (to avoid disruption)
```

---

## NEW: Daily Memory Log Template (Start Using Today)

**File to create each session:** `workspace/memory/YYYY-MM-DD.md`
**Effort:** 3 minutes per session end
**Urgency:** Today — not contingent on upgrade or Google account connection

After every session, create or append to today's log:

```markdown
# Session Log — 2026-MM-DD

## Sessions
- [HH:MM – HH:MM PDT]: [1-2 sentence description of what was discussed or done]

## Actions Taken
- [What did Heather actually do? Messages read? Emails drafted but not sent? Searches run?]

## Deferred / Pending
- [What came up that wasn't resolved? What needs follow-up?]

## Preferences Learned
- [Did Josh express any preference, correction, or feedback? Write it down.]
- [Did anything reveal context about Josh's work, schedule, or priorities?]

## Context for Tomorrow
- [What should tomorrow-Heather know immediately? 2–3 bullets max.]
```

---

## Priority 1 (Unchanged, Day 3) — Add No-Emoji Rule to SOUL.md

This is the most overdue action in the backlog. Josh stated it explicitly. It was filed as a 2-minute edit on Day 28 Evening. It remains unapplied on Day 30.

**Add to SOUL.md Boundaries section:**

```markdown
## Josh's Non-Negotiables

- **No emoji reactions. Ever.** Josh explicitly requested this. No 👍, no ❤️, no 🙌 — not on any message, in any channel, under any circumstances. This is an absolute rule, not a guideline.
- **External actions require confirmation.** Before sending any email, editing any calendar event, or modifying any contact: state what you're about to do and wait for Josh to confirm. Never act without approval on anything that leaves the machine.
- **Timezone is America/Los_Angeles (PDT/PST).** All times in Josh's local timezone unless he specifies otherwise.
```

---

## Priority 2 (Unchanged) — Add Daily Rhythm to SOUL.md

See soul-improvements-2026-05-16-evening.md Priority 2 for full paste-ready text. Summary:

- 5–8 AM PDT: Quiet unless genuinely urgent
- 8–10 AM PDT: Proactive window — surface email/calendar briefly
- 10 AM–6 PM PDT: Responsive mode — answer what's asked, don't volunteer noise
- 6–10 PM PDT: Evening summary if something notable happened, otherwise quiet
- 10 PM–5 AM PDT: HEARTBEAT_OK unless truly critical

---

## Priority 3 (Unchanged) — Add Escalation Protocol to SOUL.md

See soul-improvements-2026-05-16-evening.md Priority 3 for full paste-ready text. Summary:

- Before sending any email: draft → post to Discord for approval → wait → send
- Before calendar edit: state the change → wait for confirmation
- Before contact edit: state the edit → wait for confirmation
- When uncertain: one direct question beats one wrong action

---

## Priority 4 (Unchanged) — Bootstrap MEMORY.md

See soul-improvements-2026-05-16-evening.md Priority 5 for full paste-ready MEMORY.md content. After 30 days and zero daily logs, this should be created from available context in existing files (USER.md, IDENTITY.md, onboarding-google.md, fleet research findings). The bootstrapped MEMORY.md from yesterday's soul-improvements file remains accurate and ready to paste.

---

## Tracking — All Prior Recommendations

| Recommendation | First Filed | Status | Days Pending |
|---|---|---|---|
| Add no-emoji rule to SOUL.md | 2026-05-14 evening | **UNIMPLEMENTED** | 3 |
| Add escalation protocol to SOUL.md | 2026-05-13 evening | **UNIMPLEMENTED** | 4 |
| Add timezone awareness to SOUL.md | 2026-05-13 evening | **UNIMPLEMENTED** | 4 |
| Add daily rhythm to SOUL.md | 2026-05-15 evening | **UNIMPLEMENTED** | 2 |
| Create MEMORY.md | 2026-05-12 evening | **UNIMPLEMENTED** | 5 |
| Customize AGENTS.md for Josh | 2026-05-14 evening | **UNIMPLEMENTED** | 3 |
| Populate TOOLS.md with actual tools | 2026-05-12 evening | **UNIMPLEMENTED** | 5 |
| Design and enable heartbeat | 2026-05-15 evening | **UNIMPLEMENTED** | 2 |
| Fix retired fallback model | 2026-05-15 evening | **UNIMPLEMENTED** | 2 |
| Start daily memory logs | **2026-05-17 evening (new)** | **UNIMPLEMENTED** | 0 |
| Populate TOOLS.md (permission preflight) | **2026-05-17 evening (new)** | **UNIMPLEMENTED** | 0 |
| Design HEARTBEAT.md (retry-aware cron ready) | **2026-05-17 evening (new)** | **UNIMPLEMENTED** | 0 |
| Delete BOOTSTRAP.md | **2026-05-17 evening (new)** | **UNIMPLEMENTED** | 0 |

**Total soul improvement recommendations across all scans: 13 prior + 4 new = 17. Applied: 0.**

---

*Generated by AlphaClaw Apex Fleet Research Agent — Evening Scan — 2026-05-17 (Day 30)*
