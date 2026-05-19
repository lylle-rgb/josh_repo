# Soul Improvements — Josh / Heather Schwartz — 2026-05-19 Evening (Day 32)

**Scan Date:** 2026-05-19 (Evening)
**Purpose:** Paste-ready workspace file updates incorporating today's platform research
**Note:** All Day 31 recommendations remain unimplemented. This document ADDS to soul-improvements-2026-05-18-evening.md — do not discard the previous file.

---

## New Today: Active Memory Configuration (Post-Upgrade to 2026.5.18)

Add to `openclaw.json` after upgrading to 2026.5.18. This enforces MEMORY.md security at the platform level rather than relying on agent behavior:

```json
"memory": {
  "active": {
    "enabled": true,
    "allowedChatIds": ["<JOSH_DM_CHANNEL_ID>"],
    "deniedChatIds": []
  }
}
```

**To find Josh's DM channel ID:** In the AlphaClaw Control UI → Sessions → look for the DM session with Josh → copy the channel ID shown in the session detail.

This replaces the behavioral instruction in AGENTS.md that says "ONLY load in main session." Once this is configured, the platform enforces the restriction — Heather cannot accidentally expose MEMORY.md in group chats even if confused about context.

**After adding this config, update AGENTS.md:**

Find this block:
```
### 🧠 MEMORY.md - Your Long-Term Memory

- **ONLY load in main session** (direct chats with your human)
- **DO NOT load in shared contexts** (Discord, group chats, sessions with other people)
- This is for **security** — contains personal context that shouldn't leak to strangers
```

Replace with:
```
### 🧠 MEMORY.md - Your Long-Term Memory

- Load is **enforced by Active Memory allowedChatIds** — platform restricts recall to Josh's DM channel only
- In main session with Josh: memory context is available automatically
- In group chats or Discord servers: memory context is blocked at the harness level (not just behavioral)
- This is an architectural guarantee, not just a rule to remember
```

---

## Updated HEARTBEAT.md (Post-Upgrade to 2026.5.18 — Cron --wait Pattern)

The cron `--wait` flag is now stable. Update the HEARTBEAT.md design from Day 31 to use conditional-notification architecture:

Replace the `## Rotation Schedule` section with:

```markdown
## Rotation Schedule

Rotate through these checks 2–3x per day. Each check uses the `--wait` pattern:
- Run check → evaluate result
- **If actionable:** post 1-sentence summary to Josh's Discord DM
- **If not actionable:** HEARTBEAT_OK (silent — do NOT send "nothing to report")

### Email Check (every ~6 hours when Google connected)

Check Gmail for unread messages in the last 6 hours.
Surface only: urgent/flagged, from known contacts, or keywords: [Bliss, Oben, contract, wire, meeting, invoice].
Silent if nothing meets the threshold.

### Calendar Check (2x daily: ~8 AM and ~2 PM PT)

Check Google Calendar for events in the next 24 hours.
Notify for events starting in <2 hours, new invites, or cancellations.
Silent if nothing new.

### Social / Brand Check (1x daily — when Grok OAuth configured)

Grok scan for X mentions of @blisslifestyleofficial and @obenhifi.
Surface engagement requiring response or unusual mention volume.
Now in stable (2026.5.18) — activate when Josh has SuperGrok subscription.

## State Tracking

`memory/heartbeat-state.json`:
```json
{
  "lastChecks": {
    "email": null,
    "calendar": null,
    "social": null
  },
  "googleConnected": false,
  "grokOAuthEnabled": false,
  "activationDate": null,
  "cronWaitFlagAvailable": true
}
```
```

---

## Structured Daily Log Format (Optimized for Temporal Memory Retrieval)

Based on Mem0 research: logs structured with explicit timestamps and entity relationships are 29.6 points more retrievable on temporal queries. Use this format for `workspace/memory/YYYY-MM-DD.md`:

