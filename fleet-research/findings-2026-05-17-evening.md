# Fleet Research — Josh / Heather Schwartz — Evening Scan

**Scan Date:** 2026-05-17 (Evening — Day 30)
**Agent:** AlphaClaw Apex Fleet Research Agent
**Instance:** Josh / Heather Schwartz — Discord bot personal assistant (iMessage, email, calendar, contacts)
**Previous Findings:** findings-2026-05-16-evening.md (Day 29 Evening, Findings 1–60)
**Cumulative Open Findings:** 65 (5 new this evening, 0 resolved)

---

## Platform News — New Since Yesterday's Evening Scan (May 16)

| Item | Detail |
|---|---|
| OpenClaw 2026.5.8 – 2026.5.12 released | Five new stable releases since yesterday's scan. Josh is now 19 releases behind stable (previously 14 — on 2026.3.22 vs current 2026.5.12). Beta is at 2026.5.14-beta.2. Gap widened by 5 in a single day. |
| Stale context invalidation (2026.5.8) | Context that is stale is now actively invalidated rather than silently reused when a session restarts after an idle period. Relevant to any session that restarts mid-conversation or after an idle gap. |
| Permission preflights (2026.5.9) | Agent now surfaces credential and permission requirements proactively at session initialization — before they are needed — rather than surfacing cryptic mid-task failures. Directly relevant to Heather's Google account not-connected state. First action post-connection will now surface a clean permission gate. |
| Retry-aware cron behavior (2026.5.10) | Periodic tasks that time out or fail now retry with intelligent backoff rather than silently dropping. Heather has no cron tasks and an empty HEARTBEAT.md — new reliability makes this the best time to begin designing heartbeat tasks for activation post-upgrade. |
| Redacted logs (2026.5.10) | Sensitive data (API keys, tokens) now redacted from log output. Affects debug log reading if operator inspects OpenClaw logs on the Hetzner VPS. |
| Scoped session references (2026.5.10) | Fixes a class of session state confusion bugs where one session's state contaminated another. Relevant to Heather's multi-session Discord architecture. |
| AlphaClaw 0.8.0 Chrome DevTools MCP | Confirmed available. Still requires OpenClaw 2026.5.7+ before it can be enabled. Josh is on 2026.3.22 — still blocked. |
| Browser device pairing enforcement | Explicit browser device pairing now required before proxy-scoped access. Verify AlphaClaw UI pairing at https://5.78.142.81.sslip.io is current after any session gap. |

---

## New Findings — Evening Scan (61–65)

---

### Finding 61 — OpenClaw 5 Releases Further Behind in 24 Hours: Now 19 Releases Behind Stable

**Risk:** MEDIUM (escalating)
**Days Pending:** 55+ (cumulative since version staleness first identified)
**Previous:** Finding 54 (14 releases behind as of Day 29 Evening)

**Description:**
Five new stable releases (2026.5.8 through 2026.5.12) shipped since yesterday's evening scan. Josh's instance is now 19 stable releases behind (2026.3.22 vs 2026.5.12) and 22 releases behind the beta channel. The gap is widening at approximately 3–5 releases per day.

Key improvements now missed that are directly relevant to Heather's deployment:
- **Stale context invalidation (2026.5.8):** Any session that restarts after an idle period now starts clean rather than inheriting stale context. Could explain any inconsistent behavior across Heather's sessions.
- **Permission preflights (2026.5.9):** After Google account connection (Finding 56), first-time gog-cli credential resolution will surface cleanly rather than as a cryptic mid-task failure. This makes the post-connection debug cycle significantly easier.
- **Retry-aware cron (2026.5.10):** Any heartbeat or cron tasks configured after upgrade will automatically retry on transient failures instead of silently dropping. First heartbeat implementation (Finding 52, 65) should be done on 2026.5.7+ to get this behavior.

**Action:**
1. Back up `openclaw.json` before upgrading (recovery safeguard).
2. Connect Google account first (Finding 56) — upgrade can temporarily disrupt active sessions.
3. Review changelog between 2026.3.22 and 2026.5.12 for breaking changes (Discord `channelType` rename: field was renamed from `type` to `channelType` in the channel-create schema — relevant if any skills use the legacy field).
4. Upgrade to 2026.5.12.
5. Verify AlphaClaw version compatibility post-upgrade.

**Risk Assessment:** Medium risk of brief session disruption during upgrade; high risk of continued functional gaps from running 19 versions behind. The Google account connection (Finding 56) remains the single most impactful action before any upgrade.

---

