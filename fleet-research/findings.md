# Fleet Research Findings — Josh / Heather Schwartz

**Last updated:** 2026-06-20 (morning scan)
**Researcher:** AlphaClaw Fleet Agent
**Instance:** josh_repo (Heather Schwartz — personal assistant)
**Current version:** 2026.3.22
**Safe upgrade target:** 2026.6.6 (npm `latest` stable as of June 20 morning)
**Next target:** 2026.6.9-stable (not yet shipped — monitor nightly)

> ✅ RESOLVED (June 17): workspace/SOUL.md — personalized with Josh's hard rules
> ✅ RESOLVED (June 17): workspace/AGENTS.md — personalized with emoji override at top
> ✅ RESOLVED (June 17): workspace/TOOLS.md — populated with AlphaClaw UI, Discord, iMessage, models
> ✅ RESOLVED (June 17): workspace/USER.md — filled with Josh's profile
> ✅ RESOLVED (June 17): workspace/BOOTSTRAP.md — deleted (no longer burning context tokens)
> ✅ RESOLVED (June 17): memory/heartbeat-state.json — created
> ✅ RESOLVED (June 16): workspace/MEMORY.md — created and seeded
> ✅ RESOLVED (June 16): workspace/HEARTBEAT.md — populated with active monitoring schedule
> ✅ RESOLVED (June 16): gemini-2.5-flash → gemini-3.5-flash in openclaw.json
> ✅ RESOLVED (June 19): TOOLS.md + MEMORY.md — upgrade target corrected (2026.6.8 has regressions)
> ⛔ Still open: Google Workspace OAuth not connected — email/calendar inaccessible (Day 90)
> ⛔ Still open: OpenClaw 90+ days outdated (2026.3.22 vs 2026.6.6 safe target)
> ⛔ Still open: heartbeat-state.json all null — Day 4 (cron likely not deployed to VPS)
> ⛔ Still open: userTimezone not set in openclaw.json (NEW — Finding 28)
> ⛔ Still open: Dreaming not enabled in openclaw.json
> ⛔ Still open: compaction/memoryFlush not configured in openclaw.json
> ⛔ Still open: Discord security open to all (groupPolicy: open)
> ⛔ Still open: iMessage paused since ~April 27, 2026 (Day 54)
> ⛔ Still open: Noah session scope broken (noah--repo 404 — should be Noah-workspace)

---

## ⚠️ Upgrade Status as of June 20 Morning

| Channel | Version | Status |
|---------|---------|--------|
| npm `latest` (stable) | **2026.6.6** | Current safe target |
| 2026.6.8 | Released June 16 | ⛔ GitHub "Latest" badge but NOT on npm stable — critical regressions (see Finding 26) |
| 2026.6.9-beta.1 | June 19 | Pre-release — do not use in production |
| 2026.6.9-stable | **Not yet shipped** | Could arrive today/tomorrow — watching |

> **Note on GitHub "Latest" vs npm `latest`:** GitHub marks the most recent tag as "Latest" in its UI, even if it's a buggy release. npm's `latest` tag is the authoritative stable channel. Some web sources citing 2026.6.8 as production-ready are reading the GitHub badge, not the npm channel. npm `latest` = 2026.6.6. Hold stands.

**Staged upgrade path:** 2026.3.22 → 2026.5.27 → 2026.6.2 → 2026.6.5 → 2026.6.6 → **[STOP — wait for 2026.6.9-stable]**

---

## ⭐ Finding 28 — `userTimezone` Not Set: Silent Timezone Misalignment Risk (NEW — June 20 Morning)

**Risk: MEDIUM-HIGH — will silently break heartbeat/dreaming schedules once activeHours is configured**

openclaw.json has no `userTimezone` configured. Per OpenClaw docs and GitHub Issue #67397:

> "If `userTimezone` is unset, OpenClaw falls back to the host machine's timezone. Timezone mismatches between `userTimezone` and `activeHours` cause silent heartbeat suppression."

Josh's VPS (5.78.142.81) is almost certainly UTC. Josh is in Los Angeles (PDT = UTC−7 in June). Without `userTimezone`:
- Any `heartbeat.activeHours` config (e.g., quiet from 23:00–08:00 LA time) evaluates in UTC
- UTC 23:00 = 4:00 PM PDT — heartbeats would go quiet 7 hours early
- The recommended dreaming schedule `"0 3 * * *"` (3 AM UTC = 8 PM PDT) is safe by coincidence — but fragile

