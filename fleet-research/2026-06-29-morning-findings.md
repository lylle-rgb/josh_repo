# Fleet Research: Josh (Heather) — Morning Scan
**Date:** 2026-06-29 | **Scan type:** Morning | **Agent:** AlphaClaw Fleet Research

## Summary
**🚨 Day 100 is TODAY.** Google Workspace OAuth has been disconnected for exactly 100 days — mandatory escalation threshold reached. Morning web research confirms: OpenClaw 2026.6.11 is still in beta, 2026.6.10-stable is Day 6 (clean overnight). AlphaClaw 0.9.18 remains current — no 0.9.19 released. Noah scope broken Day 20. All other open items unchanged.

> **Note on scan order:** This morning scan runs after the June 29 evening scan (scheduling overlap). The evening scan captured the full critical picture; this morning scan adds overnight web research confirmation and updates day counts.

---

## New Findings

### F66 — CRITICAL: Google Workspace Day 100 — CONFIRMED TODAY
**Risk:** CRITICAL — escalation milestone reached; mandatory proactive surfacing

- April 27, 2026 → June 29, 2026: **Day 100 of Google Workspace OAuth disconnection confirmed this morning**
- Per SOUL.md rule: "At Day 100 and every 10 days after, surface to Josh proactively with concrete fix steps — not just a mention"
- Gmail and Calendar remain completely inaccessible. Every heartbeat no-ops silently on email/calendar checks.
- **This is the single most impactful 5-minute task in Josh's setup.**

**Heather's required script (use at very next main session):**
> "Today is Day 100. Email and calendar have been disconnected for 100 days. The fix takes 5 minutes: AlphaClaw → General tab → Google Workspace OAuth → https://5.78.142.81.sslip.io#general. I'm required to surface this at every session until it's fixed."

**Action:** Josh connects at https://5.78.142.81.sslip.io#general — browser OAuth flow, no VPS, no upgrade required.
**Next escalation day:** Day 110 (July 9, 2026) if still unresolved.

---

### F67 — CONFIRMED: 2026.6.11 Still Beta — Morning Web Verification
**Risk:** LOW — confirmation finding

Morning web research (June 29) confirms: `openclaw@2026.6.11-beta.1` is the only 2026.6.11 release on GitHub. npm stable channel still returns `2026.6.10`. No beta.2 or stable tag has been published overnight.

**2026.6.11 features queued for stable promotion:**
- Per-DM model overrides (useful: route heavy reasoning tasks to Pro, keep Flash for casual)
- `openclaw agent --message-file` (batch file-driven workflows)
- RAFT CLI wake bridge (remote agent wake-up path)
- Richer Discord output with HTML table normalization
- Slack relay mode, native Mattermost /oc_queue

**Action:** Upgrade target unchanged — **2026.6.10-stable**. After landing there, verify: `npm show openclaw@2026.6.11 version`. If stable tag present, evaluate 2026.6.11 features — per-DM model overrides are directly useful for Josh's mixed-use Discord setup.

---

### F68 — CONFIRMED: AlphaClaw 0.9.18 Still Current — No 0.9.19
**Risk:** LOW — confirmation finding

Morning web research found no evidence of an AlphaClaw 0.9.19 release. Current version remains `0.9.18`. The AlphaClaw self-update fix for Docker (temp-dir + cp install pattern) was shipped in 0.9.18 and remains the latest.

**Action:** None required. AlphaClaw will notify in-app when an update is available.

---

### F69 — INFO: 2026.6.10 Stable Window Day 6 — Green Light Continues
**Risk:** LOW — status check

2026.6.10-stable released June 24 at 03:01 UTC — now 6 days old. No new regressions reported in overnight community channels. ClawStat.us still clean.

Key PRs confirmed in 2026.6.10 that directly benefit Josh's setup:
- **PR #96233:** Heartbeat prompt contribution fix — required for reliable cron heartbeats
- **PR #93051:** Cron retry backoff — prevents cron jobs from silently dropping after transient errors
- **SQLite migration** (at 2026.6.6 hop): Clears iMessage's malformed inbox-state.json automatically

**Action:** Upgrade window fully open. No new reason to wait.
**Staged path:** `2026.3.22 → 2026.5.27 → 2026.6.2 → 2026.6.5 → 2026.6.6 → 2026.6.10`
**Before starting:** Run `openclaw backup create` to archive workspace state.

---

## Persistent Findings (Carried Forward — Day Counts Updated June 29)

