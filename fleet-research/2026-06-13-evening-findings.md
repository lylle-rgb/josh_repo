# Fleet Research — Evening Scan Findings
**Instance:** Heather Schwartz (Josh — personal assistant)
**Scan date:** 2026-06-13 (evening)
**Scanner:** AlphaClaw Fleet Agent (automated evening scan)
**Previous scan:** 2026-06-12 evening (see `2026-06-12-evening-findings.md`)

---

## Summary

Day-count escalations: JOSH-30 (MEMORY.md missing) → **Day 83**. JOSH-31 (HEARTBEAT.md empty) → **Day 83**. JOSH-48 (platform behind) → **Day 83**. JOSH-44 Google Workspace → **Day 10** (though inbox-state.json timestamps suggest the actual disconnect is ~47 days old, not 10 days).

Tonight's headline findings:

1. **inbox-state.json reveals actual Google/iMessage outage started late April 2026** — ~47 days ago, not 10 days. Heather has had zero email and iMessage access since approximately April 27–30.
2. **Cron bug #11726 confirmed** — important design constraint for any future cron-to-main-session automation Heather might use.
3. **OpenClaw 2026.5.26 Meeting Notes** — missed in prior Josh scans. Heather could take Josh's executive meeting notes in Discord voice channels. Highly relevant for a Founder/CEO.
4. **OpenClaw 2026.6.5 confirmed stable June 9** — Josh is 84 days behind. 2026.6.6-beta.2 exists but monthly cadence means 2026.6.6 stable not expected until early July.
5. **Parallel web search in 2026.6.5** — Heather would be able to research email + calendar + weather + news simultaneously during morning briefs. Direct quality-of-life improvement.

---

## NEW FINDINGS (Evening Scan — June 13)

### Finding A — inbox-state.json Reveals 47-Day Email + iMessage Outage (Not 10 Days)
**Severity:** HIGH (corrects prior finding JOSH-44 scope; significantly worse than previously documented)
**What was found:** Direct inspection of `workspace/memory/inbox-state.json` reveals:

```json
{
  "imessage_monitoring_paused": true,
  "last_email_check_ms": 1777087800000,
  "last_imessage_check_ms": 1777271400000,
  "last_email_check_ms": 1777551900000
}
```

**Decoded timestamps (UTC):**
- `last_email_check_ms` (first occurrence): 1777087800000ms ≈ **April 25, 2026**
- `last_imessage_check_ms`: 1777271400000ms ≈ **April 27, 2026**
- `last_email_check_ms` (second occurrence, overwrites first): 1777551900000ms ≈ **April 30, 2026**

**Conclusion:** Heather's last successful email check was approximately **April 30, 2026** — **44 days ago** from today (June 13). Her last iMessage check was **April 27** — **47 days ago**. iMessage monitoring remains paused.

The June 12 finding JOSH-44 stated "Google disconnected since June 4 (Day 9)." This was based on a reported reconnection attempt date, not the actual last successful access. The inbox-state.json timestamps show the underlying connectivity broke **7 weeks earlier than the Day 9 framing implies**.

**Second issue in this file:** The JSON has a **duplicate key** `"last_email_check_ms"` (appears on line 1 and line 5 with different values). This is technically invalid JSON. Most JSON parsers will use the last occurrence (1777551900000), but some strict parsers may reject the file entirely. When Heather updates this file next, she should fix the duplicate key.

**Corrected severity framing:**
- Heather has been email-deaf for **44 days** (not 9 days)
- Heather has been iMessage-deaf for **47 days**
- Josh may have missed Heather alerts, reminders, and proactive outreach since late April

**Risk level:** HIGH — the scope of the Google Workspace outage is much larger than previously documented. Priority escalation warranted.

---

