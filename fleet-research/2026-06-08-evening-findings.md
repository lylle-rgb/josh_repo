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

**Root cause:** HEARTBEAT.md is empty (see Finding 4).

**Risk level:** LOW to apply; HIGH risk of continued missing activity if left unfixed

---

## Finding 3 — BOOTSTRAP.md Still Exists Post-Onboarding
**Severity:** MODERATE
**What was found:** `workspace/BOOTSTRAP.md` still exists. Onboarding was completed on 2026-03-21.

**Risk level:** LOW — safe to delete

---

## Finding 4 — HEARTBEAT.md Empty — No Proactive Monitoring Active
**Severity:** HIGH
**What was found:** `workspace/HEARTBEAT.md` contains only a comment. No tasks configured. Zero proactive checks running.

**Risk level:** LOW

---

## Finding 5 — No Long-Term Memory (MEMORY.md Missing)
**Severity:** HIGH
**What was found:** `workspace/MEMORY.md` does not exist. No daily memory files exist either.

**Risk level:** LOW — creating a new file; no risk to existing data

---

## Finding 6 — iMessage Monitoring Paused
**Severity:** MODERATE
**What was found:** `workspace/memory/inbox-state.json` contains `"imessage_monitoring_paused": true`. Never revisited.

**Risk level:** LOW — user decision required

---

## Finding 7 — SOUL.md Is Unmodified Generic Template
**Severity:** MODERATE
**What was found:** SHA 792306ac — byte-for-byte default template, zero personalization.

**Risk level:** LOW — additive changes only. See soul-improvements.md.

---

## Finding 8 — TOOLS.md Is Unmodified Generic Template
**Severity:** MODERATE
**What was found:** Default template only, no actual setup documented.

**Risk level:** LOW

---

## Finding 9 — Duplicate JSON Key in inbox-state.json
**Severity:** LOW
**What was found:** `last_email_check_ms` defined twice in `workspace/memory/inbox-state.json`.

**Risk level:** LOW

---

## Finding 10 — hooks/bootstrap/TOOLS.md Says No Google Accounts Configured
**Severity:** LOW
**What was found:** Stale note — Google OAuth was completed 2026-03-21.

**Risk level:** LOW

---

## Web Research — OpenClaw 2026.6.2 Key Features
- Skill Workshop: turn agent work into reusable skills
- Discord voice error fixes
- Channel reliability patches
- Security hardening (corrupt snapshot rejection)
- dreaming memory system (light/deep/REM phases)

**Sources:**
- [OpenClaw Release Notes](https://releasebot.io/updates/openclaw)
- [AlphaClaw](https://alphaclaw.md/)
- [OpenClaw on X](https://x.com/openclaw)
