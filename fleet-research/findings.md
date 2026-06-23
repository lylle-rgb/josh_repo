# Fleet Research Findings — Josh / Heather Schwartz

**Last updated:** 2026-06-23 (evening scan)
**Researcher:** AlphaClaw Fleet Agent
**Instance:** josh_repo (Heather Schwartz — personal assistant)
**Current version:** 2026.3.22
**Safe upgrade target:** **2026.6.9-stable** ✅ Released June 21, 2026 — upgrade window OPEN Day 3 (skip 2026.6.8)
**Previous hold:** 2026.6.6 (hold lifted — 2026.6.9-stable is now out)

> ✅ RESOLVED (June 23): F41 — MEMORY.md day counts updated (was stale 2 days)
> ✅ RESOLVED (June 23): F38 — HEARTBEAT.md cron-not-deployed warning applied
> ✅ RESOLVED (June 22): Finding 37 — TOOLS.md stale "HOLD/STOP" upgrade warning removed
> ✅ RESOLVED (June 21): 2026.6.9-stable shipped — upgrade hold lifted, window is open
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
> 🆕 NEW (June 23 evening): F41 — MEMORY.md day counts were stale 2 days — RESOLVED ✅
> 🆕 NEW (June 23 evening): F38 — HEARTBEAT.md cron-not-deployed warning — RESOLVED ✅
> 🆕 NEW (June 23 evening): F39 — Discord Components V2 post-upgrade (buttons, modals, confirmations)
> 🆕 NEW (June 23 evening): F40 — Group chat context injection on every turn (auto in 2026.6.9)
> 🆕 NEW (June 22 morning): Finding 37 — TOOLS.md stale hold removed (RESOLVED)
> 🆕 NEW (June 22 morning): Finding 36 — dreaming config key path needs verification before applying (LOW)
> 🆕 NEW (June 22 morning): Finding 35 — AlphaClaw in-app update removed, VPS-only upgrade confirmed (INFO)
> 🆕 NEW (June 22 morning): Finding 34 — AlphaClaw git sync reliability fix (POSITIVE — auto-applied)
> 🆕 NEW (June 22 morning): Finding 33 — 2026.6.10-beta.2 auto fast mode (INFO — do not install)
> 🆕 NEW (June 21 morning): Finding 32 — iMessage SQLite migration auto-fix path confirmed (POSITIVE)
> 🆕 NEW (June 21 morning): Finding 31 — same-provider fallback chain gap (MEDIUM)
> 🆕 NEW (June 21 morning): Finding 30 — BRAVE_API_KEY not set, web search disabled (MEDIUM-HIGH)
> ⛔ Still open: Google Workspace OAuth not connected — email/calendar inaccessible (Day 93)
> ⛔ Still open: OpenClaw 93 days outdated (2026.3.22 vs 2026.6.9 safe target)
> ⛔ Still open: heartbeat-state.json all null — Day 8 (cron not deployed to VPS)
> ⛔ Still open: userTimezone not set in openclaw.json (Finding 28)
> ⛔ Still open: Dreaming not enabled in openclaw.json (Finding 22/24)
> ⛔ Still open: compaction/memoryFlush not configured (Finding 4)
> ⛔ Still open: Discord security open to all — groupPolicy: open (Finding 20)
> ⛔ Still open: iMessage paused since ~April 27, 2026 (Day 58 — auto-fix on upgrade, Finding 32)
> ⛔ Still open: Noah session scope broken (noah--repo 404 — Day 12)

---

## ⚠️ Upgrade Status as of June 23 Evening

| Channel | Version | Status |
|---------|---------|--------|
| npm `latest` (stable) | **2026.6.9** | ✅ Current target — upgrade window OPEN (Day 3) |
| 2026.6.10-beta.2 | Released June 22 | 🔬 Beta Day 2 — auto fast mode; DO NOT install |
| 2026.6.8 | Released June 16 | ⛔ Skip — critical regressions, never on npm stable |
| 2026.6.9-stable | June 21, 2026 | ✅ Confirmed stable — no patches |

