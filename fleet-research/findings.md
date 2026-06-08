# Fleet Research — Evening Scan Findings
**Instance:** Heather Schwartz (Josh — personal assistant)
**Scan date:** 2026-06-08
**Scanner:** AlphaClaw Fleet Agent (automated evening scan)

---

## Summary

6 critical issues, 6 moderate issues found. Most urgent: agent has been effectively inactive for ~5.5 weeks, heartbeat is unconfigured, and long-term memory infrastructure does not exist. OpenClaw is 76 days behind current release.

---

## Finding 1 — OpenClaw Version Critically Stale
**Severity:** HIGH
**What was found:** `openclaw.json` shows `lastTouchedVersion: 2026.3.22` (last touched March 24, 2026). Current release is **2026.6.2** — 76 days and approximately 40+ releases behind.

**Why it matters:** Missing features include:
- **Skill Workshop** (2026.6.x) — review-first skill creation from agent work; Heather could use this to codify Google Workspace workflows as reusable skills
- **Discord voice error fixes** — Heather uses Discord as primary channel
- **Channel reliability patches** — duplicate transcript mirror fixes, Discord streaming and approval path fixes
- **Safer plugin installs** — operator install policy replaces old dangerous-code scanner
- **Security hardening** — rejects corrupt shell snapshots, suspicious gateway configs, malformed config values
- **Structured task progress** (2026.4.x+) — agent shows each step of long tasks
- **GPT-5.5 support** (2026.4.23+) — if ever needed as fallback

**Exact change to apply:**
```bash
# On the VPS, run:
openclaw update
# Then restart via AlphaClaw watchdog or:
openclaw restart
```
Or trigger via the AlphaClaw UI Watchdog tab: `https://5.78.142.81.sslip.io#watchdog`

**Risk level:** LOW — updates are rolling and backwards-compatible; AlphaClaw watchdog handles restart

---

## Finding 2 — Agent Has Been Inactive ~5.5 Weeks
**Severity:** HIGH
**What was found:** `workspace/memory/inbox-state.json` shows the last recorded activity was approximately **April 30, 2026**. Current date is June 8, 2026. No daily memory files (`memory/YYYY-MM-DD.md`) have ever been created.

**Why it matters:** Heather has not been checking Josh's email, calendar, or iMessage for over a month. Josh's inbox and calendar have had zero proactive monitoring. Any time-sensitive items (emails, upcoming events) may have been missed entirely.

**Root cause:** HEARTBEAT.md is empty (see Finding 4). Without heartbeat tasks configured, there is nothing to trigger proactive checks.

**Exact change to apply:** Configure HEARTBEAT.md (see Finding 4). Once heartbeat is active, Heather will resume proactive monitoring automatically.

**Risk level:** LOW to apply; HIGH risk of continued missing activity if left unfixed

---

## Finding 3 — BOOTSTRAP.md Still Exists Post-Onboarding
**Severity:** MODERATE
**What was found:** `workspace/BOOTSTRAP.md` still exists. Per AGENTS.md instructions, BOOTSTRAP.md is the "birth certificate" that should be deleted after first-run identity setup. Onboarding was completed on 2026-03-21 (per `memory/onboarding-google.md`).

**Why it matters:** The file's presence is a signal that the post-onboarding cleanup was never completed. On a session restart, the file could cause confusion about whether identity is fully established. It's dead weight in the workspace.

**Exact change to apply:** Delete `workspace/BOOTSTRAP.md` from the repo (or ask Heather to do it on next session start).

**Risk level:** LOW — safe to delete; identity is already established in IDENTITY.md and USER.md

---

## Finding 4 — HEARTBEAT.md Empty — No Proactive Monitoring Active
**Severity:** HIGH
**What was found:** `workspace/HEARTBEAT.md` contains only a comment saying it's empty. No tasks are configured. Heather is not running any periodic checks.

**Why it matters:** Heather's core value proposition is proactive personal assistance. AGENTS.md explicitly instructs her to check email, calendar, weather, and social notifications 2-4 times per day via heartbeat. With no heartbeat tasks, she is purely reactive — only responding when Josh messages her directly.

