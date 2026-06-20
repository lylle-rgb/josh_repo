# Fleet Research — Josh (Heather Schwartz) | 2026-06-20 Morning Scan

**Scan type:** Morning delta check — version status, new bugs, overnight changes
**Date:** 2026-06-20 (morning)
**Instance:** Josh Meyers — Heather Schwartz (personal assistant)
**Repo:** lylle-rgb/josh_repo
**Prior scan:** 2026-06-20 evening — Day 4 heartbeat null escalated, Google Workspace Day 90 flagged

---

## TL;DR

- **NEW — Finding 28:** `userTimezone` is not set in openclaw.json — VPS defaults to UTC, which will silently misalign heartbeat/dreaming windows against Josh's LA time once activeHours is configured. Fix is one line.
- **NEW — Bug #67397:** Dreaming cron is gated by `heartbeat.activeHours` (no separate quiet override). Once activeHours is added, a 3 AM UTC dreaming schedule = 8 PM PDT — safe for now, but fragile. Document clearly before touching this config.
- **Hold confirmed:** 2026.6.9-stable has NOT shipped as of June 20 morning. npm `latest` = 2026.6.6.
- **Discrepancy noted:** One web source says "keep production on 2026.6.8" — this refers to GitHub's UI "Latest" label, not the npm stable channel. npm `latest` = 2026.6.6 is authoritative. Hold stands.
- Persistent blockers unchanged: heartbeat Day 4 null, Google Workspace Day 90, iMessage Day 54.

---

## Finding 28 — `userTimezone` Not Set: Silent Timezone Misalignment Risk (NEW)

**Risk: MEDIUM-HIGH — will silently break heartbeat/dreaming once activeHours is configured**

openclaw.json has no `userTimezone` configured. Per OpenClaw docs and GitHub Issue #67397:

> "If `userTimezone` is unset, OpenClaw falls back to the host machine's timezone. Timezone mismatches between `userTimezone` and `activeHours` cause silent heartbeat suppression."

Josh's VPS is in a datacenter — almost certainly UTC. Josh is in Los Angeles (PDT, UTC−7 in June). Without `userTimezone`:

- Any `heartbeat.activeHours` config (e.g., quiet from 23:00–08:00 LA time) will be evaluated in UTC instead
- UTC 23:00 = 4:00 PM PDT → heartbeats would go quiet at 4 PM Josh's time, 7 hours early
- The recommended dreaming schedule `"0 3 * * *"` (3 AM UTC = 8 PM PDT) currently falls inside Josh's active window — but this is only safe by coincidence. If `activeHours` is later narrowed, dreaming breaks silently.

**The fix is a single line in `agents.defaults`:**
```json
"agents": {
  "defaults": {
    "userTimezone": "America/Los_Angeles",
    ...
  }
}
```

Add this **before** adding any heartbeat or dreaming schedule to openclaw.json. It costs nothing and prevents a class of silent, hard-to-diagnose failures.

**Note on Bug #67397 (filed April 15, 2026):**
Dreaming cron is gated by `heartbeat.activeHours` with no independent override. If a 3 AM UTC dreaming job falls outside the active window (as evaluated in the configured timezone), it is silently skipped with `reason=quiet-hours`. The proposed fix (separate `dreaming.activeHours`) is not yet shipped. Until it is, keep dreaming schedule inside the active window or rely on timezone alignment being correct.

**When upgrading to 2026.6.9-stable, add `userTimezone` first, then add dreaming/heartbeat configs.**

---

## Finding 29 — Version Hold: Confirmed This Morning

**Risk: INFO — no change, confirming prior data**

| Channel | Version | Status |
|---------|---------|--------|
| npm `latest` (stable) | **2026.6.6** | Current safe target |
| 2026.6.8 | Released June 16 | ⛔ GitHub "Latest" label, but NOT on npm stable — critical regressions |
| 2026.6.9-beta.1 | June 19 | Pre-release only |
| 2026.6.9-stable | **NOT shipped** | Watching — could arrive today or tomorrow based on cadence |

**Discrepancy resolved:** At least one web source says "keep production on 2026.6.8." This refers to GitHub's "Latest" release badge — GitHub marks the most recent tagged version as "Latest" even if it's buggy. npm's `latest` tag is the authoritative stable channel, and it still points to 2026.6.6. Our hold stands.