> **Staged upgrade path (confirmed — skip 2026.6.8):**
> 2026.3.22 → 2026.5.27 → 2026.6.2 → 2026.6.5 → 2026.6.6 → **2026.6.9**
>
> Before upgrading: `npm show openclaw@latest version` must return `2026.6.9`.

---

## ⭐ Finding F41 — MEMORY.md Day Counts Stale (RESOLVED June 23) ✅

MEMORY.md was last updated June 21. Day counts were off by 2: Google Workspace (Day 91→93), Heartbeat null (5 days→8 days), iMessage paused (56→58 days). Applied this scan. Lessons Learned section also refreshed with TOOLS.md/MEMORY.md drift risk, beta track awareness, and cron-not-deployed note.

---

## ⭐ Finding F40 — Group Chat Context: Every Turn Now (Informational)

In OpenClaw 2026.6.x, context in group chats is injected on **every turn**, not just the first. Heather previously could "forget" participant context mid-conversation in Discord server channels. No action required — auto-applied after upgrade to 2026.6.9.

---

## ⭐ Finding F39 — Discord Components V2: Interactive Actions Post-Upgrade

After upgrading to 2026.6.9, Heather gains Discord Components V2: buttons, select menus, modals, and attachment-backed file blocks. Practical use: offer Discord button confirmation before any external action (email send, calendar create, social post). Directly serves Josh's "ask before acting externally" preference in SOUL.md. See Rec 14 in soul-improvements.md. No action until after upgrade.

---

## ⭐ Finding F38 — HEARTBEAT.md Missing Cron-Not-Deployed Warning (RESOLVED June 23) ✅

HEARTBEAT.md described heartbeat check procedures but had no acknowledgment that the cron is not deployed to the VPS. On a fresh session, Heather could wait for triggers that never arrive. Warning block added to top of workspace/HEARTBEAT.md in this commit.

---

## ⭐ Finding 37 — TOOLS.md Stale Upgrade Warning Removed (RESOLVED June 22) ✅

workspace/TOOLS.md contained a stale HOLD banner and [STOP] marker in the staged upgrade path. Both removed in the June 22 morning commit. Current safe target corrected to 2026.6.9-stable, VPS-only upgrade path clarified.

---

## ⭐ Finding 36 — Dreaming Config: Verify Key Path Before Applying

**Priority: LOW — clarifies Finding 22/24**

Dreaming config may live under `plugins.entries.memory-core.config.dreaming` rather than as a top-level `"dreaming"` key, depending on memory-core plugin vs. built-in. Before applying:
```
openclaw config schema | grep -A 10 "dreaming"
```
Finding 22/24 config values (minScore: 0.8, schedule: `"0 3 * * *"`, maxPromotion: 10) are correct regardless of key path.

---

## ⭐ Finding 35 — AlphaClaw In-App OpenClaw Update Removed

AlphaClaw removed the in-app self-update path for VPS deployments. Josh's upgrade **must go through VPS CLI** (`openclaw update`), not the AlphaClaw control UI. No action required — path clarification only.

---

## ⭐ Finding 34 — AlphaClaw Git Sync Reliability Fix (Auto-Applied) ✅

AlphaClaw's hourly git sync now resolves the real git binary at runtime, fixing sync failures in containerized deployments. Josh's hourly workspace backup to `josh_repo` is more reliable. No action required.

---

## ⭐ Finding 33 — OpenClaw 2026.6.10-beta.2: Auto Fast Mode (Do Not Install)

2026.6.10-beta.2 released June 22 with automatic fast mode for short conversational turns. Relevant to Heather — casual Discord exchanges would be noticeably faster. **Do not install.** Stay on 2026.6.9-stable. Monitor for 2026.6.10-stable (~1–2 weeks at current cadence).

