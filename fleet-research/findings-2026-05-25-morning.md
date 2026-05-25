# Fleet Research — Josh (Heather Schwartz) | 2026-05-25 Morning Scan

**Scan type:** Morning (web research + OpenClaw release tracking + config analysis)
**Date:** 2026-05-25
**Instance:** Josh Meyers — Heather Schwartz (personal assistant: iMessage, email, calendar, contacts)
**Repo:** lylle-rgb/josh_repo
**Previous scan:** 2026-05-24 morning (evening scan for today also ran; this morning scan adds new web-sourced findings)
**Current OpenClaw version:** 2026.3.22

---

## 🚨 Upgrade Target Correction

The evening scan (2026-05-25) listed 2026.5.20 as the stable upgrade target. **That has changed.**

OpenClaw **2026.5.22 is now confirmed STABLE** — released May 24, 2026. Josh should skip 2026.5.20 and upgrade directly to 2026.5.22.

| Item | Previous Target | **Corrected Target** | Josh Current | Gap |
|------|----------------|----------------------|--------------|-----|
| OpenClaw | 2026.5.20 | **2026.5.22** | 2026.3.22 | ~25 stable releases |

---

## New This Morning

### FINDING-JOSH-51 | OpenClaw 2026.5.22 Is NOW STABLE — Upgrade Target Corrects
**Severity:** HIGH (escalation of prior upgrade recommendation)
**Status:** NEW — target upgraded from 2026.5.20 to 2026.5.22

GitHub releases page confirmed: `v2026.5.22` shipped **May 24, 2026** as stable. The evening scan tracked it as `2026.5.22-beta.1 — Day 2 in train` — it graduated to stable overnight.

**What 2026.5.22 stable adds (on top of 2026.5.20):**
- **Model listing 4100× speedup** — `/models` call drops from ~20s to ~5ms via pre-warmed provider auth state. Heather's session startup will noticeably faster.
- **Sub-agent bootstrap context limited** — sub-agents now receive only AGENTS.md and TOOLS.md by default, reducing token waste per sub-agent spawn. Heather's bootstrap hooks are already scoped to these two files, so this aligns with current config.
- **Package shrinkwrap security** — dependency integrity locked at install time. Relevant to Josh's upcoming skill installations.
- **WebChat tool-source deduplication** — fix for doubled tool replies in WebChat; cleaner UX in the AlphaClaw control UI.
- **cron delivery routed through modern resolver APIs** — cron reliability hardened (applicable post-upgrade when Josh enables cron jobs).
- **Configurable `agentComponents.ttlMs`** (24h cap) — longer-lived Discord interaction components.

**Upgrade path for Josh:**
1. Back up: `cp openclaw.json openclaw.json.bak-pre-5.22`
2. Upgrade AlphaClaw container image (pulls latest OpenClaw)
3. Verify all hooks, plugins, and channel bindings in AlphaClaw dashboard
4. Post-upgrade: enable `memory-core` + `active-memory` plugins

**Do NOT upgrade to 2026.5.24-beta.2** — still in active beta train.

**Risk level:** HIGH (upgrade brings major capability unlocks but is still a version jump from 2026.3.22 → 2026.5.22)

---

### FINDING-JOSH-52 | iMessage Thumb-Approval Reactions — Beta Feature, Major Workflow Change
**Severity:** HIGH (opportunity — approaching in 2026.5.24 stable)
**Status:** NEW — confirmed in 2026.5.24-beta.2 (released May 24)

OpenClaw 2026.5.24-beta.2 introduces **iMessage thumb-approval reactions**:
- 👍 reaction on an iMessage = **allow-once** for the pending agent action
- 👎 reaction on an iMessage = **deny** the pending agent action

**Why this is transformative for Josh/Heather:**

Josh's primary interface for Heather is iMessage. Currently, when Heather wants to confirm an action (send an email, create a calendar event, respond to a contact), she must:
1. Ask Josh in Discord or iMessage
2. Josh types out his response
3. Heather proceeds

With thumb-approval reactions, Josh can:
1. Heather proposes action inline in iMessage thread
2. Josh reacts 👍 → action executes immediately
3. Josh reacts 👎 → action is cancelled

No typing required. Mobile-native. This dramatically reduces approval friction for Josh's on-the-go lifestyle.

**Timeline:** 2026.5.24-beta.2 is in train now. Stable ETA: ~7-10 days (approximately June 1-4, 2026). Do NOT upgrade to beta.

**Pre-requisites:**
1. Upgrade to 2026.5.22 stable (required first)
2. Then upgrade to 2026.5.24 stable when it ships
3. iMessage bridge must be reconnected (currently `imessage_monitoring_paused: true`)

**Blocker:** iMessage bridge is currently paused (workspace/memory/inbox-state.json). The bridge must be re-enabled for reaction approvals to work. Re-enabling iMessage is therefore a pre-requisite for this feature — making that resumption higher priority.

**Risk level:** HIGH value, LOW risk — no config needed now. Track 2026.5.24 stable.

---

### FINDING-JOSH-53 | Active Memory Plugin — Version Pre-Requisite Gap Confirmed
**Severity:** HIGH (blocked until upgrade — then highest priority first plugin to enable)
**Status:** NEW — web research confirms minimum version requirement

Active Memory plugin was introduced in **OpenClaw 2026.4.10**. Josh is on **2026.3.22** — below the minimum. Active Memory is **not available until Josh upgrades**.

**What Active Memory does (confirmed via docs):**
- Inserts a dedicated blocking memory sub-agent BEFORE Heather's main reply
- Sub-agent calls `memory_search` / `memory_get` on the MEMORY.md and memory/ directory
- Pulls relevant historical preferences, context, and prior decisions into the active context
- Result: Heather automatically "remembers" relevant things without Josh having to repeat himself

