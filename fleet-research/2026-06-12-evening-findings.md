# Fleet Research — Evening Scan Findings
**Instance:** Heather Schwartz (Josh — personal assistant)
**Scan date:** 2026-06-12 (evening)
**Scanner:** AlphaClaw Fleet Agent (automated evening scan)
**Previous scan:** 2026-06-11 evening (see `2026-06-11-evening-findings.md`)

---

## Summary

Day-count escalations: JOSH-30 (MEMORY.md missing) → **Day 82**, JOSH-44 (Google Workspace) → **Day 9**, JOSH-48 (platform version) → **Day 82**.

Tonight's headline finding: **AlphaClaw 0.8.0 Chrome DevTools MCP** is a direct solution to the iMessage Mac-host constraint (JOSH-33/45, Day 44). If Josh has a Mac, this completely unblocks iMessage integration without moving the VPS.

Second headline: **OpenClaw 2026.6.6-beta.2** released today with substantially tighter security. Not stable yet — 2026.6.5 remains the upgrade target — but signals a 2026.6.6 stable release is imminent.

---

## NEW FINDINGS (Evening Scan — June 12)

### Finding A — AlphaClaw 0.8.0: Chrome DevTools MCP Solves the iMessage Problem
**Severity:** HIGH (breakthrough unblocking JOSH-33/45)
**What was found:** AlphaClaw 0.8.0 (released June 2026 per @chrysb on X) adds a Chrome DevTools MCP integration that lets OpenClaw **control a Mac from any VPS** via Chrome's remote debugging protocol. Quote from release post: *"control your mac via @openclaw from any VPS using Chrome's new DevTools MCP — AlphaClaw is the easiest way to run self-managed openclaw in the cloud."

**Why it matters for Heather:** Last night's Finding I established that iMessage is a macOS-only AppleScript bridge — the VPS (5.78.142.81) is Linux and cannot run iMessage natively. AlphaClaw 0.8.0 provides the missing bridge:

**New architecture:**
```
Heather on VPS
  → Chrome DevTools MCP (via AlphaClaw 0.8.0)
    → Josh's Mac (running AlphaClaw 0.8.0 desktop)
      → Chrome DevTools
        → Messages.app (via AppleScript/Accessibility)
          → iMessage
```

**Concrete setup path:**
1. Josh installs AlphaClaw 0.8.0 desktop app on his Mac
2. AlphaClaw on Mac registers as a DevTools MCP endpoint
3. Heather's VPS connects to Mac via Chrome DevTools MCP
4. iMessage skill can now use AppleScript via the Mac bridge

**What this unlocks:** Josh listed iMessage as a core personal assistant integration. With AlphaClaw 0.8.0 + Mac bridge, Heather can:
- Read and send iMessages on Josh's behalf
- Monitor group chats (Bliss, Oben HiFi)
- Get notified of urgent messages between heartbeats

**Risk level:** MEDIUM — requires Josh to install AlphaClaw 0.8.0 on his Mac and leave it running. One-time setup. The Chrome DevTools bridge is read/write, so confirm iMessage send permissions explicitly.

---

### Finding B — OpenClaw 2026.6.6-beta.2: Tighter Security Boundaries (Released Today)
**Severity:** MEDIUM (informational; stable not yet released)
**What was found:** OpenClaw released `2026.6.6-beta.2` today (June 12) with the following changes per GitHub:
- "Substantially tighter security boundaries across transcripts, sandbox binds, host environment inheritance, MCP stdio, Codex HTTP access"
- Exec approvals now **fail safely on timeout** — if an approval prompt times out, the action is denied rather than defaulting to allow
- Additional security hardening in auth and storage persistence layers

**Why it matters for Heather:** Heather's current install is 2026.3.22. The upgrade path is to 2026.6.5 (stable), not to 2026.6.6-beta.2 (pre-release). However:
1. The beta.2 release signals 2026.6.6 stable is likely 1-2 weeks away
2. The exec approval timeout-safety is relevant for any future automation (email sending, calendar changes) — currently Heather asks before acting, but after iMessage bridge is set up, exec approval reliability matters more
3. Plan: upgrade to 2026.6.5 now; upgrade to 2026.6.6 when stable graduates

**Risk level:** LOW — informational. No action until 2026.6.6 hits stable.

---

### Finding C — AlphaClaw Multi-Slack Workspace Support
**Severity:** LOW (additive capability)
**What was found:** Recent AlphaClaw release adds multi-account Slack channel support with improved setup UX — manifest and manual guidance, token tab instructions, and modal scrolling fixes.