---

## ⭐ Finding 32 — iMessage SQLite Migration Will Auto-Fix inbox-state.json (POSITIVE)

OpenClaw 2026.6.1 introduced a storage schema migration that automatically cleans Josh's malformed `inbox-state.json` duplicate key. After upgrade, iMessage monitoring may partially or fully resume. **No action required** beyond running the staged upgrade.

---

## ⭐ Finding 31 — Same-Provider Fallback Chain: Single Google Failure Point

**Priority: MEDIUM**

Current chain: Primary (Google) → Fallback 1 (Google via OpenRouter) → Fallback 2 (Haiku). Two Google endpoints fail together on an outage. Fix — bundle with upgrade session:
```json
"fallbacks": [
  "openrouter/anthropic/claude-haiku-4-5",
  "openrouter/google/gemini-3.5-flash"
]
```
Haiku 4.5 becomes the cross-provider safety net; Gemini Flash stays as second.

---

## ⭐ Finding 30 — BRAVE_API_KEY Not Set: Web Search Disabled

**Priority: MEDIUM-HIGH**

No Brave Search API key configured. Heather cannot autonomously search the web. Fix now — no upgrade needed: AlphaClaw UI → Envars tab → add `BRAVE_API_KEY`. Free tier: 2,000 queries/month at https://api.search.brave.com/app/keys.

---

## ⭐ Finding 29 — 2026.6.9-STABLE: UPGRADE WINDOW OPEN (Day 3)

**Priority: HIGH**

Key 2026.6.9 improvements for Josh/Heather:
- Enhanced agent recovery: retries, session history repair, interrupted turns reach visible result
- Discord Components V2: buttons, select menus, modals (F39)
- Group chat context on every turn (F40)
- Cron reliability: backoff honored, overdue jobs rescheduled on startup
- Claude Haiku 4.5 support for fallback 2
- Auto-thread titles (60s timeout, 4,096-token reasoning budget)
- Discord streaming `"progress"` mode
- SQLite iMessage migration safety check (via 2026.6.6 in staged path)

**Bundle in ONE VPS session:**
1. Add `userTimezone: "America/Los_Angeles"` to `agents.defaults` (Finding 28 — FIRST)
2. Add `compaction/memoryFlush` block (Finding 4)
3. Verify dreaming key path (Finding 36), add dreaming config (Finding 22/24)
4. Add heartbeat cron job to `cron.jobs` (Finding 27)
5. Run staged upgrade: 2026.3.22 → 2026.5.27 → 2026.6.2 → 2026.6.5 → 2026.6.6 → 2026.6.9
6. After 2026.6.9: swap fallback chain (Finding 31)
7. After 2026.6.9: enable Discord streaming `"progress"` mode
8. After 2026.6.9: tighten Discord `allowFrom` (Finding 20)
9. Set BRAVE_API_KEY anytime via AlphaClaw UI (Finding 30)

---

## ⭐ Finding 28 — `userTimezone` Not Set: Silent Timezone Misalignment

**Risk: MEDIUM-HIGH**

VPS is UTC; Josh is in LA (PDT = UTC−7 in June). Without `userTimezone`, heartbeat/dreaming schedules evaluate in UTC — heartbeats go quiet 7 hours early. Add FIRST before any cron/dreaming config:
```json
"agents": { "defaults": { "userTimezone": "America/Los_Angeles" } }
```

---

## ⭐ Finding 27 — Heartbeat State: All Null — Day 8

**Risk: HIGH**

heartbeat-state.json all-null for 8+ consecutive days. Cron was never deployed to VPS (fleet agent created the JSON via GitHub but no cron schedule exists in live openclaw.json). Add with upgrade session:
```json
"cron": {
  "jobs": [{
    "schedule": "0 9 * * *",
    "task": "Read HEARTBEAT.md and run memory maintenance — update heartbeat-state.json after.",
    "channel": "discord:1484448262290276464",
    "description": "Daily MEMORY.md maintenance"
  }]
}
```

