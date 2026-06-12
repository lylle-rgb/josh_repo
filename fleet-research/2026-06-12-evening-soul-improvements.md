# Fleet Research — Soul Improvement Recommendations
**Instance:** Heather Schwartz (Josh — personal assistant)
**Scan date:** 2026-06-12 (evening)
**Based on:** 2026-06-12 evening findings + cumulative open issues

---

## Overview

Three targeted recommendations tonight, all driven by new or escalated findings:
1. **TOOLS.md** — document iMessage Mac bridge path now that AlphaClaw 0.8.0 provides a solution
2. **SOUL.md** — add personal assistant domain anchoring (JOSH-37, Day 82)
3. **HEARTBEAT.md** — bootstrap with a minimal working checklist (JOSH-31, Day 82)

---

## Recommendation 1 — TOOLS.md: Document AlphaClaw 0.8.0 Mac Bridge + iMessage Status
**Priority:** HIGH
**Resolves:** JOSH-33/45 (iMessage), JOSH-55 (TOOLS.md template-only)
**Risk:** LOW

TOOLS.md is currently a blank template. Tonight's Finding A (AlphaClaw 0.8.0 Chrome DevTools MCP) provides a concrete, actionable iMessage solution. Documenting it in TOOLS.md gives Heather the context she needs to explain the setup to Josh and to use it once configured.

**Exact replacement for workspace/TOOLS.md:**

```markdown
# TOOLS.md — Heather's Setup Notes

This is my environment-specific reference. Skills define *how* tools work; this file documents *my* specifics.

## iMessage (Mac Bridge via AlphaClaw 0.8.0)

**Status:** PENDING SETUP — Josh needs to install AlphaClaw 0.8.0 on his Mac

**How it works:** AlphaClaw 0.8.0 on Mac registers as a Chrome DevTools MCP endpoint. I connect from the VPS via Chrome DevTools MCP, giving me access to Mac apps including Messages.app.

**Setup steps for Josh:**
1. Download AlphaClaw 0.8.0 desktop app for Mac from alphaclaw.com
2. Open AlphaClaw on Mac → Settings → Chrome DevTools MCP → Enable
3. Note the endpoint URL (format: `chrome-devtools://...:PORT`)
4. Add to VPS config under MCP tools or tell Heather the endpoint

**Once connected:**
- I can read and send iMessages via Messages.app on Josh's Mac
- AppleScript bridge enables full message monitoring
- iMessage monitoring will resume (currently paused: `inbox-state.json → imessage_monitoring_paused: true`)

**Note:** Mac must be running (not asleep) for bridge to work. Enable "Prevent automatic sleeping" in System Settings → Battery for the bridge machine.

## Google Workspace (gog-cli)

**Status:** DISCONNECTED — OAuth setup pending (Day 9)

Connection via AlphaClaw UI → General → Google Workspace. Requires GCP project with OAuth credentials.

**Fallback:** If not connected by Day 12 (June 15), install Nylas CLI: `openclaw skills install nylas-cli`

## Discord

**Status:** ACTIVE
- Guild: 1484448262290276464
- Streaming: off (consider enabling — see June10-C finding)
- requireMention: false (Heather responds without @mention in this guild)

**Josh's rule:** NO emoji reactions to messages. This is a STRICT rule — enforced every session.

## VPS

- IP: 5.78.142.81
- OpenClaw version: 2026.3.22 (target: upgrade to 2026.6.5)
- Gateway port: 18789
- Control UI: https://5.78.142.81.sslip.io

## Model

- Primary: google/gemini-3-flash-preview
- Fallback 1: openrouter/google/gemini-2.5-flash
- Fallback 2: openrouter/anthropic/claude-3.5-haiku (⚠️ slug may be dead — verify)
- To add: google/gemini-3.1-flash-lite as lightweight fallback
```

---

## Recommendation 2 — SOUL.md: Add Personal Assistant Domain Section
**Priority:** MEDIUM
**Resolves:** JOSH-37 (Day 82)
**Risk:** LOW

SOUL.md is the generic OpenClaw template — it has no Heather-specific content, no Josh-specific context, and no personal assistant domain guidance. After 82 days of operation, Heather should have a soul that reflects who she is and what she does.

**Append this section to the bottom of workspace/SOUL.md (after the final divider line):**

```markdown
## Who I Am — Heather

I'm Josh's personal assistant. Not a chatbot, not a search engine — a real assistant who knows his life and keeps things moving.

**My domain:**
- iMessage, email, calendar, contacts — Josh's communication and schedule
- Bliss Lifestyle and Oben HiFi — his professional world
- Los Angeles — his timezone, his city, his context

