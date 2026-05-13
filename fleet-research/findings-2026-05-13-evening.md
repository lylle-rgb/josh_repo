# Fleet Research — Josh / Heather Schwartz — Evening Scan

**Scan Date:** 2026-05-13 (Evening)  
**Agent:** AlphaClaw Apex Fleet Research Agent  
**Instance:** Josh — Discord bot Heather Schwartz (personal assistant: iMessage, email, calendar, contacts)  
**Previous findings:** `findings-2026-05-13-morning.md` (Day 26 Morning). All prior findings remain unresolved.

---

## Platform News (New Since Morning Scan)

### v2026.5.12-beta.3 Confirmed — 2026.5.10 Stable Imminent

The stable release tracker now shows v2026.5.12-beta.3 in flight. The beta series leapfrogging to 5.12 means the team is confident in 5.10 stable quality — **2026.5.10 stable is expected before end of week.** Current stable remains 2026.5.7.

**What this means for Heather:** Features previewed as "2026.5.10 opportunities" (threadBindings, `/context map`, A2A 20-turn, per-agent tool overrides) are now days away from stable. This makes updating to 2026.5.7 now even more urgent — it positions Josh's instance for a second update jump (5.7 → 5.10) as soon as it drops.

**Version gap summary:** Josh at 2026.3.22 → current stable 2026.5.7 = **13 releases behind.** A second update target (5.10) arrives within days.

---

### Active Memory Plugin — Deep Dive

Evening research into the `memory-core` Active Memory plugin reveals it is significantly more powerful than noted in prior scans:

**How it works:** Before generating each response, memory-core runs a silent pre-reply sub-agent that queries semantic + keyword memory (hybrid search). It surfaces relevant preferences, historical context, and prior session details automatically — without the agent needing to remember to read specific files.

**For Heather specifically:**
- Auto-surfaces Josh's communication preferences before drafting any message
- Recalls prior email threads and decisions when new related emails arrive
- Remembers Josh's calendar patterns and meeting prefs without re-reading them each session
- Proactive recall means Heather can "know" Josh without explicitly re-reading MEMORY.md every time

**Critical gap discovered:** `memory-core` is NOT in Josh's `plugins.allow` list at all. Noah's instance has it; Josh's does not. This is a miss — a 24/7 personal assistant is exactly the use case Active Memory was designed for.

**Embedding provider:** Josh's Google/Gemini setup can use Google embedding APIs for hybrid search (semantic + keyword). No additional auth config required beyond enabling the plugin.

**Admin scope note (2026.5.7):** Active Memory global toggles now require admin scope. This is a security boundary, not a bug. Requires the 2026.5.7 update before enabling.

---

### Pre-Compaction Memory Flush — Unverified, Risk Confirmed

OpenClaw has a built-in pre-compaction memory flush that triggers a silent agentic turn before context compaction, reminding the model to write important info to disk before the window is trimmed. Josh's `openclaw.json` has **no `compaction` config at all** (Noah has `reserveTokensFloor: 40000` and `memoryFlush.enabled: true`).

**Risk:** During long sessions (email triage + calendar + iMessage all in one turn), context can grow large and trigger compaction. Without the flush config, important context may be silently dropped — never written to a daily memory file. This compounds the 26-day memory gap finding: Heather may have been losing session context at every compaction event since Day 0.

---

### workspace/memory Directory — 26 Days Empty (Confirmed)

Evening audit of `workspace/memory/` confirms the directory contains only:
- `inbox-state.json` (243 bytes — previously flagged as malformed/stale)
- `onboarding-google.md` (1.2KB — Day 0 onboarding artifact)

**No daily log files have ever been created.** Not a single `YYYY-MM-DD.md`. Heather has been running for 26 days with zero written memory continuity. Every session starts completely fresh.

This is a behavioral gap, not a config gap. AGENTS.md instructs the agent to create daily logs, but with HEARTBEAT.md empty, there is no periodic trigger to prompt memory writing — so it never happens.

---

## New Findings — Josh Instance (Day 26 Evening)

### Finding 35. memory-core Plugin Missing — Personal Assistant Core Feature Absent
**Risk: HIGH | Days pending: NEW (newly identified)**

Josh's `openclaw.json` does not include `memory-core` in `plugins.allow`. Noah's instance does (though improperly configured). Active Memory's pre-reply semantic recall is the most impactful single feature for a 24/7 personal assistant: it eliminates the "cold start" problem by automatically surfacing the right context before each response.

**Exact changes needed to `openclaw.json`:**

1. Add `memory-core` to `plugins.allow`:
```json
"allow": ["discord", "usage-tracker", "memory-core"]
```

2. Add entry to `plugins.entries`:
```json
"memory-core": {
  "enabled": true
}
```

3. Add compaction config to `agents.defaults` (enabling the pre-compaction flush):
```json
"compaction": {
  "reserveTokensFloor": 30000,
  "memoryFlush": {
    "enabled": true,
    "softThresholdTokens": 4000
  }
}
```

**Note on floor:** 30K vs Noah's 40K — Gemini 3 Flash has a larger effective context window. Adjust based on observed behavior.

