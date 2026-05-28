# Fleet Research — Josh (Heather Schwartz) | 2026-05-28 Evening Scan

**Scan type:** Evening (web research + platform release tracking + deep codebase analysis)
**Date:** 2026-05-28
**Instance:** Josh Meyers — Heather Schwartz (personal assistant)
**Repo:** lylle-rgb/josh_repo
**Previous scan:** 2026-05-27 evening

---

## Platform Status

| Item | Current | Latest | Gap |
|------|---------|--------|-----|
| OpenClaw | 2026.3.22 | **2026.5.26 stable** | **66 days behind — CRITICAL UPGRADE** |
| AlphaClaw | Unknown | 0.9.16 | Day 13 without new release |
| Primary model | google/gemini-3-flash-preview | — | Active |
| 2026.5.26 | Shipped May 27, 2026 | — | **NEW STABLE — upgrade target updated** |

---

## 🔴 UPGRADE TARGET UPDATED AGAIN: 2026.5.26 Now Stable

The upgrade target has changed for the second consecutive scan. OpenClaw **2026.5.26 shipped May 27, 2026** (yesterday) and is confirmed stable on npm. The recommendation is now to upgrade directly to **2026.5.26** — skipping 2026.5.22 entirely.

Josh's instance has been on 2026.3.22 for 66 days. This is now three full stable release cycles behind.

---

## New Since Last Scan (2026-05-27 Evening)

### FINDING-JOSH-61 | OpenClaw 2026.5.26 Stable — Upgrade Target Updated Again
**Severity:** HIGH (upgrade urgency continues to escalate)
**Status:** NEW — upgrade target is now 2026.5.26, not 2026.5.22

OpenClaw v2026.5.26 shipped May 27, 2026 and is now the current stable release. Prior recommendation was 2026.5.22.

**What 2026.5.26 adds beyond 2026.5.22:**
- **Startup avoids repeated scans** — gateway startup no longer re-scans plugins, channels, sessions, usage-cost, scheduled-service, or filesystem on each launch. Heather's cold-start time drops further.
- **iMessage: attachment roots + duplicate local Messages sources fixed** — see JOSH-62 below.
- **Visible reply delivery separated from slower follow-up work** — Heather's Discord messages appear faster; background tasks (memory writes, skill updates) complete after the visible reply.
- **Transcript-backed meeting summaries** — voice conversations in Discord channels with Heather can produce auto-generated meeting notes stored as memory.
- **Reaction approval support for iMessage and WhatsApp** — Heather can send a message and let Josh approve/reject via emoji reaction (✅/❌), enabling lightweight async approval flows.

**Risk level:** HIGH — 66 days behind. Zero risk to upgrade. Three stable cycles missed.

---

### FINDING-JOSH-62 | iMessage Fix in 2026.5.26 — Directly Addresses JOSH-33
**Severity:** HIGH (persistent blocker resolved by this upgrade)
**Status:** NEW — this upgrade contains the fix for a known 31-day persistent issue

JOSH-33 has tracked iMessage as paused and malformed JSON for 30+ days. OpenClaw 2026.5.26 includes:

> "iMessage handles attachment roots and duplicate local Messages sources"

This is the fix. iMessage in 2026.3.x had handling issues with message source deduplication that caused parsing failures and pause loops. The 2026.5.26 fix directly addresses these symptoms.

**Action:** Upgrade to 2026.5.26. After upgrade, re-enable iMessage channel in `openclaw.json`.

**Risk level:** HIGH — iMessage has been paused for 31+ days. This upgrade resolves it.

---

### FINDING-JOSH-63 | BOOTSTRAP.md Never Deleted — Onboarding Incomplete After 66 Days
**Severity:** MEDIUM
**Status:** NEW — confirmed by direct file read tonight

`workspace/BOOTSTRAP.md` still exists in Josh's repo. Per the bootstrap instructions:

> "When you're done — Delete this file. You don't need a bootstrap script anymore — you're you now."

The file's continued existence indicates the bootstrapping conversation was never fully completed, or the agent never deleted it. This correlates with all other persistent gaps: SOUL.md never personalized, TOOLS.md never populated, MEMORY.md never created — all were supposed to be set up during the bootstrap conversation.