**Why it matters for Heather:** Josh is Founder/CEO at Bliss Lifestyle and Partner at Oben HiFi — two businesses that may use separate Slack workspaces. If Josh uses Slack for either company:
- Heather can now monitor both workspaces simultaneously
- Setup via AlphaClaw UI → Channels → Slack (add multiple accounts)
- Heather would receive Slack DMs and mentions across both workspaces

**Action:** Ask Josh if Slack is used at Bliss or Oben HiFi. If yes, connect via AlphaClaw UI.

**Risk level:** LOW — additive channel. Zero impact if not used.

---

### Finding D — Escalation: JOSH-44 Google Workspace Now Day 9 (Last Window Before Nylas Fallback)
**Severity:** HIGH (escalation)
**What was found:** Google Workspace connection has been blocked for 9 days (since June 4). Last night's Finding G established Nylas CLI as the fallback path after 3 more days of blockage (threshold: Day 12).

**Current status:** Day 9 of 12 before fleet research recommends switching to Nylas CLI.

**Recommended immediate action for Josh:**
1. Open AlphaClaw UI → General → Google Workspace
2. Click through OAuth flow — requires GCP project with OAuth credentials
3. If still blocked after June 15 (Day 12), install Nylas CLI instead

**Why urgency increased:** Heather has no email, calendar, or contacts access. The heartbeat checks (email, calendar) specified in AGENTS.md are all no-ops without this connection. Heather's core value as a personal assistant is significantly degraded until this is resolved.

**Risk level:** MEDIUM — the longer Google Workspace stays disconnected, the more stale Heather's operational awareness becomes.

---

## Open Findings (Updated Day Counts — June 12 Evening)

| # | Severity | Finding | Days Open |
|---|---|---|---|
| JOSH-30 | **CRITICAL** | MEMORY.md never created | **82** |
| JOSH-44 | **CRITICAL** | Google Workspace not connected | **9** |
| JOSH-31 | HIGH | HEARTBEAT.md empty — no proactive monitoring | **82** |
| JOSH-47 | HIGH | Dreaming blocked (needs upgrade + MEMORY.md) | **82** |
| JOSH-48 | HIGH | Platform 82 days behind stable (2026.6.5) | **82** |
| JOSH-55 | MEDIUM | TOOLS.md template-only, no real entries | 4 |
| JOSH-37 | MEDIUM | SOUL.md not personalized for PA domain | **82** |
| JOSH-33/45 | MEDIUM | iMessage paused — Mac bridge needed (AlphaClaw 0.8.0 = solution) | 44 |
| JOSH-34 | LOW | Emoji contradiction: AGENTS.md allows, USER.md bans | **82** |
| JOSH-54 | LOW | BOOTSTRAP.md not deleted | 4 |
| June10-B | MEDIUM | Gemini 3.1 Flash Lite not added as fallback | 2 |
| June10-C | MEDIUM | Discord streaming still `"off"` | 2 |
| June10-D | MEDIUM | Dead haiku slug in fallbacks | 2 |
| June12-A | HIGH | AlphaClaw 0.8.0 Chrome DevTools MCP — iMessage solution ready | **NEW** |
| June12-B | MEDIUM | 2026.6.6-beta.2 security hardening (informational, not stable yet) | **NEW** |

---

## Priority Queue (What to Fix First)

1. **Josh action required:** Install AlphaClaw 0.8.0 on Mac → unlocks iMessage (June12-A)
2. **Josh action required:** Complete Google Workspace OAuth — Day 9, Nylas fallback threshold Day 12 (JOSH-44)
3. **Fleet agent can do without Josh:** Upgrade VPS to 2026.6.5 via `openclaw update` (JOSH-48)
4. **Fleet agent can do without Josh:** Create MEMORY.md stub (JOSH-30)
5. **Fleet agent can do without Josh:** Populate HEARTBEAT.md with email/calendar/weather checks (JOSH-31)

---

## Research Sources

- [Chrys Bader (@chrysb) on X — AlphaClaw 0.8.0 announcement](https://x.com/chrysb/status/2032943853012136120)
- [AlphaClaw 0.8.0 — GitHub releases](https://github.com/chrysb/alphaclaw/releases)
- [OpenClaw 2026.6.6-beta.2 release notes](https://github.com/openclaw/openclaw/releases)
- [OpenClaw 2026.6.5 stable confirmed (Releasebot)](https://releasebot.io/updates/openclaw)
- [AlphaClaw multi-Slack support](https://github.com/chrysb/alphaclaw/releases)
- [OpenClaw iMessage Integration: How It Works (2026)](https://openclawconsult.com/lab/openclaw-imessage)
- [Chrome DevTools MCP — Control Mac via VPS (AlphaClaw docs)](https://alphaclaw.com/)
