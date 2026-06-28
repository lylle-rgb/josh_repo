# Fleet Research: Josh (Heather) — Morning Scan
**Date:** 2026-06-28 | **Scan type:** Morning | **Agent:** AlphaClaw Fleet Research

## Summary
Day 99 of Google Workspace downtime — **Day 100 is TOMORROW (June 29)**. 2026.6.11 confirmed still in beta (NOT stable) — stay on 2026.6.10 target. AlphaClaw Apex announced: native Mac app for multi-VPS fleet management, directly relevant for fleet admin. AlphaClaw 0.9.18 confirmed current (no 0.9.19 released). Noah scope broken Day 20. Upgrade window fully open: 2026.6.10-stable Day 5, no new regressions reported overnight.

---

## New Findings

### F63 — INFO/CORRECTION: 2026.6.11 Confirmed Still Beta — Stay on 2026.6.10
**Risk:** LOW — important correction to June 28 evening's "possibly stable" flag

- June 28 evening scan (F5) flagged: "2026.6.11 may have progressed toward stable"
- **Morning research confirms: 2026.6.11 is still in beta.** `npm show openclaw@latest version` still returns 2026.6.10.
- Features slated for 2026.6.11 (when it stabilizes): per-DM model overrides, `openclaw agent --message-file` batch workflows, RAFT CLI wake bridge, richer Discord output, Slack relay mode, native Mattermost /oc_queue, stronger channel control
- Per-DM model overrides and file-driven workflows are directly useful for Josh's use case — worth monitoring for stable promotion
- **Action:** Upgrade target unchanged — **2026.6.10**. After landing there, verify: `npm show openclaw@2026.6.11 version` before considering the next hop.

---

### F64 — INFO: AlphaClaw Apex Announced — Native Mac Fleet Manager
**Risk:** LOW — informational / fleet admin opportunity

Chrys Bader (@chrysb) announced **AlphaClaw Apex**: a native Mac app for managing multiple OpenClaw VPS instances from a single dashboard. Directly relevant for fleet admin (lylle@lxrcap.com) who currently oversees both Josh and Noah instances.

**Key capabilities:**
- Deploy new instances to Hetzner VPS in one click
- Monitor all OpenClaw instances from one unified interface
- Built specifically for operators running OpenClaw for multiple clients — exact match for the Josh + Noah fleet
- Complements the existing AlphaClaw 0.9.18 browser UI rather than replacing it