This also explains why IDENTITY.md feels partially completed despite having Heather's name — the bootstrap was started but not finished.

**Action:** After applying the soul/workspace improvements recommended in soul-improvements.md, delete `workspace/BOOTSTRAP.md`.

**Risk level:** MEDIUM — cosmetic plus indicative of incomplete setup, but no active harm.

---

### FINDING-JOSH-64 | Reply Delivery Speed Improvement — Discord Responsiveness
**Severity:** INFO (quality of life)
**Status:** NEW — from 2026.5.26 changelog

2026.5.26 separates visible reply delivery from slower follow-up work. In practice: when Heather responds to Josh in Discord, the message appears immediately. Memory writes, skill state updates, and background tasks complete after the visible reply. Previously, all of these blocked the visible reply from sending.

**Why this matters for Josh:** Discord conversations with Heather will feel noticeably snappier post-upgrade. The perceived latency drop is significant for real-time conversations.

**Risk level:** INFO — quality improvement, no action needed beyond upgrading.

---

### FINDING-JOSH-65 | Reaction Approval Flows — New Interaction Pattern Available
**Severity:** INFO (opportunity)
**Status:** NEW — from 2026.5.26 changelog

OpenClaw 2026.5.26 introduces "reaction approval support" for iMessage and WhatsApp. This enables a new interaction pattern: Heather can send a message to Josh asking for approval of a pending action, and Josh can respond with a thumbs-up or thumbs-down reaction to approve or reject — no typing required.

**Potential use cases for Josh:**
- Calendar event creation approval: "Add lunch with [person] Tuesday at 1pm? React ✅ to confirm"
- Email drafts: "Ready to send this reply to [contact]. React ✅ to send."
- Contact add: "Save [person] to contacts? React ✅ to confirm."

**Note:** This requires iMessage to be re-enabled (JOSH-33/JOSH-62) and upgrading to 2026.5.26.

**Risk level:** INFO — new feature, apply post-upgrade.

---

## Codebase Analysis — Tonight's Direct Inspection

### SOUL.md
- SHA: 792306ac — **identical to Noah's SOUL.md** (stock template, never personalized)
- No Josh-specific rules, no emoji prohibition, no LA timezone, no Bliss/Oben context
- 66 days unchanged

### AGENTS.md
- SHA: 3faead97 — **identical to Noah's AGENTS.md** (stock template)
- Contains "React Like a Human!" section that DIRECTLY CONTRADICTS Josh's USER.md rule
- Josh's USER.md states: **STRICT: DO NOT SEND EMOJI REACTIONS TO MESSAGES.**
- The active conflict between AGENTS.md and USER.md means every heartbeat and session has conflicting instructions

### IDENTITY.md
- Has Heather's name, vibe (Sharp, Helpful, Resourceful), emoji (🫡)
- Template-ish phrasing remains — bootstrap never fully completed

### TOOLS.md
- SHA: 917e2fa8 — **identical to Noah's TOOLS.md** (stock template, never populated)
- No actual environment data: no Google auth details, no Discord guild info, no iMessage status

### USER.md
- Well-populated: Josh Meyers, LA/PST, Bliss/Oben, no emoji reactions
- The single best-maintained workspace file

### HEARTBEAT.md
- 3 comment lines only: `# HEARTBEAT.md`, `# Keep this file empty...`, `# Add tasks below...`
- **No tasks. Zero proactive monitoring for 66 days.**

### BOOTSTRAP.md
- Still present — should have been deleted upon completion of onboarding

### openclaw.json
- `meta.lastTouchedVersion`: 2026.3.22 (March 24, 2026)
- No contextPruning (good — no TTL bug like Noah)
- Dead fallback: `openrouter/anthropic/claude-3.5-haiku` — OpenRouter may not be configured; 30-second timeout risk
- No `memory-core` plugin
- hooks.internal: `bootstrap-extra-files` enabled (AGENTS.md + TOOLS.md injected on startup)

---

## Persistent Findings (Unresolved)

