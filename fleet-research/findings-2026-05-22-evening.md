# Fleet Research — Josh (Heather Schwartz) | 2026-05-22 Evening Scan

**Scan type:** Full (web research + codebase analysis + platform news)
**Date:** 2026-05-22
**Instance:** Josh Meyers — Heather Schwartz (personal assistant)
**Repo:** lylle-rgb/josh_repo
**Previous scan:** 2026-05-21 evening

---

## Platform Status

| Item | Current | Latest | Gap |
|------|---------|--------|-----|
| OpenClaw | 2026.3.22 | **2026.5.20** | **~2 months, now 2 releases behind latest** |
| AlphaClaw | Unknown | 0.9.16 | Check deployment |
| Primary model | google/gemini-3-flash-preview | — | Active |

---

## New Since Yesterday

### FINDING-JOSH-39 | OpenClaw 2026.5.20 Released (Post-Scan)
**Severity:** HIGH
**Status:** NEW (released 2026-05-21 at 20:44 UTC — after yesterday's evening scan)

OpenClaw 2026.5.20 stable dropped last night. Josh was already 2 months behind at 2026.3.22; this widens the gap to 2 months + the most recent stable build.

**What's in 2026.5.20:**
- Discord voice session improvements — multi-turn context maintained across voice messages
- Policy plugin integration — admin-level permission scoping for tool use
- xAI device-code OAuth support (not relevant to Josh but included in the platform)
- Underlying stability fixes inherited from 2026.5.19 and 2026.5.20-beta train

**What's in 2026.5.19 (May 20, also new since last scan):**
- **Meme-maker skill** — Heather can now generate memes on request (relevant for Bliss social content)
- **Python debugging skill** — extended code execution capabilities
- Browser modal dialog support — web automation workflows more reliable
- QA-Lab runtime parity for first-hour scenarios

**Why it matters:**
- Discord voice improvements directly benefit Heather's voice features
- Meme-maker could be useful for Josh's luxury lifestyle brand social media work
- Policy plugin enables granular tool scoping — relevant when configuring iMessage vs public Discord access
- AlphaClaw 0.9.15+ includes config restoration on fresh boot — important for crash recovery

**Exact changes to apply:**
```
# On the VPS via AlphaClaw terminal or SSH:
openclaw upgrade
openclaw --version  # verify 2026.5.20
```
Also upgrade AlphaClaw to 0.9.16 if not already done.

**Risk level:** MEDIUM (test Discord connectivity after upgrade; verify voice features if in use)

---

## Persistent Findings (Unresolved from Previous Scans)

### FINDING-JOSH-30 | MEMORY.md Never Created
**Severity:** CRITICAL
**Status:** PERSISTENT (Day 33+)

Confirmed: `workspace/MEMORY.md` does not exist. `workspace/memory/` contains only `inbox-state.json` and `onboarding-google.md` — no daily session notes, no long-term memory file.

The agent is instructed every session to read MEMORY.md (AGENTS.md: "If in MAIN SESSION, also read MEMORY.md") — this instruction is unanswerable every single time.

**Risk level:** LOW to create | HIGH cost of continued absence

---

### FINDING-JOSH-31 | HEARTBEAT.md Empty — No Proactive Monitoring
**Severity:** HIGH
**Status:** PERSISTENT (Day 33+)

Confirmed: `workspace/HEARTBEAT.md` is template-only. Every heartbeat fires and returns `HEARTBEAT_OK`. Heather is not checking Gmail, Google Calendar, or anything proactively.

**Risk level:** LOW to fix

---

### FINDING-JOSH-32 | Bootstrap TOOLS.md — False Google Auth State
**Severity:** MEDIUM
**Status:** PERSISTENT

Confirmed: `workspace/hooks/bootstrap/TOOLS.md` ends with:
> "No Google accounts are currently configured."

This is **false**. Josh has `google:default` configured in api_key mode (seen in `openclaw.json`). This file is injected at every session start — Heather has been starting each session with incorrect context about her own capabilities for 60+ days.

**Exact fix:** Update the last section of `workspace/hooks/bootstrap/TOOLS.md` to reflect the actual Google configuration. Replace the "No Google accounts" line with:
```markdown
## Available Google Accounts

Josh's Google Workspace is connected via api_key mode (google:default).
Available services: Gmail, Calendar, Drive, Contacts, Tasks.
Use `--client google --account default` for gog commands.
```

**Risk level:** LOW

---

### FINDING-JOSH-33 | iMessage Monitoring Paused — Malformed JSON
**Severity:** MEDIUM
**Status:** PERSISTENT

Confirmed from `workspace/memory/inbox-state.json`:
```json
{"imessage_monitoring_paused": true, "last_email_check_ms": 1777087800000, "last_imessage_check_ms": 1777271400000, "last_email_check_ms": 1777551900000}
```

Two issues:
1. `imessage_monitoring_paused: true` — iMessage has been paused for 25+ days
2. **Duplicate key** `last_email_check_ms` — invalid JSON; the second value (1777551900000 = ~April 28) silently overwrites the first. JSON parsers that error on duplicate keys will fail to parse this file entirely.

**Exact fix:**
```json
{
  "already_drafted_imessage_guids": [],
  "already_drafted_thread_ids": ["19db60d96d2118c8"],
  "imessage_monitoring_paused": false,
  "last_email_check_ms": 1777551900000,
  "last_imessage_check_ms": 1777271400000
}
```
Note: Only re-enable iMessage after confirming the bridge is operational.

**Risk level:** LOW to fix JSON; MEDIUM to re-enable iMessage (requires bridge verification)

---

### FINDING-JOSH-34 | Emoji Contradiction — USER.md vs AGENTS.md
**Severity:** LOW
**Status:** PERSISTENT (identified yesterday)

Confirmed: USER.md says "STRICT: DO NOT SEND EMOJI REACTIONS" but AGENTS.md has a full "React Like a Human!" section promoting emoji reactions. Josh's explicit preference must win.

**Exact fix:** Add to SOUL.md under a "User Preference Overrides" section:
```markdown
## User Preference Overrides

**NO emoji reactions.** Josh has explicitly asked: do not use emoji reactions on his Discord messages. This overrides the general group chat guidance in AGENTS.md. No exceptions.
```

**Risk level:** LOW

---

### FINDING-JOSH-35 | streaming.mode: "progress" Not Enabled
**Severity:** INFO
**Status:** OPPORTUNITY (requires upgrade to 2026.5.x first)

Currently `openclaw.json` has `"streaming": "off"` for Discord. After upgrading to 2026.5.20, enable progress drafts so Heather appears responsive during long tasks.

**Exact change:**
```json
// In openclaw.json, channels.discord:
"streaming": "progress"
```

**Risk level:** LOW

---

### FINDING-JOSH-36 | Mem0 Persistent Memory Plugin
**Severity:** INFO
**Status:** OPPORTUNITY (available now)

Mem0 provides external, compaction-proof memory for OpenClaw. Directly addresses the MEMORY.md gap — even if MEMORY.md is created natively, Mem0 gives a secondary layer that survives context pruning and container restarts.

**Risk level:** LOW

---

### FINDING-JOSH-37 | SOUL.md Never Personalized for Heather/Josh
**Severity:** MEDIUM
**Status:** PERSISTENT (Day 33+)

Confirmed: `workspace/SOUL.md` SHA matches the upstream OpenClaw generic template. 60+ days of personalization signals (luxury brand context, Bliss/Oben HiFi, LA timezone, Josh's strict emoji rule, Heather's name) have not been written back into SOUL.md.

**Risk level:** LOW to fix, HIGH ongoing cost

---

### FINDING-JOSH-38 | AlphaClaw Crash Notifications Not Configured
**Severity:** INFO
**Status:** OPPORTUNITY

AlphaClaw 0.9.x watchdog supports Discord/Telegram crash notifications. Josh would know the instant Heather goes down. Currently any crash is silent.

**Risk level:** LOW

---

## Summary Table

| Finding | Severity | Status | Day # |
|---------|----------|--------|-------|
| JOSH-39: OpenClaw 2026.5.20 released | HIGH | NEW | 0 |
| JOSH-30: MEMORY.md never created | CRITICAL | PERSISTENT | 33+ |
| JOSH-31: HEARTBEAT.md empty | HIGH | PERSISTENT | 33+ |
| JOSH-32: Bootstrap TOOLS.md false auth state | MEDIUM | PERSISTENT | 33+ |
| JOSH-33: iMessage paused + malformed JSON | MEDIUM | PERSISTENT | 25+ |
| JOSH-34: Emoji contradiction | LOW | PERSISTENT | 1 |
| JOSH-35: Streaming progress available | INFO | OPPORTUNITY | — |
| JOSH-36: Mem0 plugin available | INFO | OPPORTUNITY | — |
| JOSH-37: SOUL.md never personalized | MEDIUM | PERSISTENT | 33+ |
| JOSH-38: Crash notifications | INFO | OPPORTUNITY | — |

---

## Platform Research Notes (2026-05-22)

- **OpenClaw latest stable:** 2026.5.20 (released 2026-05-21 20:44 UTC)
- **OpenClaw 2026.5.19:** (released 2026-05-20) — meme-maker skill, Python debugging skill, browser modal support
- **OpenClaw 2026.5.20:** Discord voice multi-turn, policy plugin, xAI OAuth
- **AlphaClaw latest:** 0.9.16 (May 15, 2026) — no new release today
- **AlphaClaw 0.9.15:** Config restore on fresh boot (important: prevents misconfig on container restart)
- **Key Docker env var:** `OPENCLAW_STATE_DIR=/data/.openclaw` must be set to prevent data loss on restart
- **Memory architecture (2026):** Four-scope model — memories tagged by user_id, agent_id, session_id, org_id. Token-efficient retrieval averages 6,956 tokens/call with +29.6 pt gain on temporal queries
- **iMessage + OpenClaw:** Blooio iPhone-native bridge remains the leading option for iMessage integration
- **Discord voice:** Multi-turn context now maintained between voice messages — upgrade needed to access
- **meme-maker skill:** Available after 2026.5.19 upgrade; relevant for Bliss lifestyle brand social content
