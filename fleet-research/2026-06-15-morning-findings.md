# Fleet Research — Josh (Heather Schwartz) | 2026-06-15 Morning Scan

**Scan type:** Web research + platform delta
**Date:** 2026-06-15 (morning)
**Instance:** Josh Meyers — Heather Schwartz (personal assistant)
**Repo:** lylle-rgb/josh_repo
**Prior scans:** 2026-06-14 morning | 2026-06-14 evening | 2026-06-15 evening (prior session)
**Researcher:** AlphaClaw Fleet Agent

---

## ⛔ JOSH-50 | FINAL DEADLINE IN 2 DAYS — gemini-2.5-flash Deprecation

**Status:** CRITICAL — June 17, 2026 (2 days)
**Days open:** 3 consecutive scans. Zero action.

`openrouter/google/gemini-2.5-flash` → `openrouter/google/gemini-3.5-flash`

Fix is 30 seconds, zero risk. File: `openclaw.json` → `agents.defaults.model.fallbacks[0]`.

gemini-3.5-flash advantages over gemini-2.5-flash:
- Cheaper: $0.10/M input, $0.40/M output
- Better reasoning benchmarks
- GA since May 19, 2026 (Google I/O)

After June 17: the dead hop will be silently skipped. Apply before June 17.

---

## Platform Status

| Item | Current | Latest | Gap |
|------|---------|--------|-----|
| OpenClaw stable | 2026.3.22 | 2026.6.5 (Jun 3) | 84 days |
| OpenClaw beta | — | 2026.6.5-beta.6 (Jun 9) | Not chasing |
| Primary model | gemini-3-flash-preview | gemini-3.5-flash | Preview |
| Fallback 1 | gemini-2.5-flash (OpenRouter) | gemini-3.5-flash | ⛔ DEAD JUN 17 |
| Fallback 2 | claude-3.5-haiku (Anthropic) | — | OK |

No new OpenClaw stable release since June 3. 2026.6.5 remains the upgrade target.

---

## NEW — Finding 13 | Discord Streaming: Use "progress" Mode (Not Just "on")

**Risk: LOW** | **Dependency: OpenClaw ≥ 2026.5.3** | New this morning

Finding 6 recommended `"streaming": "off"` → `"streaming": "on"`. Web research today reveals a better approach: `"progress"` mode, unified across Discord/Telegram/Slack/Matrix/Teams since v2026.5.3.

**Why "progress" > "on":**
- `"on"` sends raw message chunks and creates an edit storm in Discord when tools fire mid-response
- `"progress"` is progress-aware; batches tool-use turns intelligently; produces cleaner appearance

**Updated action (replaces Finding 6 recommendation):**
```json
"channels": {
  "discord": {
    "streaming": "progress"
  }
}
```

Dependency: OpenClaw ≥ 2026.5.3, included in the 2026.6.5 upgrade target.

---

## NEW — Finding 14 | Nylas CLI: Alternative Email/Calendar Integration Path

**Risk: MEDIUM** | New this morning

Finding 2 / JOSH-45 (Google Workspace not connected) has been open 85+ days. If Google Cloud OAuth setup is the blocker, Nylas CLI provides a parallel path.

**What Nylas provides:**
- 72+ commands: email (read, send, search), calendar (events, RSVPs), contacts
- Supports Gmail, Outlook, Exchange, Yahoo, iCloud, and IMAP
- Single authentication flow — no GCP project needed

**Install:** `openclaw skill install nylas-cli`

**Risk tradeoff:** Nylas is middleware — email content transits a third-party API. More appropriate for lower-sensitivity accounts or as a path forward if GCP OAuth remains blocked after 90+ days.

**Why it matters:** Heather has not read Josh's email or iMessage in 85+ days. The current integration is fully offline. If the Google OAuth path is stalled, Nylas is an actionable alternative that can restore email and calendar access today.

---

## NEW — Finding 15 | NVIDIA SkillSpector Skill Security (Post-Upgrade Passive Benefit)