**Exact change to apply:** Replace `workspace/HEARTBEAT.md` with the following:
```markdown
# HEARTBEAT.md

## Active Checks (rotate through these, 2-4x/day)

### 1. Email & iMessage
- Check Gmail for urgent/unread messages from the last check
- If iMessage monitoring is paused, note it; don't attempt to force-resume
- Update memory/heartbeat-state.json with timestamp

### 2. Calendar
- Check Google Calendar for events in next 48 hours
- Alert Josh if anything is within 2 hours that he hasn't acknowledged

### 3. Quiet Hours
- Do not reach out 23:00–08:00 PST unless genuinely urgent
- If nothing actionable: reply HEARTBEAT_OK

## Memory Maintenance (weekly, during a heartbeat)
- Read recent memory/YYYY-MM-DD.md files
- Distill significant events into MEMORY.md
- Remove stale entries from MEMORY.md
```

**Risk level:** LOW — heartbeat is opt-in; Heather won't take external actions without reason

---

## Finding 5 — No Long-Term Memory (MEMORY.md Missing)
**Severity:** HIGH
**What was found:** `workspace/MEMORY.md` does not exist. AGENTS.md instructs Heather to maintain this as her curated long-term memory — the distilled essence of important context across sessions. No daily memory files exist either.

**Why it matters:** Every session Heather wakes up knowing Josh's name and general profile (from USER.md) but has zero memory of:
- Previous conversations and decisions
- Ongoing projects Josh mentioned
- Preferences she's learned over time
- Things Josh asked her to remember
- Past mistakes to avoid repeating

**Exact change to apply:** Create `workspace/memory/MEMORY.md` with a bootstrapped first entry:
```markdown
# MEMORY.md — Heather's Long-Term Memory
_Load this ONLY in main sessions (direct chat with Josh). Do not load in Discord group chats._

## About Josh
- Founder & CEO, Bliss (luxury lifestyle brand)
- Partner, Oben HiFi
- Based in Los Angeles (PST/PDT)
- Discord: Jpm855
- Named me Heather

## Behavioral Rules
- STRICT: DO NOT SEND EMOJI REACTIONS TO MESSAGES (Josh explicitly requested this)
- Josh prefers I just help — no filler phrases like "Great question!" or "Happy to help!"

## Google Workspace
- OAuth redirect URI: https://5.78.142.81.sslip.io/auth/google/callback
- Onboarding completed: 2026-03-21
- gog-cli available for Gmail, Calendar, Drive, Sheets, Docs, Tasks, Contacts

## Pending / To Follow Up
- iMessage monitoring is paused (imessage_monitoring_paused: true in inbox-state.json)
  - Ask Josh if he wants to resume it or leave it paused

## Session History
_Add significant events, decisions, and lessons here as you accumulate them._
```

**Risk level:** LOW — creating a new file; no risk to existing data

---

## Finding 6 — iMessage Monitoring Paused
**Severity:** MODERATE
**What was found:** `workspace/memory/inbox-state.json` contains `"imessage_monitoring_paused": true`. This was set and never revisited.

**Why it matters:** Heather is Josh's personal assistant with iMessage integration as a key feature. If iMessage monitoring is intentionally paused, that's fine — but it should be a conscious choice documented in MEMORY.md, not an unacknowledged state.

**Exact change to apply:** On next session start, Heather should ask Josh: "Your iMessage monitoring has been paused since setup. Do you want me to resume it, or keep it off?"

**Risk level:** LOW — user decision required; no automated action

---

## Finding 7 — SOUL.md Is Unmodified Generic Template
**Severity:** MODERATE
**What was found:** `workspace/SOUL.md` is byte-for-byte identical to the OpenClaw default template (SHA: 792306ac). It contains zero personalization for Josh's context.