### Finding 62 — BOOTSTRAP.md Not Deleted After 30 Days (MEDIUM)

**Risk:** MEDIUM
**Days Pending:** 30 (first time explicitly filed as a separate finding)

**Description:**
`workspace/BOOTSTRAP.md` contains the explicit instruction: *"When You're Done: Delete this file. You don't need a bootstrap script anymore — you're you now."* The file still exists 30 days into operation.

Its continued presence indicates the bootstrap process — the initial identity conversation — was never formally concluded. At session startup, the agent sees both `IDENTITY.md` (filled in, named Heather) and `BOOTSTRAP.md` (first-run script) simultaneously. This creates a minor but real ambiguity: is this a continuation session or a first run?

Additionally, BOOTSTRAP.md contains onboarding flows for WhatsApp and Telegram that are not part of Heather's deployment and consume token budget at startup without providing value.

**Action:**
Delete `workspace/BOOTSTRAP.md`. The bootstrap is complete — Heather has a name, an identity, and an established relationship with Josh. This file is vestigial.

**Risk Assessment:** Zero risk. Deleting this file reduces startup token consumption and removes a source of first-run ambiguity.

---

### Finding 63 — No Daily Memory Logs After 30 Sessions: Memory System Non-Functional (HIGH)

**Risk:** HIGH
**Days Pending:** 30

**Description:**
`AGENTS.md` mandates at session startup: *"Read `memory/YYYY-MM-DD.md` (today + yesterday) for recent context."* The `workspace/memory/` directory contains only:
- `inbox-state.json` (malformed JSON — Finding 57)
- `onboarding-google.md` (one-time setup document)

Zero daily session logs have ever been written in 30 sessions. The consequences are cascading:

1. **SOUL.md has not evolved.** After 30 sessions, Heather's soul document is the day-one generic template. Souls evolve through documented experience — experience has not been documented.
2. **USER.md is sparse.** Josh's corrections, feedback, communication preferences, and context updates from 30 conversations have not been written down. Tomorrow-Heather will meet Josh as a stranger.
3. **MEMORY.md does not exist.** The long-term memory layer cannot be curated from daily logs that don't exist.
4. **AGENTS.md's session startup reads files that aren't there.** The startup sequence is silently failing on step 3 every single session.

The AGENTS.md note is explicit: *"'Mental notes' don't survive session restarts. Files do."* Thirty sessions of mental notes have evaporated.

**Action:**
1. Create `workspace/memory/2026-05-17.md` today with a brief session log (template in soul-improvements-2026-05-17-evening.md).
2. Establish session-end discipline: every session writes at least 3–5 bullet points to today's daily log before closing.
3. After one week of daily logs, review and create `workspace/memory/MEMORY.md` with distilled context.

**Risk Assessment:** Zero risk. Adding daily log files is purely additive. Current absence is causing silent, compounding degradation of agent capability.

---

### Finding 64 — Permission Preflight System Available Post-Upgrade: TOOLS.md Must Be Populated First (LOW/Opportunity)

**Risk:** LOW (opportunity, sequenced on upgrade)
**Days Pending:** 0 (new in OpenClaw 2026.5.9)

**Description:**
OpenClaw 2026.5.9 introduces permission preflights — the agent proactively declares required credentials at session initialization before attempting any action. The preflight system works most effectively when `TOOLS.md` clearly documents what tools the agent has access to and what credentials they require.

Heather's `TOOLS.md` is the unmodified OpenClaw template (camera names, SSH hosts, TTS voices). It does not describe her actual tools:
- `gog-cli`: Google Workspace (Gmail, Calendar, Contacts)
- Discord integration
- iMessage monitoring
- AlphaClaw UI

When Heather upgrades to 2026.5.9+, the permission preflight will either skip (if TOOLS.md looks like a template) or surface requirements for tools described in a non-generic TOOLS.md. Populating TOOLS.md before the upgrade makes the preflight system immediately useful.

**Action:**
Update `workspace/TOOLS.md` with Heather's actual tool inventory before upgrading. Ready-to-paste content in soul-improvements-2026-05-17-evening.md.

**Risk Assessment:** Zero risk. Updating TOOLS.md has no runtime effect before the upgrade and improves preflight behavior after.

---

### Finding 65 — Retry-Aware Cron Available Post-Upgrade: First Heartbeat Configuration Window (LOW/Opportunity)

**Risk:** LOW (opportunity, sequenced on upgrade)
**Days Pending:** 0 (new in OpenClaw 2026.5.10)

**Description:**
OpenClaw 2026.5.10 ships retry-aware cron: scheduled and periodic tasks now retry with exponential backoff on timeout rather than failing silently and dropping the task.