**Risk: LOW (passive)** | New this morning

OpenClaw 2026.6.1 introduced Skill Workshop with NVIDIA SkillSpector scanning. After upgrading:
- Every ClawHub skill ships with a Skill Card (documents what it does and its data access scope)
- All skills are scanned for prompt injection, hidden instructions, and agentic risks
- Review queue available before skills touch production workflows

**Why it matters for Heather:** Heather has access to Josh's personal calendar, contacts, and business communications. A compromised skill could exfiltrate sensitive executive data. Post-upgrade, ClawHub skill installations carry security attestation.

**Action:** None needed now. Passive benefit that activates automatically on upgrade to 2026.6.5 (Finding 1).

---

## Confirmed — Cron Reliability Improvements in 2026.6.5

Two passive improvements confirmed active in current stable:

1. **No more startup bursts:** Overdue cron jobs are rescheduled on gateway startup instead of replayed immediately. Previously, restarting OpenClaw fired all overdue jobs at once, causing Discord disruption.

2. **Orphaned tab cleanup:** Isolated cron browser sessions now close their tabs when the run completes. Long-running setups with browser automation no longer accumulate orphaned processes over time.

Both improvements activate automatically after upgrade to 2026.6.5.

---

## Confirmed — Brave Search: 700K OpenClaw Users (Ecosystem Standard)

Brave Search API crossed 700K OpenClaw users as of June 2026 — the leading web search provider in the ecosystem. OpenClaw auto-detects a `BRAVE_API_KEY` environment variable and routes web search through it automatically.

**Josh's config status:** No BRAVE_API_KEY confirmed in environment.
**Action (optional):** Add `BRAVE_API_KEY` via AlphaClaw Envars tab. Free tier available. Enables richer web research for Heather's tasks (Bliss competitor lookups, market research for Josh, etc.).

---

## Noah Repo — Still Inaccessible

`lylle-rgb/noah--repo` returns 404. GitHub shows two candidate repos: `Noahrepo2` (updated 2026-03-08) and `Noah-workspace` (updated 2026-03-07). Neither is in this session's permitted scope.

Noah analysis is completely blind this session. See `cross-customer-analysis.md` for fleet-level impact.

**Action required (fleet operator):** Update session scope to correct Noah repo name (`Noahrepo2` or `Noah-workspace`).

---

## Open Findings Status (Morning Count — Day 11 No Action)

| Finding | Days Open | Status |
|---------|-----------|--------|
| ⛔ JOSH-50: gemini-2.5-flash deadline | 3 | CRITICAL — 2 days left |
| JOSH-45: No Google account | 85+ | CRITICAL |
| JOSH-30: No MEMORY.md | 85+ | CRITICAL |
| JOSH-31: HEARTBEAT.md empty | 85+ | HIGH |
| JOSH-44: 84 days outdated | 84 | HIGH |
| JOSH-34: Emoji rule contradiction | 85+ | HIGH |
| Finding 13: streaming progress mode | 0 | 🆕 New today |
| Finding 14: Nylas CLI alternative | 0 | 🆕 New today |
| Finding 15: SkillSpector post-upgrade | 0 | 🆕 New today |

---

*Sources: [OpenClaw Streaming docs](https://docs.openclaw.ai/concepts/streaming), [Nylas CLI OpenClaw guide](https://cli.nylas.com/guides/nylas-openclaw-personal-assistant), [OpenClaw NVIDIA Skill Workshop](https://openclaw.ai/blog/openclaw-nvidia-skill-security), [Brave Search/OpenClaw blog](https://brave.com/blog/openclaw/), [OpenClaw release notes](https://releasebot.io/updates/openclaw), [SEN-X OpenClaw 2026.6.1](https://senx.ai/openclaw-news/2026-06-02-openclaw-news), [MindStudio April 2026 update](https://www.mindstudio.ai/blog/openclaw-april-2026-update-new-features-agentic-runtime)*
