# Fleet Research — Josh (Heather) | 2026-06-03 Morning Scan

**Scan type:** Platform delta + persistent gap review  
**Date:** 2026-06-03  
**Instance:** Josh Meyers — Heather Schwartz (personal assistant)  
**Repo:** lylle-rgb/josh_repo  
**Prior scan:** 2026-06-02 morning (second scan) — see cross-customer-analysis.md  

---

## Platform Status

| Item | Current | Stable Target | Latest Beta | Gap |
|------|---------|--------------|-------------|-----|
| OpenClaw | 2026.3.22 | 2026.5.27 | **2026.6.1-beta.3** | **73 days** |
| AlphaClaw | Unknown | 0.9.16 | — | Check deployment |
| Primary model | google/gemini-3-flash-preview | — | — | — |

---

## New Findings (June 3 Delta)

### FINDING-JOSH-40 | SQLite-Backed iMessage State — Changes JOSH-33 Repair Strategy
**Severity:** HIGH  
**Status:** NEW — Platform opportunity (changes existing finding strategy)

OpenClaw 2026.5.31-beta.3 (May 31) introduced SQLite-backed state management for iMessage monitors. This has been shipping in the beta train and is expected in the next stable release.

**Why it matters for Josh:**
- Josh's `workspace/memory/inbox-state.json` has a malformed duplicate key (`"imessage_monitoring_paused": true` appears twice) and iMessage has been paused ~38 days
- SQLite-backed iMessage state eliminates the fragile JSON file entirely — no more duplicate-key bugs, no manual repair needed post-upgrade
- After upgrading to the stable that includes 2026.5.31, iMessage state can be reset cleanly via `openclaw doctor --fix` or the Control UI using the new SQLite-backed system
- **This changes the JOSH-33 repair strategy:** Do NOT manually edit `inbox-state.json` now. The SQLite migration will handle the state reset automatically at upgrade time.

**Revised action for JOSH-33:**
1. Hold off on manual `inbox-state.json` edits
2. After VPS upgrade to ≥2026.5.31-stable: run `openclaw doctor --fix` to trigger iMessage state migration to SQLite
3. Re-enable iMessage monitoring through the Control UI or agent command

**Risk level:** LOW (guidance change only — no config edit required today)

---

### FINDING-JOSH-41 | Skill Workshop — No-CLI Skill Management
**Severity:** INFO  
**Status:** OPPORTUNITY (available in 2026.6.1-beta.1+, post-upgrade)

OpenClaw 2026.6.1-beta.1 ships Skill Workshop: proposal management, revision dialogs, file previews. Skills can be browsed, proposed, and approved entirely from the Control UI without SSH access.

**Why it matters for Josh:**
- Josh has `skills.install.nodeManager: npm` — currently any skill install requires SSH into the VPS
- With Skill Workshop, Heather can propose a skill from the Control UI and Josh can approve it from his browser or mobile — no VPS access needed
- Particularly valuable for personal assistant skill additions: email templates, calendar helpers, browser automation
- Skill Workshop also handles stale disabled snapshots and loader failures more clearly, improving reliability when skills fail to load after upgrades

**Exact changes to apply:**  
None now — workflow shifts to Control UI post-upgrade to ≥2026.6.1-stable.

**Risk level:** LOW

---

### FINDING-JOSH-42 | Interrupted Tool Call Recovery
**Severity:** MEDIUM  
**Status:** OPPORTUNITY (available in 2026.6.1-beta.3+)

OpenClaw 2026.6.1-beta.3 adds enhanced recovery from interrupted tool calls and stale session bindings.

**Why it matters for Josh:**
- Heather handles multi-step tasks: read email → draft reply → send → confirm. A network hiccup or container blip mid-sequence previously left the session in a broken state requiring conversation restart
- With interrupted tool call recovery, Heather resumes the task cleanly after any interruption
- Stale session binding recovery means existing Discord sessions recover after container restart without ghost session artifacts requiring manual reconnect
- On Josh's current 2026.3.22 build, interrupted tool calls are dead ends; user must restart manually