---

## ⭐ Finding 26 — 2026.6.8 Regressions (CONFIRMED SKIP)

Discord image tools (#94266), memory-search (#94316), sub-agent tools (#94158), cron isolation, misleading fallback (#94176). Never promoted to npm stable. Jump directly from 2026.6.6 to 2026.6.9.

---

## ⭐ Finding 25 — ClawHavoc: Audit Installed Skills

341 malicious skills planted on ClawHub in January 2026 via typosquatting. Josh's skills directory is empty — no current risk. Run `openclaw skill list` after upgrade to confirm.

---

## ⭐ Finding 24 — Dreaming Config: Use minScore 0.8

Correct dreaming config (add `userTimezone` first; verify key path per Finding 36):
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

## ⭐ Finding 23 — AlphaClaw 0.9.17/0.9.18: New Capabilities

- Per-agent `thinkingDefault`: set in AlphaClaw UI model card
- OpenAI-compatible proxy: toggle in AlphaClaw Setup UI
- Remote MCP: set `REMOTE_MCP_URL` + `REMOTE_MCP_API_TOKEN` in AlphaClaw Envars tab

---

## ⭐ Finding 22 — Dreaming Still Not Enabled (Day 93)

**Risk: HIGH** — without Dreaming, MEMORY.md only updates when fleet agent or Heather manually updates it. Use corrected config from Finding 24; verify key path (Finding 36); add `userTimezone` first (Finding 28).

---

## ⭐ Finding 21 — MEMORY.md Size Monitoring

MEMORY.md now ~6,200 bytes. Monitor growth. Limit: ~20,000 chars before noticeable context budget impact.

---

## ⭐ Finding 20 — Discord Security: Open to All

**Risk: MEDIUM-HIGH**

`groupPolicy: open`, `allowFrom: ["*"]` — anyone in Discord server can query Heather with full personal context. Tighten after upgrade:
```json
"groupPolicy": "allowlist",
"dmPolicy": "allowlist",
"allowFrom": ["JOSH_DISCORD_USER_ID"]
```

---

## ⭐ Finding 4 — No Memory Protection Before Compaction (Day 93)

**Risk: HIGH** — add to openclaw.json:
```json
"compaction": {
  "reserveTokensFloor": 40000,
  "memoryFlush": { "enabled": true, "softThresholdTokens": 4000 }
},
"contextPruning": { "mode": "cache-ttl", "ttl": "6h" }
```

---

## ⭐ Finding 2 — Google Workspace Not Connected (Day 93 — CRITICAL)

No Google OAuth connected. Gmail, Calendar, Contacts all inaccessible. Three of five heartbeat checks permanently blocked.
1. AlphaClaw UI: https://5.78.142.81.sslip.io#general → Google Workspace → OAuth
2. Full steps in workspace/memory/onboarding-google.md
3. Alternative: Remote MCP via AlphaClaw 0.9.18 Envars tab (Finding 23)

---

## Summary Table (Updated June 23 Evening)

| Finding | Priority | Status |
|---------|----------|--------|
| F41. MEMORY.md day counts stale | LOW | ✅ RESOLVED June 23 |
| F38. HEARTBEAT.md cron warning missing | MEDIUM | ✅ RESOLVED June 23 |
| F39. Discord Components V2 post-upgrade | INFO | 🔬 Post-upgrade capability |
| F40. Group chat context every turn | INFO | 🔬 Auto in 2026.6.9 |
| 37. TOOLS.md stale upgrade warning | INFO | ✅ RESOLVED June 22 |
| 34. AlphaClaw git sync fix | POSITIVE | ✅ Auto-applied |
| 32. iMessage SQLite migration auto-fix | POSITIVE | Confirmed — no action needed |
| 33. 2026.6.10-beta.2 auto fast mode | INFO | 🔬 Monitor — do not install |
| 35. AlphaClaw in-app update removed | INFO | VPS-only path confirmed |
| 36. Dreaming config key path | LOW | Verify before applying |
| 31. Same-provider fallback chain gap | MEDIUM | ⏳ Fix with upgrade |
| 30. BRAVE_API_KEY not set | MEDIUM-HIGH | ⏳ Fix anytime (AlphaClaw UI) |
| 29. **2026.6.9-stable — Day 3 of window** | HIGH | ⏳ Upgrade window open |
| 2. Connect Google Workspace | CRITICAL | ⏳ Day 93 |
| 27. Heartbeat cron not deployed — Day 8 | HIGH | ⏳ Bundle with upgrade |
| 28. userTimezone not set | MEDIUM-HIGH | ⏳ Bundle with upgrade |
| 22/24. Enable Dreaming | HIGH | ⏳ Bundle with upgrade |
| 4. Add compaction/memoryFlush | HIGH | ⏳ Bundle with upgrade |
| Upgrade to 2026.6.9 (staged, skip 2026.6.8) | HIGH | ⏳ WINDOW OPEN — Day 3 |
| 20. Discord security (open → allowlist) | MEDIUM-HIGH | ⏳ After upgrade |
| 26. 2026.6.8 skip confirmed | INFO | ✅ Skip confirmed |
| 23. AlphaClaw 0.9.17/18 features | INFO | Available now |
| 25. ClawHavoc skill audit | LOW | No skills installed — safe |
| Noah scope fix (Day 12) | FLEET OPS | ⏳ Day 12 |

---

## Remaining Open Action List (June 23 Evening)

### Can do NOW — AlphaClaw UI only
0. **[MEDIUM-HIGH]** Set BRAVE_API_KEY in AlphaClaw UI → Envars tab (Finding 30)

### Requires Josh — bundle in ONE VPS session
1. **[CRITICAL]** Connect Google Workspace OAuth at https://5.78.142.81.sslip.io#general
2. **[HIGH]** Add `userTimezone: "America/Los_Angeles"` to `agents.defaults` (Finding 28 — FIRST)
3. **[HIGH]** Add `compaction/memoryFlush` block (Finding 4)
4. **[HIGH]** Verify dreaming key path (Finding 36), add dreaming config (Finding 22/24)
5. **[HIGH]** Add heartbeat cron job to `cron.jobs` (Finding 27)
6. **[HIGH]** Run staged upgrade: 2026.3.22 → 2026.5.27 → 2026.6.2 → 2026.6.5 → 2026.6.6 → 2026.6.9
   - Verify first: `npm show openclaw@latest version` = `2026.6.9`

### After upgrade to 2026.6.9
7. **[MEDIUM]** Swap fallback chain (Finding 31): Haiku 4.5 → Fallback 1, Gemini Flash → Fallback 2
8. **[MEDIUM-HIGH]** Tighten Discord allowFrom: `["*"]` → Josh's Discord user ID (Finding 20)
9. **[LOW]** Enable Discord streaming: `"streaming": "progress"`
10. **[LOW]** Enable auto-thread titles
11. **[LOW]** Add Discord Components V2 behavioral guidance to SOUL.md + AGENTS.md (Rec 14, F39)

### AlphaClaw UI
12. **[LOW]** Set per-agent `thinkingDefault` from model card (Finding 23)

### Fleet operations
13. **[FLEET OPS]** Fix Noah session scope: noah--repo (404) → Noah-workspace or Noahrepo2 (Day 12)

---

*Sources: [OpenClaw GitHub Releases](https://github.com/openclaw/openclaw/releases) · [OpenClaw Memory Docs](https://docs.openclaw.ai/concepts/memory) · [Brave Search API](https://brave.com/search/api/) · [OpenClaw Cron Docs](https://docs.openclaw.ai/automation/cron-jobs) · [AlphaClaw GitHub](https://github.com/chrysb/alphaclaw)*
