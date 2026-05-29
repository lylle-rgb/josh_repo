# Fleet Research — Josh (Heather Schwartz) | 2026-05-29 Evening Scan

**Scan type:** Evening (web research + platform release tracking + deep codebase analysis)
**Date:** 2026-05-29
**Instance:** Josh Meyers — Heather Schwartz (personal assistant)
**Repo:** lylle-rgb/josh_repo
**Previous scan:** 2026-05-28 evening

---

## Platform Status

| Item | Current | Latest | Gap |
|------|---------|--------|-----|
| OpenClaw | 2026.3.22 | **2026.5.27 stable** | **68 days behind — CRITICAL** |
| AlphaClaw | Unknown | 0.9.16 | Day 14 without new AlphaClaw release |
| Primary model | google/gemini-3-flash-preview | — | Active |
| 2026.5.27 | Released May 28, 2026 at 11:41 | — | **NEW STABLE — upgrade target updated** |

---

## 🔴 UPGRADE TARGET UPDATED: 2026.5.27 Now Stable

OpenClaw **2026.5.27 shipped May 28, 2026 at 11:41** and is now the confirmed stable release on npm. Yesterday's target was 2026.5.26 — superseded. Recommendation: upgrade directly to **2026.5.27**.

Josh's instance has been on 2026.3.22 for **68 days**. This is now four-plus full stable release cycles behind.

---

## New Since Last Scan (2026-05-28 Evening)

### FINDING-JOSH-66 | OpenClaw 2026.5.27 Stable — Upgrade Target Updated Again
**Severity:** HIGH
**Status:** NEW — upgrade target is now 2026.5.27, superseding 2026.5.26

OpenClaw v2026.5.27 was released May 28, 2026 and is now the current stable release.