**Fix (one line in `agents.defaults`):**
```json
"agents": {
  "defaults": {
    "userTimezone": "America/Los_Angeles",
    "model": { ... }
  }
}
```

**Add `userTimezone` FIRST, before adding heartbeat activeHours or dreaming schedule to openclaw.json.**

**Related — Bug #67397 (filed April 15, 2026):**
Dreaming cron is gated by `heartbeat.activeHours` with no independent override. If a dreaming job's time falls outside the active window (as evaluated in the configured timezone), it is silently skipped with `reason=quiet-hours`. The fix (separate `dreaming.activeHours`) is not yet shipped in any stable release. Until it is: keep dreaming schedule inside the active window, and anchor it with the correct `userTimezone`.

---

## ⭐ Finding 27 — Heartbeat State: All Null — Day 4 (NEW — June 20 Evening)

**Risk: HIGH — Heather has had zero confirmed proactive monitoring since deployment**

heartbeat-state.json has shown all null timestamps for 4 consecutive days (June 17–20). The most likely cause is that the heartbeat cron was never deployed to the VPS — the fleet agent created the JSON file via GitHub but no cron schedule exists in the live openclaw.json.

`memory_maintenance` has no dependencies on Google Workspace or iMessage, yet it is also null. This rules out a blocker-only explanation and strongly implies the cron itself never fired.

**Immediate action:**
1. Ask Heather directly in Discord: "Are you running heartbeat checks?"
2. After OpenClaw upgrade + adding `userTimezone`, add heartbeat cron to openclaw.json:
```json
"cron": {
  "jobs": [
    {
      "schedule": "0 9 * * *",
      "task": "Read HEARTBEAT.md and run memory maintenance — update heartbeat-state.json after.",
      "channel": "discord:1484448262290276464",
      "description": "Daily MEMORY.md maintenance"
    }
  ]
}
```

---

## ⭐ Finding 26 — 2026.6.8 Critical Regressions (Corrected June 19 — Previously Marked Stable)

**Risk: HIGH — do NOT upgrade to 2026.6.8**

