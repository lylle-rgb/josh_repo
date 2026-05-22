# Fleet Research — Josh (Heather Schwartz) | 2026-05-22 Morning Scan

**Scan type:** Morning (web research + platform news + workspace gap check)
**Date:** 2026-05-22
**Instance:** Josh Meyers — Heather Schwartz (personal assistant)
**Repo:** lylle-rgb/josh_repo
**Previous scan:** 2026-05-22 evening (2026.5.20 confirmed as upgrade target)

---

## Platform Status

| Item | Current | Latest Stable | Latest Alpha | Gap |
|------|---------|--------------|-------------|-----|
| OpenClaw | 2026.3.22 | **2026.5.20** | 2026.5.21-alpha.1 | ~2 months behind |
| AlphaClaw | Unknown | 0.9.16 | — | Check deployment |
| Primary model | google/gemini-3-flash-preview | — | Active (Gemini 3.1 Flash Lite also available) |

---

## New Since Last Scan

### FINDING-JOSH-40 | OpenClaw 2026.5.21-alpha.1 Released Overnight
**Severity:** INFO
**Status:** NEW (alpha — do not deploy)

OpenClaw shipped 2026.5.21-alpha.1 overnight. Latest **stable** target remains 2026.5.20 — do not upgrade Josh to alpha.

**What's in 2026.5.21-alpha.1 (tracking for stabilization):**
- Paced audio streaming + backpressure-aware buffering for voice sessions — directly relevant to Heather's voice features
- Barge-in queue clearing — eliminates interruption lag in voice conversations
- Discord: voice sessions now follow configured Discord users into voice channels (allowed-channel checks, multi-user handoff, DAVE recovery)
- Bounded partial recall summaries in Active Memory — context that previously discarded on sub-agent timeout is now partially recovered
- Telegram streaming polish (not relevant — Heather uses Discord)

**Action:** None yet. Re-check when alpha stabilizes (~3-7 days).

**Risk level:** N/A (alpha — no action)

---

### FINDING-JOSH-41 | Active Memory allowedChatIds — Protect MEMORY.md From Open Guild
**Severity:** MEDIUM
**Status:** NEW (available post-upgrade to 2026.5.x)

OpenClaw's Active Memory now supports per-conversation `allowedChatIds` and `deniedChatIds` filters. When configured, memory recall only fires in approved conversation contexts — all others silently skip.

**Why it matters for Heather/Josh:**
Josh's MEMORY.md (once created — FINDING-JOSH-30) will contain private personal context: schedule, contacts, iMessage threads, business details. Josh's Discord guild has `groupPolicy: "open"` — any Discord user can message Heather. Without `allowedChatIds`, Active Memory would surface MEMORY.md contents in those open group conversations.

**Exact config (add after creating MEMORY.md and enabling memory-core post-upgrade):**
```json
"active-memory": {
  "enabled": true,
  "config": {
    "agents": ["main"],
    "chatTypes": ["dm"],
    "allowedChatIds": ["JOSH_DIRECT_DM_CHANNEL_ID"],
    "inheritSessionModel": true,
    "timeout": 12000,
    "setupGraceTimeoutMs": 5000,
    "maxSummaryChars": 220
  }
}
```
Replace `JOSH_DIRECT_DM_CHANNEL_ID` with Josh's actual Discord DM channel ID (visible in gateway logs on first connection).

**Risk level:** LOW — protective privacy control, no downside once MEMORY.md exists

---

### FINDING-JOSH-42 | Replace Dead claude-3.5-haiku Fallback with Gemini 3.1 Flash Lite
**Severity:** MEDIUM
**Status:** NEW (actionable now — no upgrade needed)

Josh's current fallback chain includes `openrouter/anthropic/claude-3.5-haiku` — a retired model that no longer responds. Gemini 3.1 Flash Lite is now confirmed available on OpenRouter: same cost tier, lower latency, aligns with Josh's Gemini-first stack.

**Exact fix (no restart, no upgrade required):**
```json
"model": {
  "primary": "google/gemini-3-flash-preview",
  "fallbacks": [
    "openrouter/google/gemini-3.1-flash-lite-preview",
    "openrouter/google/gemini-2.5-flash",
    "openrouter/anthropic/claude-haiku-4-5"
  ]
}
```
This replaces the dead haiku model with the active Gemini 3.1 Flash Lite, adds claude-haiku-4-5 as tertiary fallback.

**Risk level:** NEGLIGIBLE — fallback-only change, no impact on normal operation

---

### FINDING-JOSH-43 | Discord Voice Channel-Following Stable in 2026.5.20
**Severity:** INFO
**Status:** NEW (available after upgrade to 2026.5.20)

In 2026.5.20, Discord voice sessions can follow configured Discord users into voice channels: when Josh joins a voice channel, Heather can automatically join. Includes allowed-channel checks (only follows approved channels), multi-user handoff, and DAVE protocol recovery.

**Why it matters:** If Josh uses Discord voice for work calls or meetings, Heather can join and assist — note-taking, calendar lookups, reminders — without Josh having to switch to text. Low-interruption assistance.

