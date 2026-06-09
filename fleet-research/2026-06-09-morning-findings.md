# Fleet Research — Josh (Heather Schwartz) | 2026-06-09 Morning Scan

**Scan type:** Morning web research + platform delta
**Date:** 2026-06-09
**Instance:** Josh Meyers — Heather Schwartz (personal assistant — iMessage, email, calendar, contacts)
**Repo:** lylle-rgb/josh_repo
**Prior scan:** 2026-06-09 evening — see fleet-research/2026-06-09-evening-findings.md

---

## Platform Status

| Item | Current | Stable Target | Latest Beta | Gap |
|------|---------|---------------|-------------|-----|
| OpenClaw | 2026.3.22 | **2026.6.2** | 2026.6.5-beta.5 (Jun 8) | **79 days** |
| AlphaClaw | Unknown | 0.9.18 | — | Check deployment |
| Primary model | google/gemini-3-flash-preview | — | — | Preview tag — watch for deprecation |
| MEMORY.md | **Missing** | Created | — | **Day 79** |

> **Stable target advanced:** npm stable is now **2026.6.2** (up from 2026.5.28 per prior morning scan). Josh's 79-day gap continues to widen daily.

---

## NEW Findings (June 9 Morning)

### FINDING-JOSH-56 | Cron jobs-state.json Isolation (2026.4.20+) — Foundation for Reliable Scheduling
**Severity:** MEDIUM
**Type:** Platform — available at 2026.4.20+, included in upgrade target 2026.6.2

OpenClaw 2026.4.20 isolated cron runtime execution state into a dedicated `jobs-state.json`, separate from main session state. Before this change, a crashed or restarted main session could corrupt in-progress cron job state, causing silent task failures. Key improvements bundled with this release:

- Cron jobs survive main session restarts cleanly
- `openclaw cron run --wait` gains timeout + poll-interval controls — scripts can block on a specific run ID
- Isolated browser tabs from cron runs auto-close at run completion (no orphaned browser processes)
- Queue drain reliability — no silent failures after VPS restart

**Why it matters for Heather:**
Heather has no scheduled cron jobs today. But the proactive email/calendar monitoring described in JOSH-31 (HEARTBEAT.md) should ultimately be built as cron jobs for exact morning timing — not just heartbeat polling, which can drift. The 2026.4.20+ cron reliability improvements are included in the 2026.6.2 upgrade target, so Heather gets them automatically on upgrade.

**Exact post-upgrade cron template (morning digest):**
```json
"cron": {
  "jobs": {
    "morning-digest": {
      "enabled": true,
      "schedule": "0 8 * * *",
      "prompt": "Check Josh's Gmail inbox for urgent unread messages. Check today's calendar events. Deliver a concise morning briefing to Josh via Discord.",
      "channel": "discord",
      "model": "google/gemini-3-flash-preview"
    },
    "afternoon-check": {
      "enabled": true,
      "schedule": "0 14 * * *",
      "prompt": "Check Josh's Gmail for new important messages since morning. Check for calendar changes or new events added today. Only message Josh if something urgent needs attention.",
      "channel": "discord",
      "model": "google/gemini-3-flash-preview"
    }
  }
}
```

**Risk level:** LOW — informational for now; all improvements apply automatically at 2026.6.2 upgrade

---

### FINDING-JOSH-57 | QQBot Reasoning Tag Stripping (2026.6.5-beta) — Discord Output Safety
**Severity:** LOW (forward-looking)
**Type:** Platform — available at 2026.6.5-stable

OpenClaw 2026.6.5-beta.5 (shipped June 8) strips model reasoning/thinking scaffolding before Discord channel delivery. The agent runtime now strips `<thinking>` and reasoning tags at the delivery boundary, preventing raw model reasoning from leaking into Discord replies.

**Why it matters for Heather:**
Heather uses Gemini models which don't emit reasoning tags by default. However:
1. If extended thinking is ever enabled on the OpenRouter Anthropic fallback (`openrouter/anthropic/claude-3.5-haiku`), the stripping protects Josh from seeing raw reasoning in Discord
2. This confirms the 2026.6.x series is hardening Discord output safety — a good signal for post-upgrade experience
3. Future Gemini thinking support (if added) would also benefit from this protection

**Action:** No action today. Track with 2026.6.5-stable upgrade.

**Risk level:** N/A

---

### FINDING-JOSH-58 | Operator Install Policy (2026.6.2) — Safer gog-cli and Skill Installs
**Severity:** LOW
**Type:** Platform — available at 2026.6.2 upgrade target

OpenClaw 2026.6.2 replaced the legacy "dangerous-code scanner" path for plugin and skill installs with an operator install policy. The new system has:
- Explicit operator authorization model — installs require policy approval, not just code scanning
- Clearer package/archive/source/upload/marketplace install surfaces
- Better doctor/CLI/ClawHub troubleshooting integration for failed installs