**Staged path (unchanged):** 2026.3.22 → 2026.5.27 → 2026.6.2 → 2026.6.5 → 2026.6.6 → **[STOP — wait for 2026.6.9-stable]**

---

## Finding 30 — Dreaming Schedule: 3 AM UTC Is Currently Safe But Fragile

**Risk: LOW now, MEDIUM once activeHours is added**

The dreaming schedule in findings.md Finding 24 recommends `"0 3 * * *"` (3 AM UTC). In June (PDT):

- 3 AM UTC = 8 PM PDT = inside Josh's active window (8 AM–11 PM)

Currently safe. However:
1. Without `userTimezone` set (Finding 28), any future `activeHours` config will evaluate windows in UTC, not PDT
2. Bug #67397 means dreaming inherits heartbeat quiet hours with no escape valve
3. In winter (PST, UTC−8): 3 AM UTC = 7 PM PST — still fine, but closer to the drift zone

**Recommendation:** Add `userTimezone: "America/Los_Angeles"` (Finding 28) before enabling dreaming. Once set, the 3 AM UTC schedule is safe and intentional (8 PM PDT — end of evening, good consolidation time).

---

## Persistent Blockers (Unchanged)

| Blocker | Days | Status |
|---------|------|--------|
| Google Workspace OAuth not connected | Day 90 | ⛔ CRITICAL |
| iMessage monitoring paused | Day 54 | ⛔ HIGH |
| heartbeat-state.json all null | Day 4 (today morning) | ⛔ HIGH — cron not deployed |
| OpenClaw on 2026.3.22 | Day 90+ behind stable | ⚠️ HIGH (safe path exists) |
| Noah session scope mismatch | Day 8 | ⚠️ FLEET OPS |

---

## Platform Status (June 20 Morning)

| Item | Current | Target | Status |
|------|---------|--------|--------|
| OpenClaw | 2026.3.22 | 2026.6.9-stable | ⏸ Hold — wait for stable |
| 2026.6.9-stable | — | Not shipped | 🟡 Expected soon — could be today |
| npm `latest` | 2026.6.6 | — | 2026.6.8 NOT on stable channel |
| userTimezone | ❌ Not set | America/Los_Angeles | 🆕 Add before any heartbeat/dreaming |
| Dreaming schedule | `"0 3 * * *"` (planned) | Safe once TZ set | ⚠️ Safe now, fragile without Finding 28 |
| Google Workspace | Not connected | — | ⛔ Day 90 |
| iMessage | Paused | — | ⛔ Day 54 |
| heartbeat-state.json | All null | — | ⛔ Day 4 |
| MEMORY.md | Updated June 19 | — | ✅ Current |
| TOOLS.md | Updated June 19 | — | ✅ Current |

---

## Morning Priority Queue

| Priority | Action | Method | Effort |
|----------|--------|--------|--------|
| 1 — CRITICAL | Connect Google Workspace OAuth | AlphaClaw UI: https://5.78.142.81.sslip.io#general | ~30 min |
| 2 — HIGH | Ask Heather in Discord: "Are heartbeat checks running?" | Discord DM | ~2 min |
| 3 — HIGH | When 2026.6.9-stable ships: run staged upgrade to 2026.6.6 first | VPS: `openclaw update` | ~30 min |
| 4 — HIGH | Bundle with upgrade: add `userTimezone`, compaction, dreaming, heartbeat cron to openclaw.json | VPS edit | ~10 min |
| 5 — FLEET OPS | Fix Noah session scope (noah--repo → Noah-workspace or Noahrepo2) | Fleet config | ~5 min |

---

## Changes Applied This Morning (GitHub-Only)

- `fleet-research/2026-06-20-morning-findings.md` — this file (new)
- `fleet-research/findings.md` — Finding 28 added (userTimezone gap)
- `fleet-research/cross-customer-analysis.md` — updated to June 20 morning

*Sources: [OpenClaw Bug #67397](https://github.com/openclaw/openclaw/issues/67397) · [OpenClaw Heartbeat Docs](https://docs.openclaw.ai/gateway/heartbeat) · [SFAI Labs Heartbeat Guide](https://sfailabs.com/guides/openclaw-heartbeat-not-triggering) · [OpenClaw Cron Docs](https://docs.openclaw.ai/automation/cron-jobs)*