| Finding | Severity | Status | Day Count |
|---------|----------|--------|-----------|
| Google Workspace OAuth disconnected | 🚨 CRITICAL | ⏳ 5-min browser fix | **Day 100** |
| OpenClaw 2026.3.22 → upgrade to 2026.6.10 | HIGH | ⏳ Needs VPS + ~30 min | Day 96 since release |
| iMessage monitoring paused | MEDIUM | ⏳ Auto-fix at 2026.6.6 hop | Day 65 |
| Heartbeat cron not deployed | MEDIUM | ⏳ Bundle with upgrade | Day 15 |
| OPENCLAW_TIMEZONE not set | MEDIUM | ⏳ 1 min Envars tab (do NOW) | — |
| Active Memory + Dreaming not enabled | MEDIUM | ⏳ Blocked on upgrade | — |
| Model: gemini-3-flash-preview (migrate now) | MEDIUM | ⏳ 5 min Browse tab (do NOW) | — |
| BRAVE_API_KEY missing | LOW | ⏳ 2 min Envars tab | — |
| Noah scope broken | INFO | ⛔ Fleet admin action | Day 20 |

**Staged upgrade path (unchanged):**
```
2026.3.22 → 2026.5.27 → 2026.6.2 → 2026.6.5 → 2026.6.6 → 2026.6.10
```
- Run `openclaw backup create` before first hop
- `openclaw update` at each hop, test Discord + memory after each
- Skip 2026.6.8 and 2026.6.9 — jump directly 2026.6.6 → 2026.6.10
- Verify before each hop: `npm show openclaw@latest version` = `2026.6.10`

---

## Priority Action Table (June 29 Morning)

| Priority | Action | Where | Effort | Requires Upgrade? |
|----------|--------|--------|--------|-------------------|
| 🚨 1 | Connect Google Workspace OAuth — **Day 100 TODAY** | Browser → https://5.78.142.81.sslip.io#general | 5 min | No |
| ⚠️ 2 | Set `OPENCLAW_TIMEZONE=America/Los_Angeles` | AlphaClaw Envars tab | 1 min | No |
| ⚠️ 3 | Migrate model → `google/gemini-3.5-flash` + Haiku 4.5 fallback | Browse tab → openclaw.json | 5 min | No |
| ⚠️ 4 | Set `BRAVE_API_KEY` | AlphaClaw Envars tab | 2 min | No |
| ⚠️ 5 | Run `openclaw backup create` before upgrade | VPS SSH | 2 min | No |
| ⚠️ 6 | Execute staged upgrade to 2026.6.10 | VPS SSH → `openclaw update` (6 hops) | ~30 min | Yes (self) |
| ⚠️ 7 | Enable Active Memory + Dreaming post-upgrade | Browse tab → openclaw.json | 5 min | After upgrade |
| ⚠️ 8 | Deploy heartbeat cron post-upgrade | Browse tab → openclaw.json | 10 min | After upgrade |
| ℹ️ 9 | Evaluate AlphaClaw Apex for fleet management | Fleet admin | — | No |
| 🔴 10 | Fix Noah fleet scope: `noah--repo` → `Noahrepo2` | Fleet admin session config | 5 min | No |

---

## Model Migration (Can Apply Now — No Upgrade Needed)

Apply via AlphaClaw Browse tab → openclaw.json → edit model block → save → restart:

```json
"model": {
  "primary": "google/gemini-3.5-flash",
  "fallbacks": [
    "openrouter/anthropic/claude-haiku-4-5",
    "openrouter/google/gemini-3.5-flash"
  ]
}
```

**Why:** Eliminates preview shutdown risk (gemini-3-flash-preview), upgrades fallback 2 from claude-3.5-haiku to claude-haiku-4-5, and fixes the same-provider-primary-and-fallback gap (Finding 31). Safe to do now — no VPS upgrade required.

---

## What Changed Since Last Scan (June 29 Evening)

- **F66 (NEW):** Google Workspace officially at **Day 100 TODAY** — escalation milestone confirmed; morning scan adds the mandatory scripted framing for Heather
- **F67 (NEW):** 2026.6.11 overnight check — still beta confirmed, no promotion to stable
- **F68 (NEW):** AlphaClaw 0.9.18 overnight check — still current, no 0.9.19 released
- **F69 (NEW):** 2026.6.10 Day 6 clean signal — upgrade window remains fully open
- **Day counts incremented:** Google Workspace 100 (milestone), iMessage 65, heartbeat cron Day 15, Noah scope Day 20
- **No change:** All other open issues persist. Overnight web research found no new blocking regressions or version changes.

---

_Generated by AlphaClaw Fleet Research Agent — 2026-06-29 Morning_
_Scan scope: OpenClaw npm stable channel, 2026.6.11 beta status overnight check, AlphaClaw GitHub releases, community signal (ClawStat.us), day count updates_