**Dependency:** Requires upgrade to 2026.5.20 first (Josh is at 2026.3.22).

**Risk level:** LOW — optional feature, no impact on current Discord text functionality

---

## Persistent Findings (Unresolved — Day Counts Escalating)

### FINDING-JOSH-30 | MEMORY.md Never Created — Day 35
**Severity:** CRITICAL | **Day:** 35 | **Status:** PERSISTENT

No MEMORY.md. No memory/ directory. Every heartbeat instruction to "read MEMORY.md if in MAIN SESSION" is unanswerable. 35 sessions of zero continuity.

**One-time fix:** Create `workspace/MEMORY.md` with starter sections: Identity, Josh Context, Key Facts, Lessons Learned. Takes 5 minutes.

---

### FINDING-JOSH-31 | HEARTBEAT.md Empty — Day 35
**Severity:** HIGH | **Day:** 35 | **Status:** PERSISTENT

168-byte template. Every heartbeat pulse returns HEARTBEAT_OK. No Gmail, no Calendar, no iMessage monitoring. Heather is completely reactive when she should be proactively checking in 2-4 times daily.

---

### FINDING-JOSH-32 | Bootstrap TOOLS.md States "No Google Accounts" — Day 35
**Severity:** MEDIUM | **Day:** 35 | **Status:** PERSISTENT

`workspace/hooks/bootstrap/TOOLS.md` declares "No Google accounts are currently configured." — false since Day 1. Josh has `google:default` active in api_key mode. Every session bootstrap injects incorrect capability context.

**Fix:** Replace false statement with accurate Google Workspace section (see 2026-05-22 evening findings for exact text).

---

### FINDING-JOSH-33 | iMessage Paused + Malformed JSON — Day 26
**Severity:** MEDIUM | **Day:** 26 | **Status:** PERSISTENT

`inbox-state.json` has `imessage_monitoring_paused: true` and a duplicate `last_email_check_ms` key (invalid JSON). iMessage bridge paused for 26+ days. JSON parsers that error on duplicates will fail to parse this file.

---

### FINDING-JOSH-34 | Emoji Contradiction USER.md vs AGENTS.md
**Severity:** LOW | **Status:** PERSISTENT

USER.md: "STRICT: DO NOT SEND EMOJI REACTIONS." AGENTS.md: full "React Like a Human!" section promoting emoji reactions. Explicit user preference not enforced in SOUL.md.

---

### FINDING-JOSH-37 | SOUL.md Never Personalized — Day 35
**Severity:** MEDIUM | **Day:** 35 | **Status:** PERSISTENT

SOUL.md SHA 792306ac — identical to Noah's and to the upstream generic template. 35 days of Heather-specific context (Bliss luxury brand, Oben HiFi, LA timezone, emoji rule) not written back. Every session Heather starts without knowing who she is.

---

## Summary Table

| Finding | Severity | Status | Day # |
|---------|----------|--------|-------|
| JOSH-40: OpenClaw 2026.5.21-alpha.1 | INFO | NEW (alpha, no action) | 0 |
| JOSH-41: Active Memory allowedChatIds | MEDIUM | NEW (post-upgrade) | 0 |
| JOSH-42: Dead claude-3.5-haiku fallback | MEDIUM | **NEW — fix now** | 0 |
| JOSH-43: Discord voice channel-following | INFO | NEW (post-upgrade) | 0 |
| JOSH-30: MEMORY.md never created | CRITICAL | PERSISTENT | **35** |
| JOSH-31: HEARTBEAT.md empty | HIGH | PERSISTENT | **35** |
| JOSH-32: Bootstrap false auth state | MEDIUM | PERSISTENT | **35** |
| JOSH-33: iMessage paused + bad JSON | MEDIUM | PERSISTENT | 26 |
| JOSH-34: Emoji contradiction | LOW | PERSISTENT | — |
| JOSH-37: SOUL.md never personalized | MEDIUM | PERSISTENT | **35** |

---

## Research Notes (2026-05-22 Morning)

- **Latest stable:** 2026.5.20 (unchanged from evening scan)
- **Latest alpha:** 2026.5.21-alpha.1 (released overnight — voice/audio focus, bounded recall summaries)
- **Gemini 3.5 Flash:** Released with higher prices — not recommended for Heather's heartbeat use case; 3.1 Flash Lite is the right upgrade tier
- **Gemini 3.1 Flash Lite:** Confirmed on OpenRouter — ideal dead-haiku replacement (JOSH-42, actionable today)
- **Active Memory allowedChatIds:** Confirmed stable — critical privacy control given Josh's open Discord guild policy
- **Voice channel-following:** Stable in 2026.5.20 — enables voice-first assistance if Josh adopts
- **Bounded partial recall:** Alpha feature — Active Memory now recovers partial context on sub-agent timeout rather than discarding entirely
- **Immediate action (no upgrade needed):** Fix dead fallback model (JOSH-42) — 3-minute JSON edit, should be done today
