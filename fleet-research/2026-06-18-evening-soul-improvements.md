# Soul Improvements — Josh (Heather Schwartz) | 2026-06-18 Evening

**Instance:** Heather Schwartz (Josh — personal assistant)  
**Scan date:** 2026-06-18 (evening)  
**Based on findings:** `2026-06-18-evening-findings.md`

---

## GitHub-Applicable Fixes Applied Tonight

### ✅ APPLIED — TOOLS.md: Upgrade Path Extended to 2026.6.8

The staged upgrade path previously terminated at 2026.6.6 and hedged on 2026.6.8. OpenClaw 2026.6.8 is now stable (June 16, 2026). Updated:

- Platform target: `2026.6.6` → `2026.6.8`
- Staged path: added final step `→ 2026.6.8`
- Haiku 4.5 note: "expected late June" → "NOW available after reaching 2026.6.8"
- Added Discord auto-thread titles note (4,096-token budget, activates on upgrade)
- Updated iMessage paused duration to 52+ days as of June 18

**Why this matters:** Heather's self-check step (AGENTS.md Session Startup step 5) tells her to audit `openclaw.json` for deprecated models. When she does this weekly check, she needs accurate information about the upgrade target and what's actionable. An outdated TOOLS.md would have her think haiku-4-5 isn't available yet.

### ✅ APPLIED — MEMORY.md: Status Updated to June 18

Updated Known Configuration Issues and Status sections:
- Platform target: `2026.6.6` → `2026.6.8`
- inbox-state.json SQLite migration note: target version updated to 2026.6.8
- Haiku 4.5 note: flagged as "NOW available" after VPS upgrade
- Added AlphaClaw 0.9.18 remote MCP as a future integration path note
- Updated "last updated" and Status section to June 18

---

## Open Recommendations (Requires VPS or Josh Action)

These are deferred — VPS access required.

### Rec 1 — Enable Dreaming in openclaw.json [HIGH]

**What:** Add `dreaming` block to `agents.defaults` in `openclaw.json`.  
**Why:** MEMORY.md is seeded and ready. Dreaming would nightly consolidate session notes into long-term memory automatically at 3 AM. Without it, MEMORY.md only updates when the fleet agent runs — Heather can't grow her own memory from sessions.  
**Config:**
```json
"dreaming": {
  "enabled": true,
  "schedule": "0 11 * * *",
  "maxPromotion": 10,
  "minScore": 0.7
}
```
(Schedule `0 11 * * *` = 3 AM PST if VPS is UTC.)

### Rec 2 — Add compaction/memoryFlush [HIGH]

**What:** Add `compaction` and `contextPruning` blocks to `agents.defaults`.  
**Why:** Without this, Heather silently loses context when the session hits its token limit. This is a reliability risk for a personal assistant who may be mid-task when the limit hits — critical state disappears with no warning.  
**Config:**
```json
"compaction": {
  "reserveTokensFloor": 40000,
  "memoryFlush": {
    "enabled": true,
    "softThresholdTokens": 4000
  }
},
"contextPruning": {
  "mode": "cache-ttl",
  "ttl": "6h"
}
```

### Rec 3 — Tighten Discord Security [MEDIUM-HIGH]

**What:** Change `channels.discord.groupPolicy` from `"open"` to `"allowlist"`, set `allowFrom` to Josh's Discord user ID.  
**Why:** Anyone who can join Josh's Discord server can message Heather and receive responses that draw on his personal context (calendar, Bliss business details, contacts, meeting times). For a personal assistant, this is a meaningful exposure. Noah's instance uses an allowlist — Heather should too.  
**How:** Get Josh's Discord user ID from guild `1484448262290276464`, then update `openclaw.json`:
```json
"dmPolicy": "allowlist",
"groupPolicy": "allowlist",
"allowFrom": ["JOSH_DISCORD_USER_ID"]
```

### Rec 4 — Configure Per-Agent Thinking Levels [LOW]

**What:** After AlphaClaw 0.9.17+ is confirmed, set heartbeat/cron agents to "low" thinking.  
**Why:** Email scans, iMessage status checks, and calendar peeks are simple read-and-report tasks. Using extended thinking on them wastes tokens. Setting routine checks to low thinking could reduce token burn by 40–60% on heartbeat turns, freeing budget for complex main-session tasks.  
**When:** After platform upgrade to 2026.6.8 + AlphaClaw 0.9.17+ confirmed.

### Rec 5 — Explore Remote MCP Integrations via AlphaClaw 0.9.18 [LOW / Future]

**What:** AlphaClaw 0.9.18 adds managed remote MCP servers configured through the UI Envars tab — no SSH required.  
**Why:** Josh runs Bliss (luxury lifestyle brand) and Oben HiFi. Potential high-value integrations:
- **Notion MCP:** Bliss brand docs, product roadmap, meeting notes
- **Calendly/Cal.com MCP:** Scheduling without needing Google Calendar (could partially unblock while OAuth is pending)
- **GitHub MCP:** Code and project tracking for any dev work

These can be connected via AlphaClaw UI without VPS SSH once 0.9.18 is running. Lower barrier than any previous integration path.  
**When:** After Google Workspace is connected (priority 1) and Heather is running heartbeat checks reliably.

---

## Heather's Current Health Score (June 18)

| Dimension | Status | Notes |
|-----------|--------|-------|
| Identity (SOUL.md) | ✅ Personalized | Josh context, hard rules, error recovery |
| Behavioral rules (AGENTS.md) | ✅ Done | Emoji override, weekly self-check |
| Environment knowledge (TOOLS.md) | ✅ Updated tonight | Correct upgrade target, haiku-4-5 status |
| Long-term memory (MEMORY.md) | ✅ Updated tonight | Accurate platform status |
| Proactive monitoring (HEARTBEAT.md) | ✅ Configured | Schedule, state tracking, quiet hours |
| Heartbeat execution | ⚠️ Not confirmed | State file all null on Day 2 |
| Google Workspace | ⛔ Not connected | Day 88 — email/calendar blocked |
| Platform version | ⛔ 88 days outdated | All iMessage + gateway fixes locked out |
| Memory consolidation (Dreaming) | ⛔ Not enabled | Requires VPS + openclaw.json edit |
| Context safety (compaction) | ⛔ Not configured | Requires VPS + openclaw.json edit |
| Discord security | ⚠️ Open to all | No allowlist — exposure risk |

**Bottom line:** The configuration layer (files, rules, identity, environment docs) is now comprehensive and accurate. The execution layer (platform, integrations, memory automation) remains blocked on Josh's VPS + OAuth actions. Heather is configured and ready — she just can't see Josh's inbox, calendar, or iMessages until those are resolved.

---

## Summary

Two GitHub-only fixes applied tonight (TOOLS.md upgrade path, MEMORY.md status update). The workspace is as ready as it can be without VPS access. The two persistent blockers (Google OAuth + platform upgrade) have been open 88 days. Nothing else can be done from GitHub until those unlock the next layer of configuration.
