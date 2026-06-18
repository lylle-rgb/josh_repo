# Fleet Research — Josh (Heather Schwartz) | 2026-06-18 Evening Scan

**Scan type:** Platform delta + workspace audit + GitHub-applicable fixes
**Date:** 2026-06-18 (evening)
**Instance:** Josh Meyers — Heather Schwartz (personal assistant)
**Repo:** lylle-rgb/josh_repo
**Prior scan:** 2026-06-17 evening — SOUL.md personalized, AGENTS.md emoji override, TOOLS.md populated, heartbeat-state.json created

---

## Summary

Tonight's scan finds Heather's workspace in its best shape since deployment. All GitHub-accessible fixes from the June 16–17 scan pair are in place. The platform (2026.3.22) and Google Workspace remain the two key blockers — both require Josh's direct action.

New tonight:
- OpenClaw 2026.6.8 confirmed stable (released June 16) — upgrade target updated in TOOLS.md
- AlphaClaw 0.9.17/0.9.18 feature review — per-agent thinking levels + remote MCP server noted
- Heartbeat state monitoring: all timestamps still null on Day 2 — potential execution gap flagged
- Claude Haiku 4.5 upgrade window now open — noted in TOOLS.md and MEMORY.md
- Two GitHub-only fixes applied tonight (TOOLS.md + MEMORY.md)

---

## ⚡ Actions Applied Tonight (GitHub-Only)

### ✅ APPLIED — TOOLS.md Upgrade Path Extended to 2026.6.8

**File:** `workspace/TOOLS.md`  
**Change:** Extended staged upgrade path to include 2026.6.8 as final target. Removed the "expected late June" hedge on claude-haiku-4-5 — 2026.6.8 is now stable as of June 16.

Before: target `2026.6.6`, path terminated there, haiku-4-5 "expected late June"
After: target `2026.6.8`, path extended with final step, haiku-4-5 marked "NOW available after reaching 2026.6.8"

### ✅ APPLIED — MEMORY.md Updated (Platform Target, Haiku 4.5 Status, AlphaClaw 0.9.18 Note)

**File:** `workspace/MEMORY.md`  
**Change:** Updated platform target from 2026.6.6 to 2026.6.8, clarified haiku-4-5 is now actionable, added AlphaClaw 0.9.18 remote MCP feature as a future integration note, updated "last updated" to June 18.

---

## Finding 1 — OpenClaw 2026.6.8 Confirmed Stable, No New Release Since (INFO)

No new OpenClaw release since 2026.6.8 (June 16, 2026). Platform is stable. Josh's upgrade path is clear:

```
2026.3.22 → 2026.5.27 → 2026.6.2 → 2026.6.5 → 2026.6.6 → 2026.6.8
```

Key 2026.6.8 features Heather gains on upgrade:
- **iMessage reply-action handling disabled** — fixes spurious reactions to iMessages (directly relevant once bridge restores)
- **iMessage split-send coalescing removed** — simpler, more reliable sends
- **iMessage NUL byte fix** — NUL bytes in sent-message echoes no longer corrupt session
- **Memory/state recovery** — preserved across compaction events
- **Safer model routing** — fewer silent fallback failures
- **Discord auto-thread titles** — auto-generated with 60s timeout, 4,096-token reasoning budget
- **Claude Haiku 4.5 support** — Haiku 4.5 catalog bug fixed; safe to upgrade model ID once on 2026.6.8
- **GLM-5.2 model support** added
- **Usage footer enhanced** — `/usage` generates full credit/limit report per reply

**No action needed tonight** — upgrade path is in TOOLS.md, upgrade requires VPS access.

---

## Finding 2 — AlphaClaw 0.9.18: Remote MCP Server + OpenAI API Proxy (INFO)

**Released:** June 1, 2026  
**Risk:** INFO / Capability expansion

AlphaClaw 0.9.18 ships two significant new capabilities:

