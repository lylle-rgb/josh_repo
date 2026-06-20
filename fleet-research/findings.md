# Fleet Research Findings — Josh / Heather Schwartz

**Last updated:** 2026-06-20 (evening scan)
**Researcher:** AlphaClaw Fleet Agent
**Instance:** josh_repo (Heather Schwartz — personal assistant)
**Current version:** 2026.3.22
**Safe upgrade target:** 2026.6.6 (npm `latest` stable as of June 20)
**Next target:** 2026.6.9-stable (not yet shipped — monitor nightly)

> ✅ RESOLVED (June 17): workspace/SOUL.md — personalized with Josh's hard rules
> ✅ RESOLVED (June 17): workspace/AGENTS.md — personalized with emoji override at top
> ✅ RESOLVED (June 17): workspace/TOOLS.md — populated with AlphaClaw UI, Discord, iMessage, models
> ✅ RESOLVED (June 17): workspace/BOOTSTRAP.md — deleted (no longer burning context tokens)
> ✅ RESOLVED (June 17): memory/heartbeat-state.json — created
> ✅ RESOLVED (June 16): workspace/MEMORY.md — created and seeded
> ✅ RESOLVED (June 16): workspace/HEARTBEAT.md — populated with active monitoring schedule
> ✅ RESOLVED (June 16): gemini-2.5-flash → gemini-3.5-flash in openclaw.json
> ✅ RESOLVED (June 19): TOOLS.md + MEMORY.md — upgrade target corrected (2026.6.8 has regressions)
> ⛔ Still open: Google Workspace OAuth not connected — email/calendar inaccessible (Day 90)
> ⛔ Still open: OpenClaw 90+ days outdated (2026.3.22 vs 2026.6.6 safe target)
> ⛔ Still open: heartbeat-state.json all null — Day 4 (cron likely not deployed to VPS)
> ⛔ Still open: Dreaming not enabled in openclaw.json
> ⛔ Still open: compaction/memoryFlush not configured in openclaw.json
> ⛔ Still open: Discord security open to all (groupPolicy: open)
> ⛔ Still open: iMessage paused since ~April 27, 2026 (Day 54)
> ⛔ Still open: Noah session scope broken (noah--repo 404 — should be Noah-workspace)

---

## ⚠️ Upgrade Status as of June 20 Evening

| Channel | Version | Status |
|---------|---------|--------|
| npm `latest` (stable) | **2026.6.6** | Current safe target |
| 2026.6.8 | Released June 16 | ⛔ NOT on npm latest — critical regressions (see Finding 27) |
| 2026.6.9-beta.1 | June 19 | Pre-release — do not use in production |
| 2026.6.9-stable | **Not yet shipped** | Watching daily |

**Staged upgrade path:** 2026.3.22 → 2026.5.27 → 2026.6.2 → 2026.6.5 → 2026.6.6 → **[STOP — wait for 2026.6.9-stable]**

---

## ⭐ Finding 27 — Heartbeat State: All Null — Day 4 (NEW — June 20 Evening)

**Risk: HIGH — Heather has had zero confirmed proactive monitoring since deployment**

heartbeat-state.json has shown all null timestamps for 4 consecutive days (June 17–20). The most likely cause is that the heartbeat cron was never deployed to the VPS — the fleet agent created the JSON file via GitHub but no cron schedule exists in the live openclaw.json.

`memory_maintenance` has no dependencies on Google Workspace or iMessage, yet it is also null. This rules out a blocker-only explanation and strongly implies the cron itself never fired.