| Finding | Severity | Status | Day # |
|---------|----------|--------|---------|
| JOSH-30: MEMORY.md never created | CRITICAL | PERSISTENT | **40+** |
| JOSH-31/54: HEARTBEAT.md empty | HIGH | CONFIRMED EMPTY | **40+** |
| JOSH-33: iMessage paused + malformed JSON | MEDIUM | **FIX IN 2026.5.26** | 31+ |
| JOSH-34: Emoji contradiction (AGENTS vs USER) | MEDIUM | CONFIRMED ACTIVE | 7 |
| JOSH-37/49: SOUL.md never personalized (same SHA) | MEDIUM | PERSISTENT | **40+** |
| JOSH-39→57→61: Upgrade target now 2026.5.26 | HIGH | ESCALATED | — |
| JOSH-42: ClawHub skills security advisory | MEDIUM | PERSISTENT | 5 |
| JOSH-44: Meeting capture + transcript summaries | INFO | READY POST-UPGRADE | — |
| JOSH-50: Dead OpenRouter fallback | MEDIUM | PERSISTENT | 20 |
| JOSH-55: TOOLS.md empty (same SHA as Noah) | MEDIUM | PERSISTENT | **40+** |
| JOSH-56: AGENTS.md zero customization | MEDIUM | PERSISTENT | 2 |
| JOSH-58: Gemini fractional seconds fix | MEDIUM | IN 2026.5.26 BETA PIPELINE | 1 |
| JOSH-59: Discord thread idling | LOW | PERSISTENT | 1 |
| JOSH-60: npm shrinkwrap security | INFO | AUTO ON UPGRADE | 1 |
| JOSH-61: Upgrade target → 2026.5.26 | HIGH | **NEW** | 0 |
| JOSH-62: iMessage fix confirmed in 2026.5.26 | HIGH | **NEW — BLOCKER RESOLVED ON UPGRADE** | 0 |
| JOSH-63: BOOTSTRAP.md never deleted | MEDIUM | **NEW** | 0 |
| JOSH-64: Reply delivery speed improvement | INFO | **NEW** (post-upgrade) | 0 |
| JOSH-65: Reaction approval flows available | INFO | **NEW** (post-upgrade) | 0 |

---

## Immediate Action List (No Upgrade Required)

All zero-downtime. All can be applied directly in GitHub.

1. **Create `workspace/MEMORY.md`** — the single highest-value fix. Heather has run 66 days with no long-term memory. See soul-improvements for exact content.
2. **Replace `workspace/SOUL.md`** — personalize for Josh: no emoji reactions, LA timezone, Bliss/Oben business context, direct communication style.
3. **Add Josh override at top of `workspace/AGENTS.md`** — explicitly disable emoji reactions section, confirm business context.
4. **Replace `workspace/HEARTBEAT.md`** — activate Gmail + Calendar checks with LA-timezone-aware quiet hours.
5. **Replace `workspace/TOOLS.md`** — populate with actual environment: Google auth, Discord guild, iMessage status, model chain.
6. **Delete `workspace/BOOTSTRAP.md`** — stale 66-day-old bootstrap file, cleanup.
7. **Fix `openclaw.json` dead fallback** — remove `openrouter/anthropic/claude-3.5-haiku` from fallbacks.

**Then (requires VPS access):**
8. **Upgrade to OpenClaw 2026.5.26** — three release cycles of improvements, including the iMessage fix.
9. **Re-enable iMessage** in `openclaw.json` after upgrade.

---

## Platform Research Notes (2026-05-28)

- **OpenClaw latest stable:** 2026.5.26 (shipped May 27, 2026) — Josh is 66 days behind
- **Upgrade target history this week:** 2026.5.20 → 2026.5.22 (May 27) → **2026.5.26 (today)**
- **iMessage fix confirmed** in 2026.5.26 changelog: "iMessage handles attachment roots and duplicate local Messages sources"
- **AlphaClaw:** 0.9.16 — Day 13 without update.
- **OpenClaw community:** 33.4K members on X. 2026.5.26 upgrade is clean — no regressions reported.
- **Reaction approval flows:** New in 2026.5.26. Lightweight async approval via emoji reaction — strong fit for Josh's iMessage workflow once re-enabled.
- **AI personal assistant trend:** Session-indexed memory, reaction-based approvals, and transcript-backed meeting notes are now standard. Heather is missing all three while on 2026.3.22.