**Why it matters for Josh:**
- Josh frequently has to re-explain context (LA timezone, Bliss luxury brand, Oben HiFi audio, no-emoji rule)
- Active Memory queries these from MEMORY.md before each reply
- The net effect is Heather gradually feeling like she knows Josh, even across fresh sessions

**Configuration once upgraded to 2026.5.22:**
```json
"plugins": {
  "allow": ["discord", "usage-tracker", "memory-core", "active-memory"],
  "entries": {
    "discord": {"enabled": true},
    "usage-tracker": {"enabled": true},
    "memory-core": {
      "enabled": true,
      "config": {"deduplication": true, "temporalDecay": true}
    },
    "active-memory": {
      "enabled": true,
      "config": {
        "agents": ["main"],
        "chatTypes": ["dm"],
        "allowedChatIds": ["JOSH_DISCORD_DM_CHANNEL_ID"],
        "inheritSessionModel": true,
        "timeout": 12000,
        "setupGraceTimeoutMs": 5000,
        "maxSummaryChars": 220
      }
    }
  }
}
```

**Note on `allowedChatIds`:** Josh's Discord guild uses `groupPolicy: open` — without restricting Active Memory to Josh's DM channel only, any Discord user in the open guild could trigger memory recall. Scope it to Josh's DM channel ID from day one.

**Dependency chain:**
1. Upgrade to 2026.5.22 ✓
2. Create `workspace/MEMORY.md` (this is what Active Memory reads) ✓
3. Enable memory-core + active-memory in plugins.entries ✓
4. Restrict `allowedChatIds` to Josh's DM channel ✓

**Risk level:** HIGH (critical dependency chain; enabling without MEMORY.md means Active Memory queries an empty store and adds overhead with no benefit)

---

### FINDING-JOSH-54 | Discord Voice Status Queries — Coming in 2026.5.24-beta
**Severity:** LOW (opportunity — tracking)
**Status:** NEW — confirmed in 2026.5.24-beta.2 notes

2026.5.24-beta.2 adds: **Discord voice callers can ask for active run status, cancel, or queue follow-up work** during an active Heather session.

Currently, if Josh is in a Discord voice channel and Heather is mid-task, there's no in-voice feedback mechanism. This feature closes that gap:
- Josh: "Heather, what are you working on?"
- Heather: responds with current run status without abandoning the task
- Josh: "Cancel that" → graceful cancellation
- Josh: "After that, also check my calendar" → queued follow-up

Also in the beta: **real-time wake-name gating** — Heather only activates in voice when her name is spoken, preventing spurious triggers.

**Risk level:** LOW — no action needed now. Track 2026.5.24 stable (~June 1-4).

---

## Persistent Critical Findings (unchanged from evening scan)

| Finding | Severity | Day # | Fix Available Now? |
|---------|----------|-------|--------------------|
| JOSH-30/JOSH-48: MEMORY.md never created | CRITICAL | **37+** | ✅ Yes — 5 min, zero risk |
| JOSH-31: HEARTBEAT.md empty | HIGH | **37+** | ✅ Yes — 5 min |
| JOSH-39: Upgrade to OpenClaw | HIGH | Now **target: 2026.5.22** | Upgrade required |
| JOSH-37/JOSH-49: SOUL.md generic template | MEDIUM | **37+** | ✅ Yes — 15 min |
| JOSH-50: Dead OpenRouter fallback claude-3.5-haiku | MEDIUM | **17** | ✅ Yes — 1 min |
| JOSH-33: iMessage paused (needed for JOSH-52 reactions) | MEDIUM | 28+ | Requires investigation |

---

## Platform Research Notes (2026-05-25 Morning)

- **OpenClaw latest stable:** 2026.5.22 (corrected from 2026.5.20 in evening scan)
- **OpenClaw latest beta:** 2026.5.24-beta.2 — iMessage reactions, voice status queries, Meeting Notes plugin, adaptive image compression. Do NOT deploy to Josh.
- **AlphaClaw:** 0.9.16 — no new release. 10 days without update.
- **Community sentiment:** Stable adoption of 2026.5.22. No regressions reported. Memory startup performance improvement (4100×) is widely noted as immediately noticeable.
- **iMessage reactions tracking:** Confirmed in 2026.5.24-beta.2 release notes. High community interest — Josh-relevant use case (personal assistant with iMessage integration) is the exact target persona for this feature.
- **Immediate no-upgrade actions (day 37 of documentation, day 0 of implementation):**
  1. Create `workspace/MEMORY.md` — CRITICAL, 5 min
  2. Add content to `workspace/HEARTBEAT.md` — HIGH, 5 min
  3. Personalize `workspace/SOUL.md` — MEDIUM, 15 min
  4. Remove dead `openrouter/anthropic/claude-3.5-haiku` fallback — MEDIUM, 1 min

---

## Morning Finding Summary

| Finding | What | Impact | Act When |
|---------|------|--------|----------|
| JOSH-51 | 2026.5.22 now stable — skip 5.20 | Corrects upgrade target | Upgrade this week |
| JOSH-52 | iMessage 👍/👎 approvals (beta) | Major UX reduction in approval friction | Track — upgrade to 5.24 when stable |
| JOSH-53 | Active Memory blocked until upgrade | Highest-priority first plugin post-upgrade | Upgrade first |
| JOSH-54 | Discord voice status queries (beta) | Quality of life during active runs | Track — upgrade to 5.24 when stable |

---

*Generated by AlphaClaw Apex Fleet Research Agent — Morning Scan — 2026-05-25 (Day 37)*
