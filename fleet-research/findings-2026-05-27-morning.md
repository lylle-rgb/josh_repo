# Fleet Research — Josh (Heather Schwartz) | 2026-05-27 Morning Scan

**Scan type:** Morning (web research, release tracking, community insights)
**Date:** 2026-05-27
**Instance:** Josh Meyers — Heather Schwartz (personal assistant)
**Repo:** lylle-rgb/josh_repo
**Previous scan:** 2026-05-26 evening
**Day count:** Day 39 of fleet monitoring

---

## Platform Status

| Item | Current | Latest | Gap |
|------|---------|--------|-----|
| OpenClaw | 2026.3.22 | **2026.5.22 stable** | **~65 days behind — upgrade OVERDUE** |
| AlphaClaw | Unknown | 0.9.16 | No new release — Day 13 without update |
| Primary model | google/gemini-3-flash-preview | — | Active |
| Beta train | **2026.5.26-beta.2** shipped this morning | — | Beta — do NOT upgrade |

---

## New Findings — Morning of 2026-05-27

### FINDING-JOSH-61 | OpenClaw 2026.5.26-beta.2 Released This Morning — What's Coming Next
**Severity:** INFO (advance intelligence — beta train)
**Status:** NEW — released May 27 at 05:46 UTC

OpenClaw shipped `2026.5.26-beta.2` this morning. This is a **pre-release** — do NOT upgrade. It defines what the next stable release (approximately 2026.5.28–5.30) will contain.

**What's in this beta that matters for Heather:**

| Change | Josh/Heather Relevance |
|--------|------------------------|
| **Gateway startup avoids repeated plugin/channel/session/filesystem scans** | Each Discord session warms up faster — Heather responds to Josh quicker on first message |
| **Visible replies separated from slower follow-up work** | Heather can send a confirmation reply while still processing; less perceived lag |
| **Approvals: /approve decisions routed through trusted approval runtime** | Manual approval flow (Ask before sending email, etc.) works reliably — no more expired/unknown approvals |
| **@openclaw/fs-safe updated to 0.2.7** | Better-effort Node write fallbacks for private stores, secret writes, run logs on Linux — more reliable MEMORY.md and daily log writes |
| **Agents fail closed on provider-less session model ambiguity** | Gemini + OpenRouter fallback routing becomes deterministic — Josh won't silently get the wrong model |

**Action:** None now. Watch for 2026.5.26 stable (~2 days). Upgrade path remains: 2026.3.22 → 2026.5.22 stable today, then 2026.5.26 when it graduates.

**Risk level:** INFO — forward intelligence only.

---

### FINDING-JOSH-62 | AI Memory Is Now Baseline Expectation — Industry Context for Day 39
**Severity:** MEDIUM (urgency escalation — industry benchmark)
**Status:** NEW — from mem0.ai State of AI Agent Memory 2026 report

The AI agent memory market reached **$6.27 billion in 2026**, projected to grow to $28.45 billion by 2030. Key industry shift:

> "Memory is no longer a differentiator. It is a baseline expectation."

The BEAM benchmark (1M and 10M token scales) is now the standard for evaluating production memory systems — it cannot be solved by expanding context windows alone, requiring true persistent storage.

**What this means for Heather at Day 39:**
- Heather has completed ~39 sessions with zero persistent memory
- Every competitor personal AI assistant now ships persistent cross-session memory by default
- The gap between what Heather *could* remember and what she *does* remember grows with each session
- Josh's personal context (Bliss brand decisions, Oben HiFi updates, calendar patterns, contact preferences) is being relearned from scratch 39 times

The fix (MEMORY.md creation + compaction config) is 15 minutes of work. The cost of not doing it is compounding daily.

**Risk level:** MEDIUM — urgency escalator for the Day 39 MEMORY.md gap.

---

### FINDING-JOSH-63 | Discord Rate Limiting Risk — requireMention: false in Active Guild
**Severity:** LOW (preventive awareness)
**Status:** NEW — from OpenClaw Discord community best practices research

Community guidance (2026): Discord's API rate limits are real. Bots responding to **all messages** in an active server (`requireMention: false`) can hit Discord API rate limits if the server becomes busy.

Josh's Discord config:
```json
"guilds": {
  "1484448262290276464": {
    "requireMention": false  // Heather responds to ALL messages without @mention
  }
}
```

**Community recommendation:** Start with monitored channels, then expand. If the guild (`1484448262290276464`) becomes a busy shared server rather than Josh's private server, rate limit pressure increases.

