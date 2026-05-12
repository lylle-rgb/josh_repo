# Fleet Research — Josh / Heather Schwartz — Morning Scan

**Scan Date:** 2026-05-13 (Morning)  
**Agent:** AlphaClaw Apex Fleet Research Agent  
**Instance:** Josh — Discord bot Heather Schwartz (personal assistant: iMessage, email, calendar, contacts)  
**Previous findings:** `findings-2026-05-12-evening.md` (Day 25). All prior findings remain unresolved.

---

## Platform News (New Since May 12 Evening Scan)

### v2026.5.10-beta.6 — Released May 11 (overnight confirmation)

The sixth beta in the 2026.5.10 cycle was confirmed May 11. **Current stable remains 2026.5.7.** No new stable released overnight. Beta.6 continues to add developer tooling; no production-safe features new for Josh's instance.

### threadBindings — Now Documented and Production-Ready in 2026.5.x

The `threadBindings` feature (previewed in X posts earlier this week) is fully documented and stable in the 2026.5.x series. It replaces the older split `subagent`/`ACP` thread-spawn toggles with a unified `session.threadBindings.spawnSessions` key. Known regression in v2026.4.5 (`thread_binding_invalid`) is resolved in 2026.5.x.

**What it enables for Heather:**
- Users can `/focus <target>` to bind a Discord thread to a subagent session
- Multi-turn sub-agent conversations stay isolated in their own thread instead of cluttering the main channel
- `/agents` shows active runs and binding state
- `/unfocus` releases the binding

**Config to add to `openclaw.json`:**
```json
"session": {
  "dmScope": "per-channel-peer",
  "threadBindings": {
    "enabled": true,
    "spawnSessions": true,
    "idleHours": 24,
    "maxAgeHours": 168
  }
}
```

**Risk level:** LOW — additive feature, no behavior change unless Josh explicitly uses `/focus`.

### Retry-Aware Cron — Config Now Documented

The `cron.retry` block is now stable and documented. Cron jobs that fail due to rate limits, network errors, or server errors can auto-retry with exponential backoff. Currently neither instance has this configured.

**Config to add to `openclaw.json` (when cron is active):**
```json
"cron": {
  "retry": {
    "maxAttempts": 3,
    "backoffMs": [60000, 120000, 300000],
    "retryOn": ["rate_limit", "overloaded", "network", "server_error"]
  }
}
```

**Why it matters for Heather:** Heartbeat-driven cron jobs (email checks, calendar polls) currently fail silently if Gemini is overloaded. With retry config, transient Gemini 3 Flash provider errors retry up to 3 times before giving up — critical for a 24/7 personal assistant reliability profile.

**Risk level:** LOW — additive only, protects existing cron behavior.

---

## New Findings — Josh Instance (Day 26)

### Finding 32. threadBindings Unlocks Safe Multi-Agent Discord Use
**Risk: MEDIUM | Days pending: NEW**

Heather's Discord guild has `requireMention: false` and `groupPolicy: open`, meaning she participates in all messages. Without `threadBindings`, any sub-agent spawn during a complex task (e.g., researching while managing Josh's inbox) runs inline in the main channel — cluttering conversation and mixing contexts.

With `threadBindings.spawnSessions: true`, sub-agents open in their own thread automatically. Josh can still monitor them but they don't interrupt the main flow.

**Exact change:** Apply config block above to `session` in `openclaw.json`. Requires OpenClaw ≥ 2026.5.x (version gap must be resolved first).
**Risk level:** LOW. No behavior change until Josh or Heather explicitly spawns a sub-agent.

---

### Finding 33. Retry-Aware Cron — Silent Failure Protection
**Risk: MEDIUM | Days pending: NEW**

When HEARTBEAT.md is finally activated (Finding: HIGH, 26 days pending), Heather will run scheduled email/calendar/iMessage checks via cron. Without `cron.retry`, a Gemini rate limit during a scheduled check produces a silent miss — Josh gets no notification, no retry, and no awareness that the check failed.