```markdown
# Session Log — YYYY-MM-DD

## Context Anchors [entity: fact]
- [Josh]: Founder/CEO Bliss, Partner Oben HiFi, LA PST/PDT, NO EMOJI REACTIONS (absolute)
- [Integration status]: Google not connected (as of this date), iMessage paused
- [OpenClaw]: 2026.3.22 (upgrade target: 2026.5.18)

## What Happened Today [timestamp: event]
- [HH:MM PT]: (event)
- [HH:MM PT]: (event)

## Josh's Preferences Observed Today
- (any preference demonstrated, correction given, or explicit instruction)

## Decisions Made
- (any decision, with rationale and who decided)

## Outstanding (Josh asked for / expects)
- (anything Josh is waiting for from Heather)

## Lessons
- (anything to do differently next time)
```

**Why this format matters:** The `[entity: fact]` and `[timestamp: event]` structure makes context maximally retrievable when Heather searches memory across days. "What did Josh say about Bliss on May 15?" becomes answerable.

---

## SOUL.md Addition: Business Context Block

Add to `workspace/SOUL.md` under the **Vibe** section:

```markdown
## Josh's World

When helping Josh with anything public-facing:
- **Bliss** is a luxury lifestyle brand — everything should feel premium, aspirational, polished
- **Oben HiFi** is a high-end audio brand — technical credibility matters here; don't dumb things down
- Josh is a Founder/CEO — his time is expensive; front-load conclusions, bury details
- Business communication (emails, contracts, partnerships) warrants careful review before sending
- Nothing is sent until Josh explicitly approves external-facing content

## Technical Stack Context

- Primary channel: Discord (Guild 1484448262290276464)
- Planned: Gmail, Calendar, Contacts (when Google connected)
- Future: X/Twitter monitoring via Grok OAuth (now stable in 2026.5.18)
- Voice: ElevenLabs v3 available (not yet enabled — ask Josh before activating)
```

---

## TOOLS.md Addition: TTS and Grok Updates

Add to the TOOLS.md template from Day 31:

```markdown
## TTS / Voice (Updated 2026-05-19)

- ElevenLabs v3 now in stable (2026.5.18+) — highest quality option available
- Azure Speech also available in stable
- Not configured — Josh has not requested voice activation
- When enabling: use `sag` skill + ElevenLabs v3 provider
- Recommended persona voice: select one matching Heather's "Sharp, Helpful, Resourceful" vibe

## Social Monitoring (Future)

- Grok OAuth (SuperGrok) now stable in 2026.5.18+
- Monitors X/Twitter in real-time
- Activate when: (1) Josh upgrades to 2026.5.18+, (2) Noah has SuperGrok subscription
- Target brands: @blisslifestyleofficial, @obenhifi
- Add to HEARTBEAT.md once configured

## Active Memory Filters (Post-Upgrade)

- allowedChatIds restricts MEMORY.md recall to Josh's DM channel only
- Configure in openclaw.json after 2026.5.18 upgrade
- Replaces behavioral AGENTS.md instruction with architectural guarantee
```

---

## openclaw.json Upgrade Target (Replace Prior Recommendation)

**Prior scans recommended upgrading to 2026.5.12. Revise: go directly to 2026.5.18.**

All intermediate features (defineToolPlugin, Grok OAuth, cron --wait, Active Memory filters, ElevenLabs v3) are now in 2026.5.18 stable. There is no value in stopping at 2026.5.12.

Pre-upgrade checklist:
```
[ ] Verify node --version ≥ 22.19 on Hetzner VPS (5.78.142.81)
[ ] cp openclaw.json openclaw.json.bak-pre-5.18
[ ] Fix retired fallback model (claude-3.5-haiku → claude-haiku-4-5) before upgrade
[ ] Fix inbox-state.json (duplicate key)
[ ] AlphaClaw updated to 0.9.16 (verify first)
[ ] Upgrade OpenClaw via AlphaClaw Control UI
[ ] Verify Heather comes online post-upgrade
[ ] Confirm Discord channel still responsive
```

---

*Generated by AlphaClaw Apex Fleet Research Agent — 2026-05-19 Evening (Day 32)*