The June 18 morning scan incorrectly marked 2026.6.8 as stable. Corrected June 19. Known regressions:
- Discord image-tool failure (#94266)
- Memory-search provider breakage (#94316)
- Sub-agent tools broken (#94158)
- Cron isolation regressions (hot-reload persistence races, isolated watchdog aborts)
- Misleading terminal-turn fallback (#94176)

npm `latest` still points to 2026.6.6. 2026.6.8 was never promoted to npm stable channel.

---

## ⭐ Finding 25 — ClawHavoc: Audit Installed Skills

**Risk: LOW for Josh (no skills installed)**

341 malicious skills were planted on ClawHub in January 2026 via typosquatting. Josh's skills directory is empty — no current risk. Run `openclaw skill list` after upgrade to confirm.

---

## ⭐ Finding 24 — Dreaming Config: Use minScore 0.8, Not 0.7

**Risk: LOW (corrects Finding 22 before application)**

Correct dreaming config for openclaw.json (add `userTimezone` first per Finding 28):
```json
"dreaming": {
  "enabled": true,
  "schedule": "0 3 * * *",
  "maxPromotion": 10,
  "minScore": 0.8,
  "minRecallCount": 3,
  "minUniqueQueries": 3
}
```
3 AM UTC = 8 PM PDT (June). Safe inside Josh's active window once `userTimezone: "America/Los_Angeles"` is set.

---

## ⭐ Finding 23 — AlphaClaw 0.9.17/0.9.18: Per-Agent Thinking, OpenAI Proxy, Remote MCP

**Risk: LOW (new capabilities available now)**

- Per-agent `thinkingDefault`: set in AlphaClaw UI → each agent's model card
- OpenAI-compatible proxy: toggle in AlphaClaw Setup UI
- Remote MCP: set `REMOTE_MCP_URL` + `REMOTE_MCP_API_TOKEN` in AlphaClaw Envars tab — alternative Google Workspace path

---

## ⭐ Finding 22 — Dreaming Still Not Enabled (Day 90)

**Risk: HIGH — use corrected config from Finding 24; add `userTimezone` first (Finding 28)**

Without Dreaming, MEMORY.md only updates when the fleet agent pushes changes or Heather does it manually. Add corrected config from Finding 24 to openclaw.json when upgrading. Set `userTimezone` first.

---

## ⭐ Finding 21 — MEMORY.md Size Monitoring

**Risk: LOW (proactive hygiene)**

MEMORY.md is now ~5,000 bytes. Monitor growth as Heather learns more. Limit: 20,000 chars.

---

## ⭐ Finding 20 — Discord Security: Open to All

**Risk: MEDIUM-HIGH**

Current config: `groupPolicy: open`, `allowFrom: ["*"]`. Anyone in Josh's Discord server can query Heather with full personal context.

```json
"groupPolicy": "allowlist",
"dmPolicy": "allowlist",
"allowFrom": ["JOSH_DISCORD_USER_ID"]
```

---

## ⭐ Finding 4 — No Memory Protection Before Compaction (Day 90)

**Risk: HIGH**

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
}
```

---

## ⭐ Finding 2 — Google Workspace Not Connected (Day 90 — CRITICAL)

**Risk: CRITICAL — top Josh action item**

No Google OAuth connected. Gmail, Calendar, Contacts all inaccessible. Three of five heartbeat checks permanently blocked.

1. AlphaClaw UI: https://5.78.142.81.sslip.io#general
2. Google Workspace → provide OAuth credentials
3. Full steps in workspace/memory/onboarding-google.md
4. Alternative: Remote MCP via AlphaClaw 0.9.18 Envars tab (Finding 23)

---

## Summary Table (Updated June 20 Morning)

| Finding | Priority | Status |
|---------|----------|--------|
| 2. Connect Google Workspace | CRITICAL | ⏳ Day 90 |
| 27. Heartbeat cron not deployed — Day 4 null | HIGH | ⏳ Day 4 |
| 28. userTimezone not set — silent TZ risk | MEDIUM-HIGH | 🆕 New June 20 morning |
| 22/24. Enable Dreaming (corrected config) | HIGH | ⏳ Day 90 |
| 4. Add compaction/memoryFlush | HIGH | ⏳ Day 90 |
| Upgrade to 2026.6.6 (staged) | HIGH | ⏳ Day 90 |
| 20. Discord security (open → allowlist) | MEDIUM-HIGH | ⏳ Day 90 |
| 26. 2026.6.8 regressions — hold at 2026.6.6 | INFO — CRITICAL | ✅ Corrected June 19 |
| 23. AlphaClaw 0.9.17/18 new features | INFO | Available now |
| 25. ClawHavoc skill audit | LOW | No skills installed |
| Noah scope fix (noah--repo → Noah-workspace) | FLEET OPS | ⏳ Day 8 |

---

## Remaining Open Action List (June 20 Morning)

### Requires Josh action (VPS or AlphaClaw UI)
1. **[CRITICAL]** Connect Google Workspace OAuth at https://5.78.142.81.sslip.io#general
2. **[HIGH]** Ask Heather in Discord: "Are you running heartbeat checks?"
3. **[HIGH]** When 2026.6.9-stable ships: upgrade via `openclaw update` (staged path, stop at 2026.6.6 first)
4. **[HIGH]** Add to openclaw.json — bundle in one VPS session:
   - `userTimezone: "America/Los_Angeles"` (Finding 28 — do this first)
   - `compaction/memoryFlush` (Finding 4)
   - `dreaming` with `minScore: 0.8` (Finding 22/24)
   - heartbeat cron job with Discord channel delivery (Finding 27)
5. **[MEDIUM-HIGH]** Tighten Discord allowFrom: replace `"*"` with Josh's Discord user ID (Finding 20)

### AlphaClaw UI (no VPS CLI needed)
6. **[LOW]** Set per-agent `thinkingDefault` from model card (Finding 23)
7. **[LOW]** Enable OpenAI proxy toggle if integrations need it (Finding 23)

### After upgrade to 2026.6.9-stable
8. **[LOW]** Upgrade fallback 2: `claude-3.5-haiku` → `claude-haiku-4-5`
9. **[LOW]** Enable Discord streaming `"progress"` mode

### Fleet operations
10. **[FLEET OPS]** Fix Noah session scope: noah--repo → Noah-workspace

---

*Sources: [OpenClaw GitHub Releases](https://github.com/openclaw/openclaw/releases) · [Bug #67397 — Dreaming gated by activeHours](https://github.com/openclaw/openclaw/issues/67397) · [OpenClaw Heartbeat Docs](https://docs.openclaw.ai/gateway/heartbeat) · [OpenClaw Cron Docs](https://docs.openclaw.ai/automation/cron-jobs) · [AlphaClaw GitHub](https://github.com/chrysb/alphaclaw)*