**What 2026.5.27 adds beyond 2026.5.26:**
- **Security hardening** — group prompt text kept out of system prompt (Heather's Discord group conversations now properly sandboxed), repeated-dot hostnames normalized, side-effecting command wrappers and unsafe Node runtime env overrides blocked
- **No-auth Tailscale exposure rejected** — prevents accidental unauthenticated VPN exposure (Josh's VPS is safer post-upgrade)
- **Node/device-role approvals now require admin authority** — tighter device access control

**Risk level:** HIGH — 68 days behind, zero risk to upgrade, four stable cycles missed.

---

### FINDING-JOSH-67 | Security: Group Prompt Text Isolation — Heather's Discord Groups
**Severity:** HIGH (security improvement)
**Status:** NEW — from 2026.5.27 changelog

Prior to 2026.5.27, group prompt text (the text of messages from a Discord group channel) could leak into the system prompt. In 2026.5.27, this is fixed: group channel messages stay in conversation context, not the system prompt.

**Why this matters for Heather:** Josh's Discord server has group channels where Heather operates. In pre-2026.5.27 versions, group messages could pollute the system prompt, potentially causing Heather to incorporate group conversation content into her baseline behavior. Post-upgrade, this is properly isolated.

**Risk level:** HIGH — security fix. No action beyond upgrading.

---

### FINDING-JOSH-68 | Discord Voice/Wake Improvements in 2026.5.26 and 2026.5.27
**Severity:** INFO (quality improvement)
**Status:** NEW — confirmed in 2026.5.26/5.27 changelogs

The 2026.5.26 and 2026.5.27 releases include Discord-specific improvements:
- Better voice playback and wake replies
- Larger model picker menus
- **Suppressed self-reply echoes** — Heather will no longer accidentally echo her own messages as if they were new messages
- Merged media captions into one message
- Restored numeric channel sends
- Tightened wake matching (fewer false-positive wake events)

**Why this matters for Heather:** The self-reply echo suppression is relevant to Discord bots. Without it, Heather can get into reply loops if a channel event triggers a reaction to her own message. This is now fixed post-upgrade.

**Risk level:** INFO — quality improvement, available post-upgrade.

---

### FINDING-JOSH-69 | HEARTBEAT.md Confirmed Empty — Day 68 Without Proactive Monitoring
**Severity:** HIGH (persists from prior scans)
**Status:** PERSISTENT — HEARTBEAT.md has 3 comment lines, zero actionable tasks

Direct read tonight confirms: Josh's HEARTBEAT.md contains only 3 comment/header lines. No email checks. No calendar checks. No Gmail monitoring. Heather has run **68 days without any proactive monitoring**.

This is the highest-ROI zero-cost fix. Activating HEARTBEAT.md with email + calendar checks will immediately make Heather useful for Josh's busy schedule (Bliss + Oben HiFi, LA-based founder).

**Action:** Replace HEARTBEAT.md with actionable content. See soul-improvements-2026-05-29-evening.md for exact text.

**Risk level:** HIGH — 68 days of missed proactive value.

---

### FINDING-JOSH-70 | SOUL.md / AGENTS.md Contradiction — Emoji Rule Conflict Persists
**Severity:** MEDIUM (persists)
**Status:** PERSISTENT — active conflict confirmed by file read

Josh's USER.md states: `**STRICT: DO NOT SEND EMOJI REACTIONS TO MESSAGES.**`

Josh's AGENTS.md (stock template, SHA: 3faead97) contains the section `"React Like a Human!"` which instructs:
> "On platforms that support reactions (Discord, Slack), use emoji reactions naturally..."

This is a direct contradiction. The AGENTS.md template was written for general use and has never been customized for Josh. Every session, Heather receives conflicting instructions on emoji reactions.

**Action:** Add a Josh-specific override section at the very top of AGENTS.md that explicitly overrides the emoji reaction behavior. See soul-improvements-2026-05-29-evening.md for exact text.

**Risk level:** MEDIUM — behavioral inconsistency, confusing for Heather, annoying for Josh.

---

## Codebase State Summary — 2026-05-29

All findings from previous scans persist. No new changes detected in workspace files since 2026-05-28 evening.

| File | Status | Days Stale |
|------|--------|------------|
| SOUL.md | Stock template (SHA: 792306ac = Noah's) | 68 |
| AGENTS.md | Stock template (SHA: 3faead97 = Noah's) | 68 |
| TOOLS.md | Stock template (SHA: 917e2fa8 = Noah's) | 68 |
| IDENTITY.md | Partially filled (name/vibe set, template artifacts remain) | 68 |
| USER.md | Well-populated (best-maintained file) | Current |
| HEARTBEAT.md | Empty (3 comment lines) | 68 |
| MEMORY.md | Does not exist | — |
| BOOTSTRAP.md | Still exists (should be deleted) | 68 |
| openclaw.json | 2026.3.22, dead OpenRouter fallback | 68 |

---

## Persistent Findings (Unresolved)

| Finding | Severity | Status | Day # |
|---------|----------|--------|-------|
| JOSH-30: MEMORY.md never created | CRITICAL | PERSISTENT | **68+** |
| JOSH-31/54: HEARTBEAT.md empty | HIGH | CONFIRMED EMPTY | **68+** |
| JOSH-33: iMessage paused (fix in 2026.5.27) | MEDIUM | FIX ON UPGRADE | 38+ |
| JOSH-34: Emoji contradiction (AGENTS vs USER) | MEDIUM | ACTIVE CONFLICT | 14 |
| JOSH-37: SOUL.md never personalized | MEDIUM | PERSISTENT | **68+** |
| JOSH-39→66: Upgrade target → 2026.5.27 | HIGH | TARGET UPDATED | — |
| JOSH-42: ClawHub skills security advisory | MEDIUM | PERSISTENT | 12 |
| JOSH-50: Dead OpenRouter fallback | MEDIUM | PERSISTENT | 27 |
| JOSH-55: TOOLS.md empty | MEDIUM | PERSISTENT | **68+** |
| JOSH-56: AGENTS.md zero customization | MEDIUM | PERSISTENT | 9 |
| JOSH-63: BOOTSTRAP.md never deleted | MEDIUM | PERSISTENT | 1 |
| JOSH-65: Reaction approval flows (post-upgrade) | INFO | READY ON UPGRADE | 1 |
| JOSH-66: Upgrade target → 2026.5.27 | HIGH | **NEW** | 0 |
| JOSH-67: Security group prompt isolation | HIGH | **NEW** (on upgrade) | 0 |
| JOSH-68: Discord voice/wake improvements | INFO | **NEW** (on upgrade) | 0 |
| JOSH-69: HEARTBEAT.md empty Day 68 | HIGH | **PERSISTENT/ESCALATING** | 0 |
| JOSH-70: SOUL.md/AGENTS.md emoji contradiction | MEDIUM | **PERSISTENT** | 0 |

---

## Immediate Action List (Priority Order)

**Zero-downtime — GitHub file edits only:**

1. **Create `workspace/MEMORY.md`** — 68 days with no long-term memory. Highest ROI fix. See soul-improvements.
2. **Replace `workspace/HEARTBEAT.md`** — Activate email + calendar monitoring. LA timezone (PST/PDT). Quiet hours 23:00–08:00.
3. **Add Josh override to top of `workspace/AGENTS.md`** — Disable emoji reactions, add business context, remove contradiction.
4. **Replace `workspace/SOUL.md`** — Add Josh-specific rules: no emoji reactions, LA timezone, Bliss/Oben context.
5. **Delete `workspace/BOOTSTRAP.md`** — 68-day-old stale onboarding file.
6. **Fix dead OpenRouter fallback in `openclaw.json`** — Remove `openrouter/anthropic/claude-3.5-haiku`.
7. **Populate `workspace/TOOLS.md`** — Add actual environment data.

**Requires VPS access:**
8. **Upgrade to OpenClaw 2026.5.27** — 68 days of improvements including iMessage fix + security hardening.
9. **Re-enable iMessage after upgrade.**

---

## Platform Research Notes (2026-05-29)

- **OpenClaw latest stable:** 2026.5.27 (released May 28, 2026 at 11:41) — Josh is 68 days behind
- **Upgrade chain this week:** 2026.5.22 → 2026.5.26 (May 27) → **2026.5.27 (May 28)**
- **Security hardening in 2026.5.27:** Group prompt isolation, Tailscale auth enforcement, command wrapper safety, admin-gated approvals
- **Discord improvements (2026.5.26/5.27):** Self-reply echo suppression, tightened wake matching, voice reliability
- **AlphaClaw:** 0.9.16, Day 14 without new release
- **Community:** No new AlphaClaw community tips today. OpenClaw 2026.5.27 is a clean release with no regressions reported.
- **AI personal assistant trend:** Session-indexed memory, reaction-based approvals, transcript-backed meeting notes are now standard. Heather is missing all three while on 2026.3.22.