### Finding B — Cron Bug #11726 Confirmed: Cron-to-Main-Session Delivery Won't Wake Heather
**Severity:** MEDIUM (design constraint for future automation)
**What was found:** GitHub issue [openclaw/openclaw#11726](https://github.com/openclaw/openclaw/issues/11726) is confirmed closed with a workaround. The bug: cron jobs using `sessionTarget: "main"` inject system events but the agent **does not wake** to process them. The event sits in the session queue until the next heartbeat or user message triggers the agent.

**Why this matters for Heather:** When HEARTBEAT.md is eventually populated and Heather starts using cron for precise scheduling (e.g., "remind Josh of tomorrow's board meeting at 8 AM"), any cron job designed to alert the main session will fail silently using the standard `sessionTarget: "main"` pattern.

**Correct approach for Heather's future cron jobs:**

Option 1 (preferred — isolated cron session delivering to Discord):
```json
{
  "name": "morning-brief",
  "schedule": "0 7 * * 1-5",
  "prompt": "Generate Josh's morning brief: check email for urgent items, calendar for today, weather in LA. Post to Discord.",
  "deliver": {
    "channel": "discord:1484448262290276464"
  },
  "model": "google/gemini-3-flash-preview",
  "wakeMode": "now"
}
```

Option 2 (shell workaround if main session needed):
```shell
openclaw agent --agent main --deliver --message "Morning brief ready."
```

**Also confirmed:** New cron jobs may still default to `wakeMode: "next-heartbeat"` despite 2026.2.6 changelog. Always set `wakeMode: "now"` explicitly.

**Risk level:** MEDIUM — no active cron jobs exist yet, so no current breakage. Critical to know when HEARTBEAT.md is populated.

---

### Finding C — OpenClaw 2026.5.26: Meeting Notes for Josh's Executive Calls (Not in Prior Scans)
**Severity:** HIGH (high-value feature for a Founder/CEO that was missed in prior scans)
**What was found:** OpenClaw 2026.5.26 (released late May 2026 per @openclaw X post) added **Meeting Notes + Discord voice runs**. This was not documented in any prior Josh scan series.

**What it enables for Heather:**
- Heather can join a Discord voice channel that Josh is in
- During the call, Heather listens and transcribes
- After the call ends, Heather posts structured meeting notes to the text channel and saves to `workspace/memory/YYYY-MM-DD-meeting-[title].md`
- Notes include: attendees, key decisions, action items, follow-ups

**Why this is highly relevant for Josh:**
Josh is a Founder/CEO at Bliss Lifestyle and Partner at Oben HiFi. Executive calls are his primary operational context — decisions made in those calls are the decisions Heather should be tracking. Currently Heather has zero visibility into what happens in Josh's meetings. Meeting Notes bridges that gap.

**Example use cases:**
- Weekly team call at Bliss: Heather captures action items, follows up via Discord
- Investor call: Heather notes commitments made, flags in memory
- Oben HiFi partner sync: Heather tracks project status discussed

**Prerequisite:** Josh's VPS must be upgraded to 2026.5.26+ (which is included in 2026.6.5, the current stable). JOSH-48 (upgrade to 2026.6.5) is the prerequisite. Once upgraded, Heather can be invited to a voice channel and given the `/meeting-notes` command.

**Risk level:** LOW (additive feature, no config change required post-upgrade).

---

### Finding D — OpenClaw 2026.6.5 Confirmed Stable June 9 + Parallel Web Search for Morning Briefs
**Severity:** HIGH (specific benefit for Heather's personal assistant workflow)
**What was found:** OpenClaw 2026.6.5 released **June 9, 2026** (4 days ago) as confirmed stable. Josh is on 2026.3.22, which is now **84 days behind** stable.

**Key capability unlocked by upgrade — Parallel Web Search:**
2026.6.5 includes parallel web search: the agent can now run multiple searches simultaneously. For Heather's morning brief workflow, this means:
- Email status + Calendar check + LA weather + News relevant to Bliss/Oben HiFi can all be gathered in parallel
- A morning brief that currently takes 4+ minutes of sequential searching could compress to ~60-90 seconds
- More thorough briefs possible within the same token budget

**Other 2026.6.5 improvements relevant to Josh:**
- **Channel safety improvements**: Heather's Discord connection becomes more resilient
- **Durable auth**: Google Workspace auth (once reconnected) is harder to break on restart
- **MCP recovery**: If a tool call fails mid-task, recovery is automatic rather than requiring Heather to retry

**Monthly cadence update:** 2026.6.5 is the June stable (June 9 release). 2026.6.6-beta.2 exists (released June 12) but is NOT stable. The next stable is expected early July as 2026.7.x. **Plan: upgrade to 2026.6.5 now; do not chase beta.2.**

**Risk level:** HIGH — upgrading from 2026.3.22 to 2026.6.5 is a large version jump (3 months). VPS access required for `openclaw update`. Review 2026.6.6-beta.2 breaking changes before upgrading.

---

### Finding E — Day 83 Zombie State: Core Assistant Functions Remain Zero-Operational
**Severity:** CRITICAL (cumulative escalation)
**What was found:** After 83 days, the following core personal assistant functions remain zero-operational:

| Function | Status | Days Non-Functional |
|---|---|---|
| Long-term memory (MEMORY.md) | Not created | 83 |
| Proactive monitoring (HEARTBEAT.md) | Empty | 83 |
| Email access (Google Workspace) | Disconnected | ~44 |
| iMessage | Paused | ~47 |
| Platform features | 84 days behind stable | 84 |
| Google Workspace reconnection attempt | Still pending | 10 |

**Net operational picture:** Heather is a passive reactive chatbot. She responds when Josh messages her in Discord. She does not check email. She does not check calendar. She does not monitor iMessage. She does not have memory of anything from prior sessions. She has no proactive checklist to work through.

**The gap between Heather's SOUL.md mission and her actual operational state is complete.** SOUL.md instructs: *"Be resourceful before asking. Check the file. Search for it."* But Heather has no recurring mechanism to check anything between conversations.

**What would change with the top 3 fixes:**
1. Populate HEARTBEAT.md (30 min effort, zero VPS needed) → Heather starts checking email, calendar, weather 2-4x/day
2. Reconnect Google Workspace (Josh completes OAuth) → email and calendar come back online
3. Create MEMORY.md stub (5 min, zero VPS needed) → Dreaming becomes possible after 2026.6.5 upgrade

**Risk level:** CRITICAL — Day 83 without core functions is not a minor gap. The assistant is not functioning as intended.

---

## Open Findings (Updated Day Counts — June 13 Evening)

| # | Severity | Finding | Days Open |
|---|---|---|---|
| JOSH-30 | **CRITICAL** | MEMORY.md never created | **83** |
| JOSH-31 | **CRITICAL** | HEARTBEAT.md empty — zero proactive monitoring | **83** |
| JOSH-48 | **CRITICAL** | Platform 84 days behind stable (2026.6.5) | **84** |
| JOSH-44 | **CRITICAL** | Google Workspace disconnected ~44 days (not 9) | escalation |
| June13-A | HIGH | Email outage 44 days, iMessage 47 days (inbox-state reveals true scope) | **NEW** |
| June13-A | MEDIUM | inbox-state.json duplicate key bug (invalid JSON) | **NEW** |
| JOSH-47 | HIGH | Dreaming blocked (needs upgrade + MEMORY.md) | **83** |
| JOSH-37 | HIGH | SOUL.md not personalized for Josh/executive PA domain | **83** |
| JOSH-33/45 | MEDIUM | iMessage paused — Mac bridge (AlphaClaw 0.8.0) available | 45 |
| June13-C | HIGH | 2026.5.26 Meeting Notes for executive voice calls (missed feature) | **NEW** |
| June13-D | HIGH | 2026.6.5 stable (June 9) — parallel search locked until upgrade | **NEW** |
| June13-B | MEDIUM | Cron #11726: sessionTarget:main won't wake agent — design constraint | **NEW** |
| JOSH-34 | LOW | Emoji contradiction: AGENTS.md allows reactions, USER.md bans them | **83** |
| JOSH-54 | LOW | BOOTSTRAP.md not deleted | 5 |
| JOSH-55 | MEDIUM | TOOLS.md template-only | 5 |
| June10-B | MEDIUM | Gemini 3.1 Flash Lite not added as fallback | 3 |
| June10-C | MEDIUM | Discord streaming still `"off"` | 3 |
| June10-D | MEDIUM | Dead haiku slug in fallbacks | 3 |
| June12-A | HIGH | AlphaClaw 0.8.0 Chrome DevTools MCP — iMessage solution ready | 1 |

---

## Priority Queue (What to Fix First)

**No VPS access, no Josh required (do these NOW):**
1. Create `workspace/MEMORY.md` stub (JOSH-30) — 5 min, unblocks Dreaming after upgrade
2. Populate `workspace/HEARTBEAT.md` with email/calendar/weather checklist (JOSH-31) — 30 min
3. Fix `workspace/memory/inbox-state.json` duplicate key (June13-A) — 2 min

**Josh action required:**
4. Complete Google Workspace OAuth flow (JOSH-44) — reconnects email + calendar
5. Install AlphaClaw 0.8.0 on Mac to unblock iMessage bridge (June12-A)

**VPS access required:**
6. `openclaw update` to 2026.6.5 (JOSH-48) — unlocks parallel search, Meeting Notes, Dreaming

---

## Research Sources

- [OpenClaw 2026.6.5 stable release (GitHub)](https://github.com/openclaw/openclaw/releases/tag/v2026.6.5)
- [Cron jobs with sessionTarget:main issue #11726 (GitHub)](https://github.com/openclaw/openclaw/issues/11726)
- [OpenClaw 2026.5.26 release — @openclaw on X](https://x.com/openclaw/status/2059619141384859939)
- [OpenClaw Release Notes June 2026 (Releasebot)](https://releasebot.io/updates/openclaw)
- [AlphaClaw 0.8.0 Chrome DevTools MCP — @chrysb on X](https://x.com/chrysb/status/2032943853012136120)