**Prerequisite:** OpenClaw must be updated to ≥2026.5.7 first (admin scope requirement for Active Memory).
**Risk level:** MEDIUM — additive plugin. No behavior change until first session after enable.

---

### Finding 36. workspace/memory Empty — 26 Days, Root Cause Confirmed
**Risk: HIGH | Days pending: 26 (confirmed this evening)**

The `workspace/memory/` directory has zero daily log files after 26 days of operation. This is the root cause of all memory-related failures: without daily logs, there is nothing for MEMORY.md to distill, nothing for the pre-compaction flush to find, and nothing for memory-core to index.

**Root cause chain:**
1. HEARTBEAT.md is empty → no periodic trigger → no regular prompts to write daily notes
2. No cron configured → no scheduled memory maintenance
3. Manual sessions end without written summary → next session starts cold

**This requires a live session to bootstrap.** No config change fixes it. Suggested prompt to send Heather in Discord:
```
Read your workspace/memory directory. Notice it has no daily log files.
Starting today, create memory/2026-05-13.md with a summary of what
you know about Josh, what tools you have, and any pending tasks.
Then update HEARTBEAT.md to remind yourself to write a short daily
summary note at the end of each active session.
```
**Risk level:** HIGH — this is the root cause of 26 days of memory loss.

---

### Finding 37. Pre-Compaction Flush Not Configured — Silent Context Loss
**Risk: MEDIUM | Days pending: NEW**

Josh's `openclaw.json` has no `compaction` configuration. Noah's instance has `reserveTokensFloor: 40000` and `memoryFlush.enabled: true`. Without the flush config, the built-in pre-compaction memory write is likely inactive — meaning any session involving sustained email/calendar/iMessage work (the entire use case) may be silently dropping context at compaction events.

**Exact change (add to `agents.defaults` in `openclaw.json`):**
```json
"compaction": {
  "reserveTokensFloor": 30000,
  "memoryFlush": {
    "enabled": true,
    "softThresholdTokens": 4000
  }
}
```

**Risk level:** LOW to apply. MEDIUM if left unfixed during long sessions.

---

## Persistent High-Priority Items — Day 26 Evening

**Version gap: 2026.3.22 → 2026.5.7 = 13 releases. 2026.5.10 stable expected within days.**  
**iMessage monitoring: 18 days dark.**  
**No daily memory files: 26 days confirmed.**  
**HEARTBEAT.md: empty for 26 days.**

| Finding | Severity | Days Pending | Status |
|---|---|---|---|
| OpenClaw 13 releases outdated | HIGH | 26 | ⬜ Pending |
| memory-core plugin missing entirely | HIGH | NEW | ⬜ Pending |
| workspace/memory empty — no daily logs | HIGH | 26 confirmed | ⬜ Pending |
| iMessage monitoring dark (~April 26) | HIGH | 18 | ⬜ Pending |
| HEARTBEAT.md effectively empty | HIGH | 26 | ⬜ Pending |
| Pre-compaction flush not configured | MEDIUM | NEW | ⬜ Pending |
| MEMORY.md never created | MEDIUM | 26 | ⬜ Pending |
| SOUL.md never evolved | MEDIUM | 26 | ⬜ Pending |
| No-emoji rule not in SOUL.md | MEDIUM | 26 | ⬜ Pending |
| Bootstrap TOOLS.md stale (55+ days) | MEDIUM | 26 | ⬜ Pending |
| inbox-state.json malformed + stale | MEDIUM | 26 | ⬜ Pending |
| TOOLS.md template only | MEDIUM | 26 | ⬜ Pending |
| Active Memory admin scope (5.7) | MEDIUM | 8 | ⬜ Noted |
| Retired claude-3.5-haiku fallback | LOW | 8 | ⬜ Noted |
| Discord streaming off | LOW | 26 | ⬜ Pending |
| IDENTITY.md avatar blank | LOW | 1 | ⬜ Pending |
| threadBindings — multi-agent Discord | MEDIUM | 1 | ⬜ Post-update |
| Retry-aware cron — silent fail protection | MEDIUM | 1 | ⬜ Post-heartbeat |
| v2026.5.10 stable landing this week | OPPORTUNITY | NEW | ⬜ Monitor |
| /context map — token visibility | OPPORTUNITY | 1 | ⬜ Post-5.10 |
| A2A 20-turn sub-agent delegation | OPPORTUNITY | 1 | ⬜ Post-5.10 |
| Per-agent tool overrides (Discord) | OPPORTUNITY | 1 | ⬜ Post-5.10 |

**Updated implementation order:**
1. Update OpenClaw to 2026.5.7 ← still the primary blocker
2. Add memory-core to plugins.allow + entries (NEW — now HIGH priority)
3. Add compaction config (NEW)
4. Populate HEARTBEAT.md (email/calendar/iMessage checks)
5. Bootstrap daily memory: trigger live session to create first memory file
6. Create MEMORY.md
7. Add cron retry config when cron is active
8. Enable threadBindings
9. Monitor for 2026.5.10 stable → second update jump

---
*Generated by AlphaClaw Apex Fleet Research Agent — Evening Scan — 2026-05-13*