Heather's `HEARTBEAT.md` is currently empty — no heartbeat tasks have ever been configured (Finding 52). AGENTS.md describes the full heartbeat architecture: email checks, calendar checks, weather, social mentions — none are active.

Prior to 2026.5.10, a heartbeat task that timed out on a Google API call (network hiccup, credential delay) would silently fail. The same timeout post-2026.5.10 retries automatically. This makes heartbeat configuration more reliable and reduces the operational risk of missed checks. This is the optimal window to design and configure Heather's heartbeat tasks — ready to activate immediately after the upgrade.

**Action:**
1. Design HEARTBEAT.md tasks now (ready-to-paste content in soul-improvements-2026-05-17-evening.md).
2. Hold activation until OpenClaw is upgraded to 2026.5.12.
3. After upgrade and Google account connection (Finding 56), activate heartbeat and verify one complete cycle.

**Risk Assessment:** Zero risk to design now. Activation is safely sequenced: upgrade first, Google account first, then activate.

---

## Day 30 Implementation Order

### Tonight (Under 10 Minutes Each, Zero Dependencies)

1. **Fix retired fallback model** (Finding 59): `openclaw.json` → replace `openrouter/anthropic/claude-3.5-haiku` with `openrouter/anthropic/claude-haiku-4-5`. 3 minutes.
2. **Fix inbox-state.json** (Finding 57): Remove duplicate `last_email_check_ms` key. Set `imessage_monitoring_paused: false`. Validate JSON. 5 minutes.
3. **Add no-emoji rule to SOUL.md** (Finding 60/55): One sentence in Boundaries. Josh explicitly requested this 3 days ago. 2 minutes.
4. **Delete BOOTSTRAP.md** (Finding 62): Zero-risk cleanup. 30 seconds.

### This Weekend

5. **Connect Google Account** (Finding 56 — CRITICAL): Browser visit to https://5.78.142.81.sslip.io#general. Unlocks Gmail, Calendar, Contacts — the entire stated purpose of this deployment. Day 30 without this is Day 30 of zero primary functionality.
6. **Populate TOOLS.md** (Finding 64): Document actual tool inventory before upgrade.
7. **Design HEARTBEAT.md** (Finding 65): Ready-to-paste content in soul-improvements file.
8. **Update SOUL.md fully** (Finding 60): Timezone, daily rhythm, escalation protocol — full text in soul-improvements-2026-05-16-evening.md.
9. **Start daily memory logs** (Finding 63): Create `workspace/memory/2026-05-17.md` today.

### Next Week

10. **Plan and execute OpenClaw upgrade to 2026.5.12** (Finding 61): Backup → upgrade → verify. Enables permission preflights, retry-aware cron, stale context invalidation, and AlphaClaw 0.8.0 Chrome DevTools MCP.
11. **Create MEMORY.md** (Finding 50): After one week of daily logs to distill from.
12. **Customize AGENTS.md for Josh** (Finding 51): Remove generic template; add Josh-specific startup sequence, tool inventory, and group chat rules.
13. **Design and activate heartbeat** (Findings 52, 65): After upgrade and Google account connected.

---

## Persistent Findings Status Table — Day 30 Evening

| # | Title | Risk | Days Open |
|---|---|---|---|
| 48/56 | Google account never connected | CRITICAL | 30 |
| 49/57 | inbox-state.json invalid JSON + iMessage paused | HIGH | 3 |
| 50 | No MEMORY.md | MEDIUM | 30 |
| 51 | AGENTS.md generic template | MEDIUM | 30 |
| 52 | No active heartbeat | MEDIUM | Unknown |
| 53/59 | Retired fallback model claude-3.5-haiku | MEDIUM | 3 |
| 54/61 | 19 releases behind stable (was 14) | MEDIUM | 55+ |
| 55/60 | SOUL.md generic — no-emoji rule absent | MEDIUM | 3 |
| 62 | BOOTSTRAP.md not deleted after 30 days | MEDIUM | 30 |
| 63 | No daily memory logs in 30 sessions | HIGH | 30 |
| 64 | TOOLS.md unpopulated — permission preflights blocked | LOW | 0 |
| 65 | Retry-aware cron available — heartbeat design pending | LOW | 0 |

**Open: 65 | Resolved: 0 | Critical: 1 | High: 9+ | Medium: 25+ | Low: 5+**

---

*Generated by AlphaClaw Apex Fleet Research Agent — Evening Scan — 2026-05-17 (Day 30)*
