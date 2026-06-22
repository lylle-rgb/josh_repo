# Fleet Research: Josh (Heather) — Morning Findings
**Date:** 2026-06-22 Morning
**Agent:** Heather Schwartz — Personal Assistant (Discord bot)
**Scan type:** Web research + status review
**Prior scan:** 2026-06-21 Morning (findings.md)

---

## Status: Upgrade Window Still Open

OpenClaw **2026.6.9-stable** (released June 21) remains the current `npm latest`. No hotfix or patch released overnight. Upgrade window is clean and confirmed open — Day 2.

Stagged upgrade path still valid:
```
2026.3.22 → 2026.5.27 → 2026.6.2 → 2026.6.5 → 2026.6.6 → 2026.6.9
```
Verify before upgrading: `npm show openclaw@latest version` should return `2026.6.9`.

---

## New Findings Since June 21 Morning

### Finding 33 — OpenClaw 2026.6.10-beta.2: Auto Fast Mode (DO NOT INSTALL)

**Priority: INFO — beta only, do not install**

2026.6.10-beta.2 was released June 22 with **automatic fast mode for short conversational turns**: OpenClaw detects brief back-and-forth exchanges, enables fast mode automatically, then returns to normal mode for longer analytical runs with bounded fallback and delivery behavior.

This is directly relevant to Heather — casual Discord exchanges with Josh ("what time is my next meeting?", "any urgent emails?") would be noticeably faster without any config change.

**Action:** Do not install. Stay on 2026.6.9-stable. Monitor beta progress; if 2026.6.10 stabilizes quickly, the next upgrade cycle can target it instead of stopping at 2026.6.9.

---

### Finding 34 — AlphaClaw Git Sync Reliability Fix (Silent Improvement)

**Priority: LOW-POSITIVE — no action needed**

AlphaClaw's hourly git sync now resolves the real git binary at runtime, fixing sync failures in containerized and hosted deployments. Josh's VPS uses AlphaClaw's auto git backup — this fix means the hourly commit-and-push of workspace files is more reliable across restarts and AlphaClaw watchdog recoveries.

No action required — applies automatically on next AlphaClaw restart or watchdog cycle.

---

### Finding 35 — AlphaClaw In-App OpenClaw Update Removed

**Priority: INFO — confirms VPS-only upgrade path**

AlphaClaw removed the in-app OpenClaw self-update path for hosted deployments. The AlphaClaw control UI no longer shows an update button for VPS installs. This confirms: Josh's upgrade **must go through VPS CLI** (`openclaw update`), not the AlphaClaw control UI. The UI upgrade path was only ever valid for desktop/local installs.

No action required — this is a path clarification, not a blocker.

---

### Finding 36 — Dreaming Config: Verify Key Path Before Applying

**Priority: LOW — clarifies Finding 22/24 before application**

Research surfaces a possible ambiguity: dreaming config may live under `plugins.entries.memory-core.config.dreaming` rather than as a top-level `"dreaming"` key, depending on whether memory-core is installed as a plugin (default in 2026.5+) or built-in.

**Recommended action:** Before applying the dreaming config in the upgrade session, run this on the VPS after upgrading:
```
openclaw config schema | grep -A 10 "dreaming"
```
or check live plugin state: `openclaw config get plugins.entries.memory-core`

Finding 22/24 config values (minScore: 0.8, schedule: `"0 3 * * *"`, maxPromotion: 10) are correct regardless of the key path.

---

### Finding 37 — TOOLS.md Stale: Upgrade Hold Lifted (Resolved This Scan)

**Priority: LOW — documentation housekeeping**
**Status: RESOLVED — TOOLS.md updated in this commit**

workspace/TOOLS.md contained an `⚠️ HOLD: Do NOT upgrade to 2026.6.8` banner and a `[STOP — wait for 2026.6.9-stable]` marker in the staged upgrade path. Both were stale since 2026.6.9-stable shipped June 21.

Updated in this commit: hold banner replaced with a concise skip note, current safe target corrected to 2026.6.9-stable, and the VPS-only upgrade path clarified.

---

## Open Items Status (June 22 Morning)

No open items resolved overnight. All items from June 21 carry forward with updated day counts:

| Item | Status | Day |
|------|--------|-----|
| Connect Google Workspace OAuth | ⏳ CRITICAL | Day 92 |
| Upgrade to 2026.6.9 (staged) | ⏳ HIGH — window open | Day 2 of window |
| Add userTimezone to openclaw.json | ⏳ MEDIUM-HIGH | Bundle with upgrade |
| Add compaction/memoryFlush config | ⏳ HIGH | Bundle with upgrade |
| Add dreaming config (verify key path first) | ⏳ HIGH | Bundle with upgrade |
| Deploy heartbeat cron to VPS | ⏳ HIGH | Day 7+ null |
| Set BRAVE_API_KEY (AlphaClaw UI) | ⏳ MEDIUM-HIGH | No VPS needed |
| Tighten Discord allowFrom | ⏳ MEDIUM-HIGH | After upgrade |
| Fix fallback chain (Google → Haiku first) | ⏳ MEDIUM | After upgrade |
| Noah scope fix (noah--repo → Noah-workspace) | ⏳ FLEET OPS | Day 11 |

---

## Cross-Customer Note

Noah's repo (`lylle-rgb/noah--repo`) returned 404 again this morning — Day 11. The fleet session scope references a repo that does not exist. Noah's actual workspace is `lylle-rgb/Noah-workspace`, which is outside this session's allowed scope.

**Fleet admin action needed:** Update session allowed repos to include `lylle-rgb/Noah-workspace` and remove the non-existent `lylle-rgb/noah--repo` reference.

Until resolved, Noah's agent (Market Catalyst) receives no fleet research updates, config audits, or workspace file improvements from morning/evening scans.
