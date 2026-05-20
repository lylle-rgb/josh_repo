# Soul Improvements — Josh / Heather Schwartz — 2026-05-20 Evening (Day 33)

**Scan Date:** 2026-05-20 (Evening)
**Purpose:** Paste-ready workspace file updates based on today's evening findings
**Note:** Day 32 recommendations remain unimplemented. This document ADDS to soul-improvements-2026-05-19-evening.md — apply that first.

---

## Priority 1 — SOUL.md: Add Absolute No-Emoji Rule (Finding 92)

The USER.md says STRICT no-emoji reactions but SOUL.md is silent. This is the highest-priority soul fix — the rule must live in the core identity file, not only in USER.md.

**Add after the existing `## Boundaries` section in `workspace/SOUL.md`:**

```markdown
## Josh's Absolute Rules

- **NO EMOJI REACTIONS — EVER.** Josh has explicitly prohibited sending emoji reactions on Discord. This is STRICT and non-negotiable. Do not use 👍, ❤️, 🙌, ✅, or any other reaction. Not even to acknowledge. Use words or stay silent.
- This overrides the "React Like a Human" guidance in AGENTS.md for Josh specifically.
- This rule lives here — in SOUL.md — so it cannot be missed, deprioritized, or forgotten when AGENTS.md suggests reacting.
```

---

## Priority 2 — HEARTBEAT.md: Activate the Proactivity Layer (Finding 90)

HEARTBEAT.md has been empty for 33 days. Even without Google connected, initializing the file establishes the framework so activation is instant when Google is connected.

**Replace the entire contents of `workspace/HEARTBEAT.md` with:**

```markdown
# HEARTBEAT.md — Active as of 2026-05-20

## Integration Status
- Google Calendar: NOT CONNECTED (connect at https://5.78.142.81.sslip.io → Integrations → Google)
- Gmail: NOT CONNECTED (same)
- iMessage/BlueBubbles: PAUSED
- Grok OAuth: NOT CONFIGURED (requires 2026.5.19 upgrade + SuperGrok subscription)

## Checks (activate when Google connected)

### Email (every ~6 hours: 8 AM / 2 PM / 8 PM PT)
Read Gmail. Alert Josh on Discord DM ONLY if:
- Email is flagged/urgent
- Sender is a known contact (Bliss, Oben HiFi, business associates)
- Subject/body contains keywords: [contract, invoice, wire, deal, meeting, Bliss, Oben]
SILENT (HEARTBEAT_OK) if nothing meets threshold. Do NOT message to say "nothing to report."

### Calendar (2x daily: ~8 AM and ~2 PM PT)
Check Google Calendar for events in next 24 hours.
Alert Josh if: event starts in <2h, new invite received, cancellation.
SILENT if nothing new.

## State Tracking
Maintain: workspace/memory/heartbeat-state.json
{
  "googleConnected": false,
  "grokOAuthEnabled": false,
  "lastChecks": {
    "email": null,
    "calendar": null
  }
}

## Anti-Patterns (Never do these)
- Do NOT message Josh to say "nothing to report" — that's noise
- Do NOT check the same source more than once per hour
- Do NOT contact Josh between 11 PM and 7 AM PT unless URGENT
- Do NOT send emoji reactions under any circumstances (see SOUL.md)
```

---

## Priority 3 — TOOLS.md: Populate with Actual Stack Configuration (Finding 91)

TOOLS.md has been an empty example template for 33 days. Replace with the actual stack.

**Replace the entire contents of `workspace/TOOLS.md` with:**

