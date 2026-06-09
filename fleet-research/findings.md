# Fleet Research — Morning Scan Findings
**Instance:** Heather Schwartz (Josh — personal assistant)
**Scan date:** 2026-06-09 (morning)
**Scanner:** AlphaClaw Fleet Agent (automated morning scan)
**Previous scan:** 2026-06-09 evening (see `2026-06-09-evening-findings.md`)

---

## Summary

All prior open findings remain unresolved. This morning scan adds 3 new findings from web research.

Most urgent issues unchanged:
- **MEMORY.md missing — Day 79** (CRITICAL, GitHub-only)
- **HEARTBEAT.md empty — Day 79** (HIGH, GitHub-only)
- **Google Workspace not connected** (CRITICAL, VPS/setup)
- **OpenClaw 79 days behind stable (2026.6.2)** (HIGH, VPS upgrade)

**Version note:** npm stable is now **2026.6.2** — advanced from 2026.5.28. The 2026.6.5-beta.5 track shipped June 8 with QQBot reasoning tag stripping, Parallel web search, MCP tool result coercion, and SQLite state storage.

---

## NEW FINDINGS (Morning Scan — June 9)

### New Finding A — Cron jobs-state.json Isolation (2026.4.20+) — Foundation for Email/Calendar Scheduling
**Severity:** MEDIUM
**What was found:** OpenClaw 2026.4.20 isolated cron runtime state into `jobs-state.json`, separate from main session state. Cron jobs now survive VPS restarts without state corruption. `openclaw cron run --wait` gains timeout + poll-interval controls. This improvement is included in the 2026.6.2 upgrade target.

**Why it matters:** Heather's email/calendar morning digest should be built as a cron job (exact 8 AM timing, not heartbeat drift). The cron reliability improvements make scheduled tasks crash-resilient — critical for a personal assistant whose user wakes up expecting a briefing.

**Exact steps to implement (after upgrading to 2026.6.2):**
Add to `openclaw.json`:
```json
"cron": {
  "jobs": {
    "morning-digest": {
      "enabled": true,
      "schedule": "0 8 * * *",
      "prompt": "Check Josh's Gmail for urgent unread messages. Check today's calendar events. Deliver a concise morning briefing to Josh via Discord.",
      "channel": "discord",
      "model": "google/gemini-3-flash-preview"
    }
  }
}
```

**Risk level:** LOW — informational now; applies post-upgrade

---

### New Finding B — QQBot Reasoning Tag Stripping (2026.6.5-beta) — Discord Output Safety
**Severity:** LOW
**What was found:** OpenClaw 2026.6.5-beta.5 strips model reasoning/thinking scaffolding (`<thinking>` tags) before Discord channel delivery.

**Why it matters:** Heather uses Gemini (no reasoning tags by default). However, the OpenRouter Anthropic fallback could produce reasoning tags if extended thinking is activated. This protection ensures clean Discord output. Track for next upgrade cycle after 2026.6.5-stable ships.

**Risk level:** N/A — informational

---

### New Finding C — Operator Install Policy (2026.6.2) — Safer Skill Installs
**Severity:** LOW
**What was found:** OpenClaw 2026.6.2 replaced the dangerous-code scanner path with an operator install policy — explicit authorization model, better doctor/CLI integration, clearer install surfaces.

**Why it matters:** When gog-cli (Google Workspace, JOSH-44) and the BlueBubbles iMessage skill are installed post-upgrade, they use the new policy-based path — fewer false-positive rejections, more predictable install flow.

**Risk level:** N/A — benefit applies automatically at 2026.6.2 upgrade

---

## Open Findings (Carried Over — Full Detail in 2026-06-09-evening-findings.md)

| # | Severity | Finding | Days Open |
|---|---|---|---|
| JOSH-30 | **CRITICAL** | MEMORY.md never created | **79** |
| JOSH-44 | **CRITICAL** | Google Workspace not connected | 6 |
| JOSH-31 | HIGH | HEARTBEAT.md empty — no proactive monitoring | 79 |
| JOSH-47 | HIGH | Dreaming blocked (needs upgrade + MEMORY.md) | 6 |
| JOSH-29/48 | HIGH | Platform 79 days behind stable 2026.6.2 | **79** |
| JOSH-55 | MEDIUM | TOOLS.md template-only | 1 |
| JOSH-37 | MEDIUM | SOUL.md not personalized | 79 |
| JOSH-33/45 | MEDIUM | iMessage paused + malformed state | 43 |
| JOSH-54 | LOW | BOOTSTRAP.md not deleted | 1 |
| JOSH-34 | LOW | Emoji contradiction in AGENTS.md vs USER.md | 79 |

---

## Research Sources

- [OpenClaw Releases · GitHub](https://github.com/openclaw/openclaw/releases)
- [OpenClaw CHANGELOG](https://github.com/openclaw/openclaw/blob/main/CHANGELOG.md)
- [OpenClaw Release Notes (Releasebot)](https://releasebot.io/updates/openclaw)
- [OpenClaw Cron Jobs Docs](https://docs.openclaw.ai/automation/cron-jobs)
- [OpenClaw 2026.6.5-beta Notes (OpenClaw Playbook)](https://www.openclawplaybook.ai/blog/openclaw-2026-4-9-release-dreaming-security-hardening/)
- [AlphaClaw Apex 0.9.18 (Chrys Bader, X)](https://x.com/chrysb/status/2035479976074760664)
- [OpenClaw Community Tips (AI Edge, X)](https://x.com/aiedge_/status/2025163629080051989)
- [AlphaClaw Apex (Product Hunt)](https://www.producthunt.com/products/alphaclaw-apex)