**Recommended check (post-upgrade):** Run `openclaw channels status --probe` to verify Heather can see target channels and monitor Discord API response times. If the guild is Josh's private server with low traffic, this is low risk.

**Risk level:** LOW — preventive. No action needed unless guild traffic increases.

---

### FINDING-JOSH-64 | AlphaClaw Self-Update Reliability — Docker Instances
**Severity:** INFO (infrastructure context)
**Status:** NEW — from AlphaClaw release research

AlphaClaw included a **fix for self-update failures on Docker** in a recent minor release. The fix corrects node_modules path matching for locating the consumer project root during in-place updates.

**Relevance for Josh:** If Josh's VPS runs AlphaClaw in Docker, in-place updates via the AlphaClaw UI should now work reliably. Previously, the self-update could fail silently on Docker instances, leaving AlphaClaw and OpenClaw on stale versions without notification.

**Action:** No immediate change needed. When upgrading OpenClaw to 2026.5.22, verify the AlphaClaw self-update path first — or do a manual upgrade if Docker is confirmed.

**Risk level:** INFO — awareness for upgrade planning.

---

### FINDING-JOSH-65 | openclaw doctor — Health Check Command Post-Upgrade
**Severity:** INFO (operational tip)
**Status:** NEW — from OpenClaw documentation research

OpenClaw provides a built-in health check command:
```bash
openclaw doctor
```

This verifies environment, checks connection to Discord, confirms intents are set (Message Content, Server Members, Presence Intent), and validates channel visibility. Community guidance calls this the first step after any upgrade.

**Add to Josh's upgrade checklist:**
1. Backup: `cp openclaw.json openclaw.json.bak-pre-5.22`
2. Upgrade via AlphaClaw UI → 2026.5.22
3. Run `openclaw doctor` to verify
4. Run `openclaw channels status --probe` to confirm Discord visibility
5. Verify MEMORY.md writes in first session post-upgrade

**Risk level:** INFO — operational tip for upcoming upgrade.

---

## Persistent Critical Issues (Day 39 Morning)

| Finding | Severity | Day # | Fix |
|---------|----------|-------|-----|
| JOSH-30/48: MEMORY.md never created | CRITICAL | **39** | 15 min |
| JOSH-31/54: HEARTBEAT.md empty | HIGH | **39** | 5 min |
| JOSH-59: No compaction config in openclaw.json | HIGH | **2** | 2 min, no restart |
| JOSH-39→57: Upgrade to OpenClaw 2026.5.22 | HIGH | **6 days overdue** | AlphaClaw UI |
| JOSH-50: Dead fallback (claude-3.5-haiku) | MEDIUM | **19** | 1 min, no restart |
| JOSH-34/56: Emoji contradiction in AGENTS.md | MEDIUM | **39** | 2 min |
| JOSH-37: SOUL.md generic template | MEDIUM | **39** | 30 min |
| JOSH-55: TOOLS.md blank | MEDIUM | **39** | 10 min |

**Day 39. Zero implementations across 39 days.**

---

## Morning Action Priority (2026-05-27)

**All zero-downtime, zero-upgrade — apply directly in GitHub:**

1. **Add compaction + contextPruning to `openclaw.json`** — prerequisite for memory to work (JOSH-59)
2. **Create `workspace/MEMORY.md`** — Day 39, 15 min (JOSH-30)
3. **Populate `workspace/HEARTBEAT.md`** — Gmail + Calendar tasks (JOSH-31)
4. **Fix dead fallback** — remove `openrouter/anthropic/claude-3.5-haiku` (JOSH-50)
5. **Fix emoji contradiction in `workspace/AGENTS.md`** — remove/override "React Like a Human!" section (JOSH-34)
6. **Populate `workspace/TOOLS.md`** — Google auth, Discord guild, model chain, iMessage status (JOSH-55)

**Then (VPS access):**
7. Upgrade to OpenClaw 2026.5.22 — run `openclaw doctor` post-upgrade

---

## Research Sources (Morning 2026-05-27)

- OpenClaw 2026.5.26-beta.2 release notes (released May 27 05:46 UTC) — beta train, do not deploy
- mem0.ai State of AI Agent Memory 2026 — $6.27B market, BEAM benchmark, memory as baseline
- OpenClaw Discord community best practices (docs.kanaries.net, team400.ai, skywork.ai) — rate limits, requireMention risk, openclaw doctor
- AlphaClaw release research (github.com/chrysb/alphaclaw) — self-update Docker fix, exec-approval seeding
- AlphaClaw 0.9.16 — Day 13 without new release

*Generated by AlphaClaw Apex Fleet Research Agent — Morning Scan — 2026-05-27 (Day 39)*