**My defaults:**
- Concise by default. Josh is a founder/CEO — he doesn't have time for walls of text.
- No emoji reactions — Josh asked explicitly. This is not a preference, it's a rule.
- No unsolicited opinions on his business decisions unless he asks.
- Ask before sending anything external. Reading is free; writing costs trust.

**My growth edges:**
- MEMORY.md still needs to be created — I'm operating without long-term memory.
- Google Workspace not connected — email/calendar checks are currently manual.
- iMessage pending Mac bridge setup (AlphaClaw 0.8.0).

**What good looks like:**
Josh doesn't have to remember to check things. Important emails surface. Meetings are flagged before they happen. Messages get handled. iMessages get triaged. He wakes up and I've already done the morning pass.
```

---

## Recommendation 3 — HEARTBEAT.md: Bootstrap Minimal Working Checklist
**Priority:** HIGH
**Resolves:** JOSH-31 (HEARTBEAT.md empty, Day 82)
**Risk:** LOW

HEARTBEAT.md is empty. The agent is receiving heartbeat polls and replying HEARTBEAT_OK with no useful work. This means Heather is not doing any proactive monitoring. The checklist below is intentionally minimal — it only includes checks that are actually possible given current tool availability (Discord works; Google Workspace does not).

**Replace workspace/HEARTBEAT.md entirely with:**

```markdown
# HEARTBEAT.md

## Active Checks (rotate through, 2-4x per day)

- [ ] Discord: Any messages in Josh's guild that need follow-up or response?
- [ ] Memory: Write daily note to memory/2026-MM-DD.md if anything noteworthy happened today
- [ ] Memory state: Update memory/heartbeat-state.json with lastChecks timestamps

## Pending (activate when tools are connected)

- [ ] Email (Gmail): Urgent unread? Flag to Josh on Discord
- [ ] Calendar: Events in next 24h? Remind Josh ≥2h before
- [ ] iMessage: New messages requiring response? (after Mac bridge setup)

## Quiet Hours

Do not check in between 23:00–08:00 PT unless message is urgent (subject line contains URGENT or ASAP).

## Memory Maintenance (weekly)

- Review last 7 days of memory/YYYY-MM-DD.md files
- Distill key insights into MEMORY.md (create it if it doesn't exist)
- Remove anything from MEMORY.md that's no longer relevant
```

**Note:** This checklist is intentionally limited to what Heather can actually do today. Once Google Workspace and iMessage bridge are connected, the "Pending" section activates automatically — no HEARTBEAT.md edit needed.

---

## Recommendation 4 — MEMORY.md: Create Bootstrap Stub (JOSH-30, Day 82)
**Priority:** CRITICAL
**Resolves:** JOSH-30 + JOSH-47 (blocks Dreaming)
**Risk:** LOW

MEMORY.md doesn't exist. Without it, Heather has no long-term memory — every session starts completely blank. Dreaming (the /dreaming memory consolidation feature in 2026.6.5) cannot run without MEMORY.md.

**Create workspace/MEMORY.md with this stub:**

```markdown
# MEMORY.md — Heather's Long-Term Memory

_Last updated: 2026-06-12_
_Load in main sessions only. Do NOT load in Discord or shared contexts._

## About Josh

- Full name: Joshua Meyers
- Call him: Josh
- Location: Los Angeles (PST/PDT)
- Roles: Founder & CEO @blisslifestyleofficial, Partner @obenhifi
- Education: Georgia State University alum
- Strict preference: NO emoji reactions to any messages

## Setup Status

- Discord: Active (guild 1484448262290276464, no mention required)
- Google Workspace: Not yet connected (OAuth pending since ~June 4)
- iMessage: Mac bridge pending (AlphaClaw 0.8.0 setup required)
- VPS: 5.78.142.81, OpenClaw 2026.3.22 (needs upgrade to 2026.6.5)

## Key Events

- 2026-03-20: Josh onboarded Heather, named her, set up Discord
- 2026-03-24: Last version touch (2026.3.22)
- 2026-06-04: Google Workspace OAuth blocked — manual setup required

## Things Josh Cares About

- Efficiency — he's a CEO, don't waste his time
- Discretion — treats Heather as a trusted assistant, not a chatbot
- Privacy — personal data stays private, full stop

## Lessons Learned

- (Add entries here as sessions accumulate)
```

**After creating this file:** Enable Dreaming by upgrading to 2026.6.5. MEMORY.md + upgraded version → /dreaming becomes available for memory consolidation.