**Why it matters for Heather:**
When Heather's two critical skills are installed post-upgrade:
- `openclaw skills install gog` (Google Workspace CLI — JOSH-44)
- BlueBubbles iMessage skill (JOSH-33/45)

...they install through the new policy-based path rather than the legacy scanner. Fewer false-positive rejections, more predictable install flow.

**Action:** No action today. Benefit applies automatically at 2026.6.2 upgrade.

**Risk level:** N/A

---

## Persistent Findings (Status Update — Day 79)

| Finding | Severity | Days Open | Note |
|---------|----------|-----------|------|
| JOSH-30: MEMORY.md never created | **CRITICAL** | **79** | Zero long-term memory. GitHub-only. |
| JOSH-44: Google Workspace not connected | **CRITICAL** | 6 | Core integrations missing. VPS/setup. |
| JOSH-31: HEARTBEAT.md empty | HIGH | 79 | No proactive monitoring. GitHub-only. |
| JOSH-47: Dreaming blocked (needs upgrade + MEMORY.md) | HIGH | 6 | Post-upgrade to ≥2026.4.5. |
| JOSH-29/48: Platform 79 days behind stable 2026.6.2 | HIGH | **79** | Requires VPS upgrade. |
| JOSH-54: BOOTSTRAP.md not deleted | LOW | 1 | Confusion risk on restart. GitHub-only. |
| JOSH-55: TOOLS.md template-only | MEDIUM | 1 | Post-Google-connection. GitHub-only. |
| JOSH-37: SOUL.md not personalized | MEDIUM | 79 | GitHub-only. |
| JOSH-34: Emoji contradiction in AGENTS.md | LOW | 79 | USER.md says STRICT NO, AGENTS.md says yes. |
| JOSH-33/45: iMessage paused + malformed state | MEDIUM | 43 | Wait for upgrade + `openclaw doctor --fix`. |

---

## Immediate Action Queue (Priority Order)

### GitHub-Only (No VPS, No Downtime) — Apply Now

1. **JOSH-30 (CRITICAL, day 79):** Create `workspace/MEMORY.md` — even empty stub unblocks Dreaming and session continuity
2. **JOSH-31 (HIGH):** Populate `workspace/HEARTBEAT.md` with email/calendar check tasks
3. **JOSH-54 (LOW):** Delete `workspace/BOOTSTRAP.md` — onboarding complete, creates restart confusion
4. **JOSH-37 (MEDIUM):** Add Josh's business context to SOUL.md (Bliss/Oben, LA timezone, luxury/audio)
5. **JOSH-34 (LOW):** Fix emoji contradiction in AGENTS.md — match USER.md strict disable

### VPS/Setup Actions

1. **JOSH-44 (CRITICAL):** Connect Google Workspace via AlphaClaw UI → `https://5.78.142.81.sslip.io#general`
2. **JOSH-29/48 (HIGH):** Upgrade OpenClaw to 2026.6.2 via AlphaClaw UI
3. **AlphaClaw 0.9.18 (MEDIUM):** Upgrade for watchdog fix + security hardening + remote MCP support
4. **JOSH-33/45 (MEDIUM, post-upgrade):** Run `openclaw doctor --fix` for iMessage SQLite migration
5. **JOSH-47 (HIGH, post-upgrade):** Enable memory-core + create MEMORY.md to activate Dreaming

---

## Platform Research Notes (2026-06-09 Morning)

- **OpenClaw stable:** 2026.6.2 | **OpenClaw beta:** 2026.6.5-beta.5 (June 8, 2026)
- **2026.6.5 headline:** QQBot reasoning tag stripping, MCP tool result coercion, Parallel web search, SQLite state, Anthropic extended-thinking recovery
- **2026.6.2 headline:** Operator install policy, Discord/Telegram safety hardening, hardened agent/provider recovery, strengthened config security
- **2026.4.20 headline:** cron jobs-state.json isolation — foundational for scheduled task reliability (included in 2026.6.2 upgrade target)
- **AlphaClaw 0.9.17:** Per-agent thinking level control, Discord pairing cold-restart fix, login throttling
- **AlphaClaw 0.9.18:** OpenAI-compatible API proxy, managed remote MCP server support, timing-safe auth, rate-limiting
- **Community signal (X):** Claude Sonnet 4 / 4.5 via direct Anthropic API highlighted as optimal OpenClaw model; Heather uses Gemini — OpenRouter Anthropic fallback is available but the dead haiku slug (JOSH-10) needs updating
- **Memory retention 2026 trend:** Cross-session recall is the #1 differentiator for personal assistant bots. Heather on Day 79 with zero persistent memory is the most visible weakness in the fleet. Creating MEMORY.md is the single highest-impact GitHub-only action available.
