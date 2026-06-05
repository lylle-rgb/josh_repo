# Fleet Research — Josh (Heather Schwartz) | 2026-06-05 Evening Scan

**Scan type:** Platform delta + persistent gap review + web research  
**Date:** 2026-06-05  
**Instance:** Josh Meyers — Heather Schwartz (personal assistant)  
**Repo:** lylle-rgb/josh_repo  
**Prior scan:** 2026-06-03 morning — all findings carried forward (none resolved)

---

## Platform Status

| Item | Current | Latest Stable | Gap |
|------|---------|--------------|-----|
| OpenClaw | 2026.3.22 | **2026.6.2** | **75 days** |
| AlphaClaw | Unknown | 0.9.16 | Check deployment |
| Primary model | google/gemini-3-flash-preview | — | — |

---

## NEW Findings (June 5 Evening Delta)

### FINDING-JOSH-44 | OpenClaw 2026.6.2 Stable — Beta Features Now Available
**Severity:** HIGH  
**Status:** NEW — Platform delta (upgrade unlocks these for Josh)

OpenClaw released 2026.6.2 stable today. All features that were in beta as of the June 3 morning scan are now fully stable and available post-upgrade.

**What ships in 2026.6.2:**
- **Skill Workshop (stable):** Browse, propose, and approve skills from the Control UI without SSH. Josh can add new skills to Heather from his phone or browser. No VPS access needed.
- **Interrupted tool call recovery (stable):** Multi-step sequences (read email → draft → confirm → send) now resume cleanly after any network interruption or container blip. Previously these left Heather in a broken state requiring full conversation restart.
- **iOS hosted push relay (stable):** Push notifications from Heather to Josh's iPhone without self-hosted relay infrastructure. Auto-activates when the iOS app connects post-upgrade.
- **SQLite iMessage state (stable):** The `inbox-state.json` fragility is resolved at the platform level post-upgrade. `openclaw doctor --fix` will migrate iMessage state to SQLite cleanly.
- **Plugin install operator policy (stable):** Safer skill installations — operator policy replaces the old dangerous-code scanner path.
- **Discord channel reliability (stable):** Calmer Discord composer controls, safer duplicate transcript mirrors, steadier streamed-final previews.

**Exact changes to apply:**  
Requires VPS — upgrade from 2026.3.22 to 2026.6.2 (75-day jump; test on dev first, or stage through 2026.5.27 stable).

**Risk level:** MEDIUM (large version jump; recommend staging through 2026.5.27 first)

---

### FINDING-JOSH-45 | No Google Account Connected — Core Assistant Functionality Blocked
**Severity:** CRITICAL  
**Status:** NEW — Configuration gap (confirmed today)

Josh's `hooks/bootstrap/TOOLS.md` explicitly states: **"No Google accounts are currently configured."**

**Impact:**
- Heather cannot read Gmail — zero email monitoring, zero inbox triage
- Heather cannot check Google Calendar — no proactive meeting reminders, no schedule awareness
- Heather cannot access Google Contacts — no contact lookups for Josh's business network
- HEARTBEAT.md is empty *because there is nothing to check* — the proactive monitoring system has no integrations to poll
- Josh's profile (Founder/CEO Bliss, Partner Oben HiFi, LA-based) is exactly the use case that benefits most from email + calendar intelligence

**Exact changes to apply:**  
1. Josh opens AlphaClaw Setup UI: `https://5.78.142.81.sslip.io#general`
2. Under Google Workspace: provide OAuth client credentials from Google Cloud Console
3. Authorize Gmail, Calendar, Contacts (minimum set for personal assistant)
4. Once connected, Heather can begin proactive monitoring

**Risk level:** LOW (no code change — UI-guided OAuth flow)

---

### FINDING-JOSH-46 | Discord Streaming Disabled — Heather Feels Unresponsive
**Severity:** MEDIUM  
**Status:** NEW — Configuration gap

Josh's `openclaw.json` has `"streaming": "off"` for Discord. This means Josh sees nothing until Heather finishes a complete response — potentially 10–30 seconds of silence for complex queries.

**Impact:**
- Josh messages Heather on Discord, sees no response for 10–30s — feels broken, not thinking
- 2026.6.2 specifically improves Discord channel reliability and streamed-final previews
- Real-time streaming would make Heather feel like a live participant, not a slow API call

**Exact changes to apply:**  
In `openclaw.json`, under `channels.discord`:
```json
"streaming": "on"
```
Restart gateway to apply. No other config change needed.

**Risk level:** LOW

---

### FINDING-JOSH-47 | inbox-state.json Malformed JSON (Confirmed)
**Severity:** MEDIUM  
**Status:** CONFIRMED today — malformed duplicate key

`workspace/memory/inbox-state.json` contains a duplicate key:
```json
{
  "already_drafted_imessage_guids": [],
  "already_drafted_thread_ids": ["19db60d96d2118c8"],
  "imessage_monitoring_paused": true,
  "last_email_check_ms": 1777087800000,
  "last_imessage_check_ms": 1777271400000,
  "last_email_check_ms": 1777551900000   ← DUPLICATE KEY
}
```

