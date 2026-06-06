# Fleet Research — Josh (Heather Schwartz) | 2026-06-06 Evening Scan

**Scan type:** Platform delta + persistent gap review + web research
**Date:** 2026-06-06
**Instance:** Josh Meyers — Heather Schwartz (personal assistant)
**Repo:** lylle-rgb/josh_repo
**Prior scan:** 2026-06-05 evening — all findings carried (zero resolved)

---

## Platform Status

| Item | Current | Latest Stable | Gap |
|------|---------|--------------|-----|
| OpenClaw | 2026.3.22 | **2026.6.2** | **76 days** |
| AlphaClaw | Unknown | 0.9.16 | Check deployment |
| Primary model | google/gemini-3-flash-preview | — | — |

---

## ⚠️ DAY 76 — ZERO GITHUB-ONLY FIXES APPLIED

Five zero-risk GitHub-file edits have been pending since May 22. None have been applied. These require no VPS access, no downtime, and no deployment changes. They are pure text file commits.

**The most critical:** `workspace/MEMORY.md` does not exist. Heather has no long-term memory and never will until this file is created. This has been Day 1 → Day 76 with no action.

---

## NEW Findings (June 6 Evening Delta)

### FINDING-JOSH-50 | OPENCLAW_STATE_DIR — Plugin Artifact Persistence Fixed
**Severity:** MEDIUM
**Status:** NEW — AlphaClaw platform improvement (relevant post-upgrade)

AlphaClaw updated its managed startup to export `OPENCLAW_STATE_DIR=/data/.openclaw` before OpenClaw launches. All plugins now write durable artifacts to `/data/.openclaw` instead of ephemeral temp paths (`/tmp`). Previously, any plugin writing to the default temp path would lose state on container restart.

**Why it matters for Josh:**
- Heather's iMessage inbox state (`memory/inbox-state.json`) is a manual file in the repo today, but the underlying iMessage monitor plugin was likely also writing to temp paths
- After upgrading to 2026.6.2 + AlphaClaw ≥0.9.x: the SQLite iMessage state migration (from JOSH-40/47) writes to `/data/.openclaw` — durable across container restarts
- Once Google is connected, the Gmail/Calendar plugin state will also persist correctly under `/data/.openclaw`
- No action needed today — benefit arrives automatically with AlphaClaw upgrade

**Risk level:** LOW (informational — no action required now)

---

### FINDING-JOSH-51 | No New Platform Release — 2026.6.2 Still Latest
**Severity:** INFO
**Status:** Confirmed today

OpenClaw 2026.6.2 remains the latest stable release as of June 6. No new stable or beta release was published today. The upgrade gap remains 76 days.

**Next expected stable:** 2026.5.31-stable, expected mid-to-late June 2026 (Tailscale Serve service-name bindings, Communication Notifications settings). Not yet released.

**Risk level:** LOW (no action change)

---

### FINDING-JOSH-52 | Apple Watch + iMessage — Personal Assistant Dream Setup
**Severity:** INFO
**Status:** OPPORTUNITY — reiterated from June 5 (JOSH-48)

For completeness: once Josh connects Google Workspace AND upgrades to 2026.6.2, the full personal assistant stack becomes available:
- **Apple Watch:** Time-sensitive alerts (upcoming meeting, urgent email) delivered to wrist without unlocking phone
- **iOS push relay:** Heather → iPhone push, no self-hosted relay needed
- **iMessage monitoring (re-enabled):** After `openclaw doctor --fix` post-upgrade, iMessage resumes via SQLite-backed state
- **Gmail + Calendar proactive monitoring:** HEARTBEAT.md activates once Google account is connected

This entire stack is blocked by two actions: (1) connect Google account in AlphaClaw Setup UI, (2) upgrade to 2026.6.2. Both require ~30 minutes total.

**Risk level:** LOW (opportunity tracking)

---

## Persistent Findings (All Unresolved — Day 76)

| Finding | Severity | Days Open | Note |
|---------|----------|-----------|------|
| JOSH-30: MEMORY.md never created | **CRITICAL** | **76** | Heather has zero long-term memory. One file creation. |
| JOSH-45: No Google account connected | **CRITICAL** | 2 | Blocks Gmail, Calendar, Contacts, all proactive monitoring |
| JOSH-29: Platform 76 days outdated | HIGH | 76 | 2026.3.22 → 2026.6.2 (requires VPS) |
| JOSH-31: HEARTBEAT.md empty | HIGH | 76 | Explained: no Google acct = nothing to monitor |
| JOSH-37: SOUL.md never personalized | MEDIUM | 76 | Generic template — no Josh-specific context |
| JOSH-32: TOOLS.md template not filled | MEDIUM | 76 | No actual setup documented |
| JOSH-34: Emoji contradiction | LOW | 76 | AGENTS.md encourages reactions; USER.md prohibits them |
| JOSH-33/40/47: iMessage paused | MEDIUM | 41 | Wait for SQLite migration at upgrade |
| JOSH-46: Discord streaming off | MEDIUM | 2 | `"streaming": "off"` → `"on"` (post-upgrade) |
| JOSH-49: BOOTSTRAP.md not deleted | LOW | 2 | Stale artifact burning tokens |

---

## Priority Action Queue (Unchanged from June 5 — Nothing Applied)

### GitHub-Only (Zero Downtime, Zero VPS Access):

1. **[CRITICAL] Create `workspace/MEMORY.md`** — Day 76. See soul-improvements.md for exact stub content. One file creation.
2. **[MEDIUM] Personalize `workspace/SOUL.md`** — Add Josh-specific section and remove generic template language. See soul-improvements.md.
3. **[MEDIUM] Fill in `workspace/TOOLS.md`** — Document actual setup (Discord primary, iMessage paused, no Google yet). See soul-improvements.md.
4. **[LOW] Fix emoji contradiction** — Update `workspace/AGENTS.md` React section or add override to SOUL.md. See soul-improvements.md.
5. **[LOW] Delete `workspace/BOOTSTRAP.md`** — Stale onboarding artifact.

### Requires VPS / Setup UI:

6. **[CRITICAL] Connect Google account** — AlphaClaw Setup UI (`https://5.78.142.81.sslip.io#general`) → Google Workspace → authorize Gmail + Calendar + Contacts
7. **[HIGH] Upgrade to 2026.6.2** — From 2026.3.22. Recommend staging through 2026.5.27 first.
8. **[MEDIUM] Enable Discord streaming** — `channels.discord.streaming: "on"` after upgrade

---

## Web Research Notes (2026-06-06)

- **OpenClaw 2026.6.2** is confirmed still the latest stable. No new release today.
- **AlphaClaw OPENCLAW_STATE_DIR export** is a background improvement that arrives with an AlphaClaw upgrade — no config change needed, benefit is automatic.
- **Skill Workshop PROPOSAL.md workflow** is now more detailed: skills proposed by the agent are placed in `PROPOSAL.md` as pending, visible in the Control UI board and today views, and wait for human approval before activation. Josh can review and approve new skills from his browser or phone without SSH.
- **Memory hierarchy research (April 2026):** Token-efficient hierarchical memory extraction shows +29.6 percentage points on temporal queries and +23.1 on multi-hop reasoning vs. flat memory. Heather cannot benefit from any memory improvement until `MEMORY.md` exists. Day 76.
- **Discord bot improvements (2026):** Redis-backed memory persistence for Discord bots is now common practice. OpenClaw's file-based memory system (MEMORY.md + daily notes) is equivalent to this — but only once the files exist.