**Exact change:** Apply retry config block above when cron becomes active.
**Prerequisite:** OpenClaw updated (Finding: HIGH), HEARTBEAT.md populated (Finding: HIGH).
**Risk level:** LOW — additive protective config.

---

### Finding 34. Avatar Field Blank in IDENTITY.md
**Risk: LOW | Days pending: NEW**

`IDENTITY.md` has Name, Creature, Vibe, and Emoji set ("Heather", "AI Assistant", "Sharp, Helpful, Resourceful", 🫡), but `Avatar` is empty. While functional, AlphaClaw Apex uses the avatar field in its fleet dashboard.

**Exact change (optional):** Add an avatar URL or workspace-relative path to `workspace/IDENTITY.md`:
```
- **Avatar:** https://api.dicebear.com/8.x/notionists/svg?seed=Heather
```
**Risk level:** COSMETIC — no functional impact.

---

## Persistent High-Priority Items — Day 26 Summary

**Version gap: 2026.3.22 → 2026.5.7 = 13 releases, 83 days.**  
**iMessage monitoring: 18 days dark.**  
**Email last polled: 15 days ago.**  
**HEARTBEAT.md: effectively empty (only comments) — 26 days.**  
**MEMORY.md: never created — 26 days.**  
**No daily memory files — 26 days.**

| Finding | Severity | Days Pending | Status |
|---|---|---|---|
| OpenClaw 13 releases outdated | HIGH | 26 | ⬜ Pending |
| iMessage monitoring dark (~April 26) | HIGH | 18 days dark | ⬜ Pending |
| HEARTBEAT.md effectively empty | HIGH | 26 | ⬜ Pending |
| No daily memory files | HIGH | 26 | ⬜ Pending |
| MEMORY.md never created | MEDIUM | 26 | ⬜ Pending |
| SOUL.md never evolved | MEDIUM | 26 | ⬜ Pending |
| No-emoji rule not in SOUL.md | MEDIUM | 26 | ⬜ Pending |
| Bootstrap TOOLS.md stale (55 days) | MEDIUM | 26 | ⬜ Pending |
| inbox-state.json malformed + thread pending | MEDIUM | 26 | ⬜ Pending |
| TOOLS.md template only | MEDIUM | 26 | ⬜ Pending |
| memory-core plugin not installed | MEDIUM | 26 | ⬜ Pending |
| Active Memory admin scope (5.7) | MEDIUM | 8 | ⬜ Noted |
| Retired claude-3.5-haiku fallback | LOW | 8 | ⬜ Noted |
| Discord streaming off | LOW | 26 | ⬜ Pending |
| No compaction config | LOW | 26 | ⬜ Pending |
| IDENTITY.md avatar blank | LOW | new | ⬜ Pending |
| iMessage cloud proxy root cause | INFO | 7 | ⬜ Investigate |
| threadBindings — multi-agent Discord | MEDIUM | new | ⬜ Post-update |
| Retry-aware cron — silent failure protection | MEDIUM | new | ⬜ Post-heartbeat |
| /context map — token visibility | OPPORTUNITY | 1 | ⬜ Post-stable |
| Per-agent tool overrides (Discord boundary) | OPPORTUNITY | 1 | ⬜ Post-stable |
| A2A 20-turn sub-agent delegation | OPPORTUNITY | 1 | ⬜ Post-stable |

**Correct implementation order (unchanged):**
1. Update OpenClaw to 2026.5.7 (unblocks all 5.x features)
2. Populate HEARTBEAT.md (unblocks proactive email/calendar/iMessage checks)
3. Create MEMORY.md + daily memory scaffold
4. Install memory-core plugin (after admin scope confirmed)
5. Apply compaction config
6. Add cron retry config (when cron active)
7. Enable threadBindings
8. Apply per-agent tool overrides (post-2026.5.10-stable)

---
*Generated by AlphaClaw Apex Fleet Research Agent — Morning Scan — 2026-05-13*