**Managed Remote MCP Server:**
- Remote MCP servers can now be configured with environment variables directly in AlphaClaw UI
- No SSH or manual config file editing required
- Opens a path for connecting integrations (Notion, Calendly, GitHub, Zapier, etc.) without VPS access

**OpenAI-Compatible API Proxy:**
- Exposes `/v1/chat/completions`, `/v1/responses`, `/v1/embeddings`, `/v1/models`
- Third-party tools that speak OpenAI's API (LangChain, Open WebUI, Raycast, etc.) can route through Heather's AI provider
- Enhanced authentication: timing-safe token validation + rate limiting
- Toggle in AlphaClaw Setup UI

**For Heather:** The remote MCP capability is particularly useful for Josh's workflow. Once Google Workspace is connected, remote MCP could add Notion (Bliss brand docs), Calendly (scheduling), or GitHub integrations without VPS SSH — all configured through the AlphaClaw UI Envars tab.

**Action:** No immediate action needed. After platform upgrade, check AlphaClaw Watchdog tab to confirm version 0.9.18 is running, then explore remote MCP integrations.

---

## Finding 3 — AlphaClaw 0.9.17: Per-Agent Thinking Level Control (INFO)

**Released:** May 31, 2026  
**Risk:** INFO / Token optimization

AlphaClaw 0.9.17 added per-agent thinking level configuration. Agents (cron, heartbeat, main session) can now be given different thinking budgets:

- **Low thinking** — Good for routine heartbeat checks (email scan, calendar peek, iMessage status). Saves tokens on frequent low-complexity tasks.
- **High/extended thinking** — Good for complex research, multi-step planning, Bliss/Oben HiFi business tasks, calendar conflict resolution
- **Model-aware options** — Thinking level adapts to the current model's capabilities

**For Heather:** Setting heartbeat and cron sessions to low thinking would reduce token burn on routine checks. Main session stays on auto. This is a meaningful cost optimization for a personal assistant running checks 3–4× per day.

**Action:** No GitHub-only action available. Note for AGENTS.md after upgrade.

---

## Finding 4 — Discord Auto-Thread Titles (2026.6.8 Feature Preview) (INFO)

OpenClaw 2026.6.8 adds auto-generated Discord thread titles:
- Titles generated with 60-second timeout
- 4,096-token reasoning-model output budget per thread
- Threads are named meaningfully rather than "Thread" + timestamp

**For Heather:** When creating discussion threads in Josh's Discord server, they'll get descriptive titles automatically. No action needed — activates on upgrade.

---

## Finding 5 — Heartbeat State: All Null on Day 2 (Execution Gap)

**Risk: MEDIUM**

`workspace/memory/heartbeat-state.json` was created June 17 with all null timestamps. As of June 18 evening (Day 2), all timestamps remain null — no heartbeat checks have been logged.

Possible explanations:

**a) Google-blocked checks silently skip (most likely)** — Email and calendar checks silently no-op because Google Workspace isn't connected. HEARTBEAT.md says to skip silently if Google isn't connected, so Heather returns `HEARTBEAT_OK` without updating the state file. However, the **iMessage status check** reads local `memory/inbox-state.json` and does NOT require Google. This check should be running and updating the state file.

**b) heartbeat-state.json not being written to** — Heather reads HEARTBEAT.md and performs checks mentally but doesn't write the state file after each check. "Mental notes don't survive sessions" — this is the core error the state file was meant to prevent.

**c) Heartbeat poll not triggering HEARTBEAT.md** — The heartbeat message fires but Heather returns `HEARTBEAT_OK` without reading HEARTBEAT.md.

**Why it matters:** If checks aren't being logged, Josh has had no confirmed proactive monitoring since deployment (88+ days). The configuration is correct — the execution hasn't started.

**Suggested action for Josh:** In Discord, ask Heather: "Did you check iMessage status this morning?" If she says "I can't access Google Workspace" — she's conflating checks. If she says yes but no file was updated — she's not writing state. Either way, it tells you which gap to close.

---

## Finding 6 — compaction/memoryFlush and Dreaming Still Missing (Day 88)