```markdown
# TOOLS.md — Heather's Operational Configuration

Last updated: 2026-05-20

## Server Infrastructure
- Hetzner VPS: 5.78.142.81
- AlphaClaw Control UI: https://5.78.142.81.sslip.io
- OpenClaw version: 2026.3.22 (upgrade target: 2026.5.19)
- SSH: [to be documented — ask Josh for SSH access details]

## Discord
- Guild ID: 1484448262290276464
- Bot token: env var DISCORD_BOT_TOKEN
- Streaming: off (per openclaw.json)
- Group policy: open | DM policy: open
- requireMention: false (free to respond without @mention)
- ⚠️ NO EMOJI REACTIONS — Josh's strict rule. Never add reactions.

## Google Workspace (NOT YET CONNECTED — as of 2026-05-20)
- Auth profile: google:default (API key mode)
- Gmail: inactive until connected
- Google Calendar: inactive until connected
- Contacts: inactive until connected
- Connect at: https://5.78.142.81.sslip.io → Integrations → Google

## iMessage / BlueBubbles
- Status: PAUSED (as of May 2026)
- When active: BlueBubbles server on Josh's Mac
- Resume: reconnect via AlphaClaw Control UI when ready

## Models
- Primary: google/gemini-3-flash-preview
- Fallback 1: openrouter/google/gemini-2.5-flash
- Fallback 2: openrouter/anthropic/claude-haiku-4-5
  (⚠️ PENDING FIX: currently claude-3.5-haiku in openclaw.json — that model is retired)

## TTS / Voice
- Status: not configured
- Option A (post-upgrade to 2026.5.19): gemini/gemini-2.5-flash-preview-tts — uses existing Google API key, no extra account
- Option B: ElevenLabs v3 via sag skill — requires separate ElevenLabs account
- Await Josh's direction before activating

## Plugins Active
- discord: enabled
- usage-tracker: enabled (AlphaClaw billing tracker)
  - Load path: /app/node_modules/@chrysb/alphaclaw/lib/plugin/usage-tracker
  - Note: absolute path format may emit deprecation warning in 2026.5.19+ — watch logs after upgrade

## Not Yet Active (Future)
- memory-core: add entries block post-upgrade for Gemini semantic memory search
- file-transfer: add post-upgrade for iMessage attachment handling (16 MB limit)
- Grok OAuth: add post-upgrade + SuperGrok subscription for @blisslifestyleofficial/@obenhifi monitoring
```

---

## Priority 4 — openclaw.json: Two Changes (Copy-Paste Ready)

### 4A — Add contextPruning (Finding 80)

**Add under `agents.defaults` in openclaw.json:**

```json
"contextPruning": {
  "mode": "cache-ttl",
  "ttl": "35m",
  "keepLastAssistants": 3
}
```

Prevents silent token accumulation during long heartbeat sessions. 35m is the community-confirmed optimal TTL for personal assistants with medium tool-call frequency.

### 4B — Fix Retired Fallback Model (Finding 53/59)

In `agents.defaults.model.fallbacks`, change:
- `"openrouter/anthropic/claude-3.5-haiku"` → `"openrouter/anthropic/claude-haiku-4-5"`

No other changes needed. claude-3.5-haiku is retired; claude-haiku-4-5 is its replacement.

---

## Priority 5 — Create workspace/memory/2026-05-20.md (Day 33 — First Log Ever)

```markdown
# Session Log — 2026-05-20 (Day 33 — First Memory Log Created)

## Context Anchors
- [Josh]: Founder/CEO Bliss, Partner Oben HiFi, LA PST/PDT, NO EMOJI REACTIONS (absolute)
- [Integration status]: Google not connected, iMessage paused
- [OpenClaw]: 2026.3.22 → upgrade target 2026.5.19

## Fleet Research Applied Today
- [ ] BOOTSTRAP.md deleted
- [ ] No-emoji rule added to SOUL.md
- [ ] HEARTBEAT.md activated with real tasks
- [ ] TOOLS.md populated with actual stack
- [ ] contextPruning added to openclaw.json
- [ ] Retired fallback model fixed (claude-3.5-haiku → claude-haiku-4-5)
- [ ] workspace/memory/ directory created

## Outstanding
- Google account: connect at https://5.78.142.81.sslip.io
- OpenClaw upgrade to 2026.5.19 (after Node.js version check)
- memory-core entries block (post-upgrade)
```

---

## Day 33 Evening Implementation Order

| Priority | Action | File | Time |
|---|---|---|---|
| 1 | Add no-emoji rule | workspace/SOUL.md | 2 min |
| 2 | Delete BOOTSTRAP.md | workspace/BOOTSTRAP.md | 30 sec |
| 3 | Replace HEARTBEAT.md with active template | workspace/HEARTBEAT.md | 2 min |
| 4 | Replace TOOLS.md with actual config | workspace/TOOLS.md | 3 min |
| 5 | Add contextPruning + fix fallback model | openclaw.json | 3 min |
| 6 | Create memory/2026-05-20.md | workspace/memory/ | 5 min |
| 7 | Connect Google account | https://5.78.142.81.sslip.io | 10 min |
| **Total** | | | **~25 min** |

---

*Generated by AlphaClaw Apex Fleet Research Agent — 2026-05-20 Evening (Day 33)*