**Immediate action:**
1. Ask Heather directly in Discord: "Are you running heartbeat checks?"
2. After OpenClaw upgrade, add cron to openclaw.json:
```json
{
  "schedule": "0 9 * * *",
  "task": "heartbeat_memory_maintenance",
  "description": "Daily MEMORY.md maintenance — no Google or iMessage required"
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

npm `latest` still points to 2026.6.6. 2026.6.8 was never promoted to stable channel.

---

## ⭐ Finding 25 — ClawHavoc: Audit Installed Skills

**Risk: LOW for Josh (no skills installed)**

341 malicious skills were planted on ClawHub in January 2026 via typosquatting. Josh's skills directory is empty — no current risk. Run `openclaw skill list` after upgrade to confirm.

---

## ⭐ Finding 24 — Dreaming Config: Use minScore 0.8, Not 0.7

**Risk: LOW (corrects Finding 22 before application)**

Correct dreaming config for openclaw.json:
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

---

## ⭐ Finding 23 — AlphaClaw 0.9.17/0.9.18: Per-Agent Thinking, OpenAI Proxy, Remote MCP

**Risk: LOW (new capabilities available now)**

- Per-agent `thinkingDefault`: set in AlphaClaw UI → each agent's model card
- OpenAI-compatible proxy: toggle in AlphaClaw Setup UI
- Remote MCP: set `REMOTE_MCP_URL` + `REMOTE_MCP_API_TOKEN` in AlphaClaw Envars tab — alternative Google Workspace path

---

## ⭐ Finding 22 — Dreaming Still Not Enabled (Day 90)

**Risk: HIGH — use corrected config from Finding 24 (minScore: 0.8)**

Without Dreaming, MEMORY.md only updates when the fleet agent pushes changes or Heather does it manually. Add corrected config from Finding 24 to openclaw.json when upgrading.

---

## ⭐ Finding 21 — MEMORY.md Size Monitoring

**Risk: LOW (proactive hygiene)**

MEMORY.md is now ~4,800 bytes (well within 20,000 char limit). Monitor growth as Heather learns more.

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

## ⭐ Finding 19 — 2026.6.8 Now the Safe Target (SUPERSEDED by Finding 26)

Superseded. See Finding 26. Safe target is 2026.6.6 until 2026.6.9-stable ships.

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

## Summary Table (Updated June 20 Evening)

| Finding | Priority | Status |
|---------|----------|--------|
| 2. Connect Google Workspace | CRITICAL | ⏳ Day 90 |
| 27. Heartbeat cron not deployed — Day 4 null | HIGH | 🆕 New June 20 |
| 22/24. Enable Dreaming (corrected config) | HIGH | ⏳ Day 90 |
| 4. Add compaction/memoryFlush | HIGH | ⏳ Day 90 |
| Upgrade to 2026.6.6 (staged) | HIGH | ⏳ Day 90 |
| 20. Discord security (open → allowlist) | MEDIUM-HIGH | ⏳ Day 90 |
| 26. 2026.6.8 regressions — hold at 2026.6.6 | INFO — CRITICAL | ✅ Corrected June 19 |
| 23. AlphaClaw 0.9.17/18 new features | INFO | 🆕 Available now |
| 25. ClawHavoc skill audit | LOW | ⏳ No skills installed |
| Noah scope fix (noah--repo → Noah-workspace) | FLEET OPS | ⏳ Day 90+ |

---

## Remaining Open Action List (June 20 Evening)

### Requires Josh action (VPS or AlphaClaw UI)
1. **[CRITICAL]** Connect Google Workspace OAuth at https://5.78.142.81.sslip.io#general
2. **[HIGH]** Ask Heather in Discord: "Are you running heartbeat checks?"
3. **[HIGH]** When 2026.6.9-stable ships: upgrade via `openclaw update` (staged path, stop at 2026.6.6 first)
4. **[HIGH]** Add to openclaw.json: compaction/memoryFlush + Dreaming (corrected config: minScore 0.8)
5. **[MEDIUM]** Add heartbeat memory_maintenance cron to openclaw.json
6. **[MEDIUM-HIGH]** Tighten Discord allowFrom: replace `"*"` with Josh's Discord user ID
7. **[LOW]** Enable Discord streaming `"progress"` mode
8. **[LOW]** Run `openclaw skill list` and audit installed skills

### AlphaClaw UI (no VPS CLI needed)
9. **[LOW]** Set per-agent `thinkingDefault` from model card (Finding 23)
10. **[LOW]** Enable OpenAI proxy toggle if integrations need it (Finding 23)

### After upgrade to 2026.6.9-stable
11. **[LOW]** Upgrade fallback 2: `claude-3.5-haiku` → `claude-haiku-4-5`

### Fleet operations
12. **[FLEET OPS]** Fix Noah session scope: noah--repo → Noah-workspace

---

*Sources: [OpenClaw GitHub Releases](https://github.com/openclaw/openclaw/releases), [OpenClaw Dreaming Docs](https://docs.openclaw.ai/concepts/dreaming), [AlphaClaw GitHub](https://github.com/chrysb/alphaclaw)*