**Risk: HIGH (persistent)**

These two `openclaw.json` settings remain unset after 88 days:

**compaction/memoryFlush** — Without this, Heather silently loses context when the session hits its token limit. Mid-conversation facts disappear. This is a fundamental reliability risk for a long-running personal assistant session.

**Dreaming** — Without nightly consolidation, MEMORY.md stays static between fleet agent scans. Heather cannot automatically grow long-term memory from her sessions.

Both require VPS access to edit `openclaw.json`. They should be added as part of the upgrade process. Recommended configs:

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
},
"dreaming": {
  "enabled": true,
  "schedule": "0 11 * * *",
  "maxPromotion": 10,
  "minScore": 0.7
}
```

Add under `agents.defaults` in `openclaw.json`. (Schedule `0 11 * * *` = 3 AM PST if VPS is UTC.)

---

## Noah Fleet Status

Session scope still lists `lylle-rgb/noah--repo` (404 — repo does not exist). Actual repos: `lylle-rgb/Noahrepo2` and `lylle-rgb/Noah-workspace` — both out of session scope. Noah analysis remains blocked for every scan in this series.

**⚠️ Action for fleet operator:** Update this session's repo scope to `lylle-rgb/Noahrepo2` (appears to be the active repo based on search results) before next scan.

---

## Platform Status (June 18 Evening)

| Item | Current | Latest Stable | Gap / Status |
|------|---------|--------------|-------------|
| OpenClaw | 2026.3.22 | **2026.6.8** | 88 days — upgrade on VPS |
| AlphaClaw | Unknown | 0.9.18 | Check Watchdog tab |
| Primary model | google/gemini-3-flash-preview | — | Active preview |
| Fallback 1 | openrouter/google/gemini-3.5-flash | — | ✅ Current |
| Fallback 2 | openrouter/anthropic/claude-3.5-haiku | claude-haiku-4-5 | ⏳ Upgrade after VPS upgrade |
| SOUL.md | ✅ Personalized June 17 | — | Good |
| AGENTS.md | ✅ Emoji override + weekly check | — | Good |
| TOOLS.md | ✅ Updated tonight | — | Good |
| MEMORY.md | ✅ Updated tonight | — | Good |
| HEARTBEAT.md | ✅ Populated June 16 | — | Good |
| heartbeat-state.json | ✅ Created June 17 | — | ⚠️ All null — checks not logged |
| Google Workspace | Not connected | — | ⛔ CRITICAL — day 88 |
| iMessage monitoring | Paused | — | ⛔ Day 52+ |
| Dreaming | Not enabled | — | ⛔ Day 88 |
| compaction/memoryFlush | Not configured | — | ⛔ Day 88 |
| Discord security | Open ("*") | — | ⚠️ MEDIUM — no allowlist |

---

## Priority Action Queue (June 18 Evening)

| Priority | Action | Method | Effort |
|---------|--------|--------|-------|
| 1 — CRITICAL | Connect Google Workspace OAuth | AlphaClaw UI: https://5.78.142.81.sslip.io#general | ~30 min |
| 2 — HIGH | Upgrade OpenClaw to 2026.6.8 | VPS: `openclaw update` (staged path in TOOLS.md) | ~30 min |
| 3 — HIGH | Add compaction + Dreaming to openclaw.json | VPS: edit openclaw.json | ~5 min |
| 4 — MEDIUM-HIGH | Tighten Discord security (open → allowlist) | VPS: openclaw.json | ~5 min |
| 5 — LOW | Enable Discord streaming "progress" | VPS: openclaw.json post-upgrade | ~1 min |
| 6 — FUTURE | Upgrade fallback 2 to claude-haiku-4-5 | openclaw.json after 2026.6.8 VPS upgrade | ~1 min |
| 7 — FUTURE | Configure per-agent thinking levels | AlphaClaw 0.9.17+ post-upgrade | ~5 min |
| 8 — FUTURE | Explore remote MCP integrations (Notion, Calendly) | AlphaClaw 0.9.18 UI | ~variable |