This is invalid JSON. Any strict parser would silently drop one value; some would error. If/when Google is connected, the inbox monitor may behave unpredictably.

**Revised action (per JOSH-40 update from June 3):** Do NOT manually edit this file now. After upgrading to ≥2026.5.31-stable, run `openclaw doctor --fix` — the SQLite migration will supersede this file and iMessage state will be clean. The duplicate key won't matter once migrated.

**Risk level:** LOW (guidance only — no change required today)

---

### FINDING-JOSH-48 | Apple Watch Integration Now Available
**Severity:** INFO  
**Status:** OPPORTUNITY (available in 2026.6.x)

OpenClaw 2026.6.x adds Apple Watch support — checking messages and sending quick replies directly from the wrist.

**Why it matters for Josh:**
- Josh is a mobile-first LA entrepreneur. Time-sensitive alerts (urgent email, meeting in 30 min, market-relevant news) delivered to Apple Watch = faster response loop
- Heather can push brief status updates without requiring Josh to unlock his phone
- Particularly useful for calendar reminders and urgent message notifications

**Exact changes to apply:**  
Available post-upgrade to 2026.6.2. iOS app connects automatically. No config change needed.

**Risk level:** LOW

---

### FINDING-JOSH-49 | BOOTSTRAP.md Should Be Deleted
**Severity:** LOW  
**Status:** NEW — Stale artifact (confirmed today)

`workspace/BOOTSTRAP.md` still exists. Per the file itself: *"When You're Done — Delete this file. You don't need a bootstrap script anymore — you're you now."*

Josh completed onboarding (Heather has a name, IDENTITY.md is filled, USER.md has Josh's details). BOOTSTRAP.md is dead weight that consumes context tokens on every session startup and could confuse Heather if she re-reads it.

**Exact changes to apply:**  
Delete `workspace/BOOTSTRAP.md` via the AlphaClaw Browse tab or have Heather delete it with: `rm workspace/BOOTSTRAP.md && git commit -am "chore: remove bootstrap artifact"`

**Risk level:** LOW

---

## Persistent Findings (Carried — All Unresolved)

| Finding | Severity | Days Open | Note |
|---------|----------|-----------|------|
| JOSH-30: MEMORY.md never created | **CRITICAL** | 75 | Highest-priority GitHub-only fix |
| JOSH-31: HEARTBEAT.md empty | HIGH | 75 | Zero monitoring (now explained: no Google acct) |
| JOSH-45: No Google account connected | **CRITICAL** | NEW | Blocks all proactive monitoring |
| JOSH-29: Platform 75 days outdated | HIGH | 75 | Requires VPS (2026.3.22 → 2026.6.2) |
| JOSH-37: SOUL.md never personalized | MEDIUM | 75 | GitHub-only, zero risk |
| JOSH-32: TOOLS.md template not filled | MEDIUM | 75 | GitHub-only |
| JOSH-34: Emoji contradiction | LOW | 75 | USER.md says no reactions, AGENTS.md encourages them |
| JOSH-33/40: iMessage paused | MEDIUM | 40 | Wait for SQLite migration at upgrade |

---

## Priority Action Queue

### GitHub-Only (Zero Downtime, Zero VPS Access Needed):

1. **[CRITICAL] Create `workspace/MEMORY.md`** — Heather cannot build long-term memory without this file. A one-paragraph stub is enough to start.
2. **[MEDIUM] Personalize `workspace/SOUL.md`** — See soul-improvements.md for exact additions
3. **[MEDIUM] Fill in `workspace/TOOLS.md`** — Document the setup: no cameras, iMessage paused, Discord is primary channel
4. **[LOW] Delete `workspace/BOOTSTRAP.md`** — Stale artifact consuming tokens
5. **[LOW] Fix emoji contradiction** — Remove the emoji reaction encouragement from AGENTS.md or add USER.md preference to SOUL.md (see soul-improvements.md)

### Requires VPS / Setup UI:

6. **[CRITICAL] Connect Google account** — Setup UI General tab → Google Workspace → authorize Gmail + Calendar + Contacts
7. **[HIGH] Upgrade to 2026.6.2** — Unlocks streaming, interrupted recovery, iOS push, Skill Workshop, SQLite iMessage fix
8. **[MEDIUM] Enable Discord streaming** — `channels.discord.streaming: "on"` after upgrade

---

## Platform Research Notes (2026-06-05)

- **OpenClaw 2026.6.2** released today (stable). This is the version that makes Skill Workshop, interrupted tool call recovery, iOS push relay, and SQLite iMessage state all generally available.
- **Prior beta track (2026.6.1-beta.3, June 3)** has been promoted to stable — no additional beta testing needed.
- **Next expected stable:** 2026.5.31 → promoted to stable mid-to-late June (Tailscale Serve improvements, Communication Notifications settings).
- **Community insight:** Discord streaming improvements in 2026.6.2 specifically address the "no response visible until complete" UX problem — enabling streaming on Josh's instance is now low-risk.
- **Memory research:** A new token-efficient hierarchical memory extraction algorithm (released April 2026) shows +29.6pts on temporal queries and +23.1pts on multi-hop reasoning. Heather cannot leverage this until MEMORY.md exists and is populated — creating it is the highest-leverage action available today.