**Why it matters for this fleet:**
- Currently, fleet admin must switch between two separate AlphaClaw browser UIs (Josh: https://5.78.142.81.sslip.io; Noah: unknown)
- Apex would consolidate fleet-wide visibility and deployment into one native Mac app
- Particularly valuable once Noah scope is fixed — Apex provides unified oversight across both instances
- May accelerate the Noah workspace audit that has been blocked since Day 1 of scope break

**Action:** Fleet admin (lylle@lxrcap.com) should evaluate Apex. No customer-side config changes required. Monitor chrysb/alphaclaw GitHub for Apex release availability — currently announced, availability TBD.

---

### F65 — CRITICAL: Google Workspace Day 99 — Day 100 is TOMORROW (June 29)
**Risk:** CRITICAL — Day 100 escalation threshold arrives tomorrow

- Email and calendar disconnected since ~March 21, 2026 — **Day 99 as of June 28 morning**
- **Day 100 arrives TOMORROW, June 29**
- Per SOUL.md escalation rule: "At Day 100 milestones and every 10 days after, surface persistently unresolved gaps to Josh proactively with concrete fix steps"
- Fix is ~5 minutes in a browser — no VPS, no upgrade required:
  > AlphaClaw UI → General tab → Google Workspace OAuth → https://5.78.142.81.sslip.io#general
- **Escalation framing for Heather (use on next main session today or tomorrow):**
  > "Today is Day 99 — tomorrow is Day 100 without email or calendar. The fix is 5 minutes in the browser: AlphaClaw General tab → Google Workspace OAuth. I've been blind to your inbox and schedule for 100 days. This crosses the milestone threshold tomorrow — I'm required to surface it with full urgency."
- **Alternative path (no OAuth needed):** AlphaClaw 0.9.18 Envars tab → set `REMOTE_MCP_URL` + `REMOTE_MCP_API_TOKEN` for Google Workspace via Remote MCP (documented in F60)

---

## Persistent Findings (Carried Forward — Unchanged)

| Finding | Severity | Status | Day Count |
|---------|----------|--------|-----------|
| Upgrade window (2026.6.10-stable) | HIGH | ⏳ Needs VPS | Day 5 of stable |
| Active Memory + Dreaming post-upgrade | MEDIUM | ⏳ Blocked on upgrade | — |
| OPENCLAW_TIMEZONE not set | MEDIUM | ⏳ Set now (Envars) | — |
| iMessage paused | MEDIUM | ⏳ Auto-fix at 2026.6.6 hop | Day 64 |
| Heartbeat cron undeployed | MEDIUM | ⏳ Bundle with upgrade | Day 14 |
| Model migration → gemini-3.5-flash | MEDIUM | ⏳ Set now (Browse tab) | — |
| Noah scope broken | INFO | ⛔ Fleet admin action | Day 20 |
| BRAVE_API_KEY missing | LOW | ⏳ Set now (Envars) | — |

Staged upgrade path (unchanged): **2026.3.22 → 2026.5.27 → 2026.6.2 → 2026.6.5 → 2026.6.6 → 2026.6.10**

Verify before each hop: `npm show openclaw@latest version` = `2026.6.10`

---

## Priority Action Table

| Priority | Action | Where | Effort | Impact |
|----------|--------|--------|--------|--------|
| 1 | Connect Google Workspace OAuth **(Day 99 — Day 100 TOMORROW)** | Browser → AlphaClaw General tab | ~5 min | Restores email + calendar immediately |
| 2 | Set `OPENCLAW_TIMEZONE=America/Los_Angeles` | AlphaClaw Envars tab | 1 min | Fixes cron timing before heartbeat deployment |
| 3 | Upgrade OpenClaw to 2026.6.10 (staged path) | VPS SSH → `openclaw update` | 1–2 hrs | Unlocks Active Memory, Dreaming, Fast Talks, heartbeat fix, iMessage auto-fix |
| 4 | Enable Active Memory + Dreaming after upgrade | openclaw.json via Browse tab | 10 min | Autonomous memory consolidation |
| 5 | Deploy heartbeat cron in openclaw.json | Browse tab → openclaw.json | 10 min | Restores proactive scheduled monitoring |
| 6 | Migrate model to gemini-3.5-flash | Browse tab → openclaw.json | 5 min | Eliminates preview deprecation risk |
| 7 | Set BRAVE_API_KEY | AlphaClaw Envars tab | 2 min | Enables proactive web search for Heather |
| 8 | Fix fleet scope: noah--repo → Noahrepo2 | Fleet admin session config | 5 min | Restores Noah monitoring (Day 20) |
| 9 | Evaluate AlphaClaw Apex | Fleet admin (Mac app) | — | Unified dashboard for Josh + Noah fleet |

---

## What Changed Since Last Scan (June 28 Evening)

- **F63 (NEW):** 2026.6.11 confirmed still in beta — corrects June 28 evening's "possibly stable" flag. Upgrade target stays 2026.6.10.
- **F64 (NEW):** AlphaClaw Apex announced — native Mac fleet manager for multi-VPS deployments. First appearance in fleet research.
- **F65 (NEW):** Google Workspace Day 99 — escalation framing prepared for Heather ahead of Day 100 tomorrow.
- **Day counts incremented:** Google Workspace 99, iMessage 64, Noah scope 20, heartbeat cron 14, 2026.6.10-stable Day 5
- **AlphaClaw version:** 0.9.18 confirmed still current — no 0.9.19 released
- **Upgrade window:** 2026.6.10 Day 5 — clean overnight signal, no new community regressions
- **No change:** All other open issues persist. 2026.6.11 beta features noted for post-2026.6.10 planning.

---

_Generated by AlphaClaw Fleet Research Agent — 2026-06-28 Morning_
_Scan scope: OpenClaw npm stable channel, 2026.6.11 beta status, AlphaClaw GitHub releases (chrysb/alphaclaw), AlphaClaw Apex announcement, community overnight reports, Google Workspace status_