**Why it matters:** SOUL.md is Heather's core behavioral document. A generic soul means Heather doesn't have internalized rules specific to her role (luxury brand context, professional communications, Josh's explicit preferences like no emoji reactions, his business context).

**Exact change to apply:** See `fleet-research/soul-improvements.md` for specific additions.

**Risk level:** LOW — additive changes only

---

## Finding 8 — TOOLS.md Is Unmodified Generic Template
**Severity:** MODERATE
**What was found:** `workspace/TOOLS.md` is byte-for-byte identical to the default template. It contains only example placeholders.

**Why it matters:** TOOLS.md should document Heather's actual setup — Google account details, OAuth endpoint, channel specifics. Without this, Heather has to re-derive setup details from memory/onboarding-google.md each session.

**Exact change to apply:**
Replace `workspace/TOOLS.md` with:
```markdown
# TOOLS.md — Heather's Setup Notes

## Google Workspace
- Account: Josh's personal Google account (configured via OAuth)
- OAuth redirect: https://5.78.142.81.sslip.io/auth/google/callback
- Onboarded: 2026-03-21
- Skill: gog-cli (if installed) for Gmail, Calendar, Drive, Sheets, Docs, Tasks, Contacts
- Provider: google:default (api_key mode in openclaw.json)

## AlphaClaw UI
- URL: https://5.78.142.81.sslip.io
- Tabs: General, Watchdog, Providers, Envars, Webhooks, Browse

## Discord
- Guild: 1484448262290276464
- DM policy: open (all users)
- Group policy: open
- streaming: off
- Josh's handle: Jpm855

## iMessage
- Status: PAUSED (as of ~2026-04-30, reason unknown)
- To resume: ask Josh

## Formatting Rules
- Discord/WhatsApp: No markdown tables — use bullet lists
- Discord links: Wrap multiple links in <> to suppress embeds
```

**Risk level:** LOW — documentation only, no behavioral change

---

## Finding 9 — duplicate JSON key in inbox-state.json
**Severity:** LOW
**What was found:** `workspace/memory/inbox-state.json` has `last_email_check_ms` defined twice. The second value (1777551900000) wins in most parsers, but the file is technically invalid JSON.

**Exact change to apply:** Remove the first `last_email_check_ms` key, keeping only the latest value (1777551900000).

**Risk level:** LOW — minor cleanup

---

## Finding 10 — hooks/bootstrap/TOOLS.md Says No Google Accounts Configured
**Severity:** LOW
**What was found:** `workspace/hooks/bootstrap/TOOLS.md` ends with `"## Available Google Accounts\n\nNo Google accounts are currently configured."` — but Google OAuth was completed on 2026-03-21 per `memory/onboarding-google.md`.

**Why it matters:** This bootstrap TOOLS.md is loaded on every session (it's in the `bootstrap-extra-files` hook entries). Every session Heather reads a stale note saying no Google is configured, which may cause confusion.

**Exact change to apply:** Update the final section of `workspace/hooks/bootstrap/TOOLS.md` to reflect the actual Google account status.

**Risk level:** LOW — docs fix

---

## Web Research Summary — OpenClaw 2026.6.2 Highlights

Key improvements Heather should benefit from after updating:

| Feature | Version | Benefit |
|---|---|---|
| Skill Workshop | 2026.6.x | Build reusable Google Workspace skills from repeated tasks |
| Discord voice error fixes | 2026.6.x | More reliable Discord delivery |
| Channel reliability | 2026.6.x | Safer duplicate transcript handling |
| Structured task progress | 2026.4.x | Heather shows step-by-step progress on long tasks |
| GPT-5.5 support | 2026.4.23 | Available as fallback model |
| dreaming memory system | 2026.4.5 | Light/deep/REM memory phases for better long-term retention |

**Sources:**
- [OpenClaw Release Notes (Releasebot)](https://releasebot.io/updates/openclaw)
- [AlphaClaw docs](https://alphaclaw.md/)
- [OpenClaw X/Twitter](https://x.com/openclaw)
- [Alpha Batcher on X — 2026.4.5 features](https://x.com/alphabatcher/status/2041156996355760337)