**Exact changes to apply:**  
Available post-upgrade to ≥2026.5.27 (partial) or ideally the stable that includes 2026.6.1 full recovery.

**Risk level:** LOW

---

### FINDING-JOSH-43 | iOS Hosted Push Relay
**Severity:** INFO  
**Status:** OPPORTUNITY (available in 2026.6.1-beta.3+)

OpenClaw 2026.6.1-beta.3 adds iOS hosted push relay support and iPad layout compatibility.

**Why it matters for Josh:**
- If Josh interacts with Heather via iPhone or iPad (likely for a personal assistant use case), this enables reliable push notifications without a self-hosted relay
- Currently iOS push requires additional relay infrastructure; the hosted relay removes that requirement entirely
- Auto-activates post-upgrade when the iOS app connects — no configuration needed

**Risk level:** LOW

---

## Persistent Findings (Carried from June 2 — Unresolved)

| Finding | Severity | Days Open | Note |
|---------|----------|-----------|------|
| JOSH-30: MEMORY.md never created | **CRITICAL** | 73 | Highest-priority GitHub-only fix |
| JOSH-31: HEARTBEAT.md empty | HIGH | 73 | Zero proactive monitoring |
| JOSH-29/39: Platform 73 days outdated | HIGH | 73 | Requires VPS |
| JOSH-88: Dreaming activation | HIGH | — | Blocked on upgrade |
| JOSH-37: SOUL.md never personalized | MEDIUM | 73 | GitHub-only |
| JOSH-32: Bootstrap TOOLS.md stale | MEDIUM | 73 | GitHub-only |
| JOSH-33: iMessage monitoring paused | MEDIUM | 38 | Strategy updated — see JOSH-40 |
| JOSH-85: Gemini 3.1-flash-lite prep | MEDIUM | — | GitHub-only, zero-risk |
| JOSH-34: Emoji contradiction | LOW | 73 | GitHub-only |
| JOSH-89: Track 2026.5.28→stable | INFO | — | Monitor only |

---

## Summary Table

| Finding | Severity | Type | Status |
|---------|----------|------|--------|
| JOSH-40: SQLite iMessage state | HIGH | Platform opportunity | NEW — changes JOSH-33 strategy |
| JOSH-41: Skill Workshop | INFO | Platform opportunity | OPPORTUNITY (post-upgrade) |
| JOSH-42: Interrupted tool call recovery | MEDIUM | Platform opportunity | OPPORTUNITY (post-upgrade) |
| JOSH-43: iOS push relay | INFO | Platform opportunity | OPPORTUNITY (post-upgrade) |

---

## Platform Research Notes (2026-06-03)

- **OpenClaw latest stable:** 2026.5.27 (unchanged — still the upgrade target)
- **OpenClaw latest beta:** 2026.6.1-beta.3 (released TODAY June 3, 2026)
- **2026.6.1-beta.3 key changes:** SQLite iMessage state management, Skill Workshop, iOS push relay, interrupted tool call recovery, Tokenjuice externalized as official plugin, calmer Discord composer controls
- **2026.5.31-beta.4:** Tailscale Serve service-name bindings, Communication Notifications settings — stable promotion expected mid-to-late June 2026
- **Next stable expected:** 2026.5.31 (mid-to-late June 2026). Josh should upgrade to 2026.5.27 now; 2026.5.31-stable adds iMessage SQLite migration
- **No new GitHub-only fixes identified today** — all new findings are platform-dependent (require VPS upgrade) or informational
- **JOSH-33 strategy update:** Do not manually edit inbox-state.json; SQLite migration at upgrade time handles it cleanly
- **Top GitHub-only priority unchanged:** JOSH-30 (create MEMORY.md stub) remains the single highest-leverage action with zero risk
