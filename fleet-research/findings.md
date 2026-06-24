# Fleet Research Findings — Josh / Heather Schwartz

**Last updated:** 2026-06-24 (evening scan — F43 added)
**Researcher:** AlphaClaw Fleet Agent
**Instance:** josh_repo (Heather Schwartz — personal assistant)
**Current version:** 2026.3.22
**Safe upgrade target:** **2026.6.9-stable** ✅ Released June 21, 2026 — upgrade window OPEN Day 5 (skip 2026.6.8)
**Previous hold:** 2026.6.6 (hold lifted — 2026.6.9-stable is now out)

> ⚠️ CRITICAL (June 24 eve): F43 — gemini-3.1-flash-image-preview + gemini-3-pro-image-preview shut down TOMORROW June 25 — primary model `gemini-3-flash-preview` is same family — check deprecation page TONIGHT
> ✅ RESOLVED (June 23 eve): F41 — MEMORY.md day counts updated (was stale 2 days)
> ✅ RESOLVED (June 23 eve): F38 — HEARTBEAT.md cron-not-deployed warning applied
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
> 🆕 NEW (June 24 eve): F43 — Gemini preview SISTER MODELS shut down TOMORROW June 25 (CRITICAL)
> 🆕 NEW (June 24 eve): F44 — Noah session scope broken — Day 14 (FLEET OPS)
> 🆕 NEW (June 24 eve): F45 — SkillSpector now standard on all ClawHub installs (INFO/POSITIVE)
> 🆕 NEW (June 24 eve): F46 — 2026.6.10-beta.2 auto fast mode Day 4 — do not install (INFO)
> 🆕 NEW (June 23 morning): F42 — Gemini preview sunset wave — ESCALATED → see F43 (CRITICAL)
> 🆕 NEW (June 23 eve): F41 — MEMORY.md day counts were stale 2 days — RESOLVED ✅
> 🆕 NEW (June 23 eve): F38 — HEARTBEAT.md cron-not-deployed warning — RESOLVED ✅
> 🆕 NEW (June 23 eve): F39 — Discord Components V2 post-upgrade (buttons, modals, confirmations)
> 🆕 NEW (June 23 eve): F40 — Group chat context injection on every turn (auto in 2026.6.9)
> 🆕 NEW (June 22 morning): Finding 37 — TOOLS.md stale hold removed (RESOLVED)
> 🆕 NEW (June 22 morning): Finding 36 — dreaming config key path needs verification before applying (LOW)
> 🆕 NEW (June 22 morning): Finding 35 — AlphaClaw in-app update removed, VPS-only upgrade confirmed (INFO)
> 🆕 NEW (June 22 morning): Finding 34 — AlphaClaw git sync reliability fix (POSITIVE — auto-applied)
> 🆕 NEW (June 22 morning): Finding 33 — 2026.6.10-beta.2 auto fast mode (INFO — do not install) [→ see F46]
> 🆕 NEW (June 21 morning): Finding 32 — iMessage SQLite migration auto-fix path confirmed (POSITIVE)
> 🆕 NEW (June 21 morning): Finding 31 — same-provider fallback chain gap (MEDIUM)
> 🆕 NEW (June 21 morning): Finding 30 — BRAVE_API_KEY not set, web search disabled (MEDIUM-HIGH)
> ⛔ Still open: Google Workspace OAuth not connected — email/calendar inaccessible (Day 95)
> ⛔ Still open: OpenClaw 95 days outdated (2026.3.22 vs 2026.6.9 safe target)
> ⛔ Still open: heartbeat-state.json all null — Day 10 (cron not deployed to VPS)
> ⛔ Still open: userTimezone not set in openclaw.json (Finding 28)
> ⛔ Still open: Dreaming not enabled in openclaw.json (Finding 22/24)
> ⛔ Still open: compaction/memoryFlush not configured (Finding 4)
> ⛔ Still open: Discord security open to all — groupPolicy: open (Finding 20)
> ⛔ Still open: iMessage paused since ~April 27, 2026 (Day 60 — auto-fix on upgrade, Finding 32)
> ⛔ Still open: Noah session scope broken (noah--repo 404 — Day 14)

---

## ⚠️ Upgrade Status as of June 24 Evening

| Channel | Version | Status |
|---------|---------|--------|
| npm `latest` (stable) | **2026.6.9** | ✅ Current target — upgrade window OPEN (Day 5) |
| 2026.6.10-beta.2 | Released June 22 | 🔬 Beta Day 4 — auto fast mode; DO NOT install |
| 2026.6.8 | Released June 16 | ⛔ Skip — critical regressions, never on npm stable |
| 2026.6.9-stable | June 21, 2026 | ✅ Confirmed stable 5 days — no patches |

> **Staged upgrade path (confirmed — skip 2026.6.8):**
> 2026.3.22 → 2026.5.27 → 2026.6.2 → 2026.6.5 → 2026.6.6 → **2026.6.9**
>
> Before upgrading: `npm show openclaw@latest version` must return `2026.6.9`.

---

## ⭐ Finding F43 — ESCALATED: Gemini Preview Sister Models Shut Down TOMORROW ⚠️⚠️

**Priority: CRITICAL — Added June 24 Evening (escalation of F42)**

Two Gemini preview models are confirmed shutting down **June 25, 2026 — TOMORROW**:
- `gemini-3.1-flash-image-preview` → SHUTDOWN JUNE 25
- `gemini-3-pro-image-preview` → SHUTDOWN JUNE 25

Josh's primary model is `google/gemini-3-flash-preview` — same naming family, same generation.
No confirmed shutdown date for this specific model ID, but two sister models shut down tomorrow.

**What happens if primary shuts down:**
- Heather silently fails to Fallback 1 (openrouter/google/gemini-3.5-flash — acceptable quality)
- Then Fallback 2 (openrouter/anthropic/claude-3.5-haiku — acceptable)
- Josh gets **no notification** — no primary-failure alert in current config
- Performance degrades silently until Josh or fleet agent notices

**Action — TONIGHT or FIRST THING June 25:**
1. Check https://ai.google.dev/gemini-api/docs/deprecations — search for `gemini-3-flash-preview`
2. If NOT listed: no immediate action; migration to `gemini-3.5-flash` still recommended (see F42)
3. If listed: migrate primary IMMEDIATELY — no upgrade needed, edit openclaw.json:
```json
"model": {
  "primary": "google/gemini-3.5-flash",
  "fallbacks": [
    "openrouter/anthropic/claude-haiku-4-5",
    "openrouter/google/gemini-3.5-flash"
  ]
}
```

**This can be done NOW via AlphaClaw UI — no VPS upgrade needed:**
Browse tab → `.openclaw/workspace/../openclaw.json` → edit model block → save → gateway restart.
This single edit also resolves F42 (Gemini sunset) and F31 (same-provider fallback gap).

**Risk level:** CRITICAL. Sister model shutdown confirmed for tomorrow. Check deprecation page tonight.

---

## ⭐ Finding F44 — Noah Session Scope: Broken — Day 14

**Priority: FLEET OPS — Ongoing since June 11**

The configured Noah repo (`lylle-rgb/noah--repo`) returns 404. Evening scan June 24 confirms ongoing.

**Actual Noah repos found:**
- `lylle-rgb/Noahrepo2` — last updated 2026-03-08 (private, more recent)
- `lylle-rgb/Noah-workspace` — last updated 2026-03-07 (private)

Session scope prevents accessing either. Fleet agent cannot review Noah's workspace files.

**Why this matters:**
- Noah (Market Catalyst Agent) handles Alpaca paper trading, SEC filings, market data
- Last known git sync: March 2026 — Noah is ~107 days without fleet research
- ClawHavoc attack specifically targeted trading skills — Noah is highest-risk profile in fleet
- Without fleet coverage, Noah's OpenClaw version, model config, and installed skills are unknown

**Action:**
1. **Fleet admin:** Update session scope to `lylle-rgb/Noahrepo2` (most recently updated)
2. On next scan after scope fix: full Noah workspace audit — SOUL.md, MEMORY.md, AGENTS.md, TOOLS.md, openclaw.json, installed skills, cron/heartbeat status

---

## ⭐ Finding F45 — SkillSpector Now Standard on All ClawHub Installs (POSITIVE)

**Priority: INFO/POSITIVE — Added June 24 Evening**

All ClawHub skills now ship with:
- A **Skill Card** documenting purpose, origin, and permissions requested
- **SkillSpector scan results** for hidden instructions and agentic risks
- Opt-in auto mode for Enterprise-ready host exec guardrails

**Why it matters for Josh:** When Josh eventually installs skills post-upgrade, each skill includes a
verified safety scan. Directly reduces ClawHavoc-style supply chain attack risk (Finding 25).
No action required — automatic in 2026.6.9+.

Still run `openclaw skill list` post-upgrade to confirm no skills installed during upgrade path (Finding 25).

---

## ⭐ Finding F46 — 2026.6.10-beta.2: Auto Fast Mode (Day 4 — Do Not Install)

**Priority: INFO — Updated June 24 Evening (supersedes Finding 33)**

2026.6.10-beta.2 is in Day 4 of beta. Auto fast mode automatically uses faster inference for short
conversational turns — directly relevant for Heather's casual Discord exchanges with Josh.

**Timeline:** At current OpenClaw cadence, 2026.6.10-stable could arrive late June or early July 2026.
**Do NOT install.** Stay on 2026.6.9-stable.

---

## ⭐ Finding F42 — Gemini Preview Model Sunset Wave (ESCALATED — see F43) ⚠️

**Priority: CRITICAL (escalated from MEDIUM-HIGH on June 24) — Originally added June 23 Morning**

> ⚠️ See F43 for the June 24 escalation — sister models confirmed shut down TOMORROW June 25.

Google is systematically retiring Gemini preview models:
- `gemini-3.1-flash-image-preview` + `gemini-3-pro-image-preview` → shut down **June 25, 2026 (TOMORROW)**
- `gemini-3.1-flash-lite-preview` → shut down July 9, 2026
- `gemini-3-pro-preview` → already shut down March 9, 2026

Josh's primary model is `google/gemini-3-flash-preview`. No confirmed shutdown date for this model
specifically, but the pattern is unambiguous. See F43 for action steps.

**Recommended migration (resolves F31 at same time):**
```json
"model": {
  "primary": "google/gemini-3.5-flash",
  "fallbacks": [
    "openrouter/anthropic/claude-haiku-4-5",
    "openrouter/google/gemini-3.5-flash"
  ]
}
```

---

## ⭐ Finding F41 — MEMORY.md Day Counts Stale (RESOLVED June 23) ✅

MEMORY.md was last updated June 21. Day counts were off by 2: Google Workspace (Day 91→93),
Heartbeat null (5 days→8 days), iMessage paused (56→58 days). Applied evening scan June 23.

---

## ⭐ Finding F40 — Group Chat Context: Every Turn Now (Informational)

In OpenClaw 2026.6.x, context in group chats is injected on **every turn**, not just the first.
No action required — auto-applied after upgrade to 2026.6.9.

---

## ⭐ Finding F39 — Discord Components V2: Interactive Actions Post-Upgrade

After upgrading to 2026.6.9, Heather gains Discord Components V2: buttons, select menus, modals,
and attachment-backed file blocks. Practical use: offer Discord button confirmation before any
external action. Directly serves Josh's "ask before acting externally" preference. See Rec 14.

---

## ⭐ Finding F38 — HEARTBEAT.md Missing Cron-Not-Deployed Warning (RESOLVED June 23) ✅

Warning block added to top of workspace/HEARTBEAT.md in the June 23 evening commit.

---

## ⭐ Finding 37 — TOOLS.md Stale Upgrade Warning Removed (RESOLVED June 22) ✅

workspace/TOOLS.md stale HOLD banner and [STOP] marker removed. Current safe target corrected.

---

## ⭐ Finding 36 — Dreaming Config: Verify Key Path Before Applying

**Priority: LOW — clarifies Finding 22/24**

Dreaming config may live under `plugins.entries.memory-core.config.dreaming` rather than as a
top-level `"dreaming"` key. Before applying:
```
openclaw config schema | grep -A 10 "dreaming"
```
Finding 22/24 config values are correct regardless of key path.

---

## ⭐ Finding 35 — AlphaClaw In-App OpenClaw Update Removed

Josh's upgrade **must go through VPS CLI** (`openclaw update`), not the AlphaClaw control UI.

---

## ⭐ Finding 34 — AlphaClaw Git Sync Reliability Fix (Auto-Applied) ✅

AlphaClaw's hourly git sync now resolves the real git binary at runtime. Josh's hourly workspace
backup to `josh_repo` is more reliable. No action required.

---

## ⭐ Finding 33 — OpenClaw 2026.6.10-beta.2: Auto Fast Mode [→ see F46]

**Do not install.** Stay on 2026.6.9-stable. Monitor for 2026.6.10-stable.

---

## ⭐ Finding 32 — iMessage SQLite Migration Will Auto-Fix inbox-state.json (POSITIVE)

OpenClaw 2026.6.1 introduced a storage schema migration that automatically cleans Josh's malformed
`inbox-state.json` duplicate key. After upgrade, iMessage monitoring may partially or fully resume.
**No action required** beyond running the staged upgrade.

---

## ⭐ Finding 31 — Same-Provider Fallback Chain: Single Google Failure Point

**Priority: MEDIUM — now combined with F42/F43**

Current chain: Primary (Google) → Fallback 1 (Google via OpenRouter) → Fallback 2 (Haiku).
Two Google endpoints fail together on an outage. Fix bundled with F43 model migration:
```json
"fallbacks": [
  "openrouter/anthropic/claude-haiku-4-5",
  "openrouter/google/gemini-3.5-flash"
]
```

---

## ⭐ Finding 30 — BRAVE_API_KEY Not Set: Web Search Disabled

**Priority: MEDIUM-HIGH**

No Brave Search API key configured. Heather cannot autonomously search the web. Fix now — no
upgrade needed: AlphaClaw UI → Envars tab → add `BRAVE_API_KEY`.
Free tier: 2,000 queries/month at https://api.search.brave.com/app/keys.

---

## ⭐ Finding 29 — 2026.6.9-STABLE: UPGRADE WINDOW OPEN (Day 5)

**Priority: HIGH**

Key 2026.6.9 improvements for Josh/Heather:
- Enhanced agent recovery: retries, session history repair, interrupted turns reach visible result
- Discord Components V2: buttons, select menus, modals (F39)
- Group chat context on every turn (F40)
- Cron reliability: backoff honored, overdue jobs rescheduled on startup
- Heartbeat de-duplication in main session
- Claude Haiku 4.5 support for fallback 2
- Auto-thread titles (60s timeout, 4,096-token reasoning budget)
- Discord streaming `"progress"` mode
- SQLite iMessage migration safety check (via 2026.6.6 in staged path)

**Bundle in ONE VPS session:**
1. Add `userTimezone: "America/Los_Angeles"` to `agents.defaults` (Finding 28 — FIRST)
2. Add `compaction/memoryFlush` block (Finding 4)
3. Verify dreaming key path (Finding 36), add dreaming config (Finding 22/24)
4. Add heartbeat cron job to `cron.jobs` (Finding 27)
5. Migrate primary model to `gemini-3.5-flash` + fix fallback chain (F42/F43 + F31)
6. Run staged upgrade: 2026.3.22 → 2026.5.27 → 2026.6.2 → 2026.6.5 → 2026.6.6 → 2026.6.9
7. After 2026.6.9: enable Discord streaming `"progress"` mode
8. After 2026.6.9: tighten Discord `allowFrom` (Finding 20)
9. Set BRAVE_API_KEY anytime via AlphaClaw UI (Finding 30)

---

## ⭐ Finding 28 — `userTimezone` Not Set: Silent Timezone Misalignment

**Risk: MEDIUM-HIGH**

VPS is UTC; Josh is in LA (PDT = UTC−7 in June). Without `userTimezone`, heartbeat/dreaming
schedules evaluate in UTC — heartbeats go quiet 7 hours early. Add FIRST before any cron/dreaming:
```json
"agents": { "defaults": { "userTimezone": "America/Los_Angeles" } }
```

---

## ⭐ Finding 27 — Heartbeat State: All Null — Day 10

**Risk: HIGH**

heartbeat-state.json all-null for 10+ consecutive days. Cron never deployed to live openclaw.json.
Add with upgrade session (documented format from openclaw.ai/automation/cron-jobs):
```json
"cron": {
  "jobs": [{
    "name": "Daily heartbeat",
    "schedule": {
      "kind": "cron",
      "expression": "0 9 * * *",
      "timezone": "America/Los_Angeles"
    },
    "sessionTarget": "main",
    "wakeMode": "now",
    "payload": {
      "kind": "systemEvent",
      "text": "Read HEARTBEAT.md and run all scheduled checks — update heartbeat-state.json after."
    }
  }]
}
```

---

## ⭐ Finding 26 — 2026.6.8 Regressions (CONFIRMED SKIP)

Discord image tools (#94266), memory-search (#94316), sub-agent tools (#94158), cron isolation,
misleading fallback (#94176). Never promoted to npm stable. Jump directly from 2026.6.6 to 2026.6.9.

---

## ⭐ Finding 25 — ClawHavoc: Audit Installed Skills

1,184 malicious skills found on ClawHub in early 2026. Josh's skills directory is empty — no
current risk. Run `openclaw skill list` after upgrade to confirm.

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

## ⭐ Finding 22 — Dreaming Still Not Enabled (Day 95)

**Risk: HIGH** — without Dreaming, MEMORY.md only updates when fleet agent or Heather manually
updates it. Use config from Finding 24; verify key path (Finding 36); add `userTimezone` first (Finding 28).

---

## ⭐ Finding 21 — MEMORY.md Size Monitoring

MEMORY.md now ~6,200 bytes. Monitor growth. Limit: ~20,000 chars before noticeable context budget impact.

---

## ⭐ Finding 20 — Discord Security: Open to All

**Risk: MEDIUM-HIGH**

`groupPolicy: open`, `allowFrom: ["*"]` — anyone in Discord server can query Heather with full
personal context. Tighten after upgrade:
```json
"groupPolicy": "allowlist",
"dmPolicy": "allowlist",
"allowFrom": ["JOSH_DISCORD_USER_ID"]
```

---

## ⭐ Finding 4 — No Memory Protection Before Compaction (Day 95)

**Risk: HIGH** — add to openclaw.json:
```json
"compaction": {
  "reserveTokensFloor": 40000,
  "memoryFlush": { "enabled": true, "softThresholdTokens": 4000 }
},
"contextPruning": { "mode": "cache-ttl", "ttl": "6h" }
```

---

## ⭐ Finding 2 — Google Workspace Not Connected (Day 95 — CRITICAL)

No Google OAuth connected. Gmail, Calendar, Contacts all inaccessible. Three of five heartbeat
checks permanently blocked.
1. AlphaClaw UI: https://5.78.142.81.sslip.io#general → Google Workspace → OAuth
2. Full steps in workspace/memory/onboarding-google.md
3. Alternative: Remote MCP via AlphaClaw 0.9.18 Envars tab (Finding 23)

---

## Summary Table (Updated June 24 Evening)

| Finding | Priority | Status |
|---------|----------|--------|
| **F43. Gemini sister models shut down TOMORROW June 25** | **CRITICAL** | ⏳ Act TONIGHT |
| F44. Noah session scope broken — Day 14 | FLEET OPS | ⏳ Fix scope |
| F45. SkillSpector standard on ClawHub | POSITIVE | ✅ Auto post-upgrade |
| F46. 2026.6.10-beta.2 auto fast mode Day 4 | INFO | 🔬 Monitor — do not install |
| F42. Gemini preview sunset (escalated → F43) | CRITICAL | ⏳ See F43 |
| F41. MEMORY.md day counts stale | LOW | ✅ RESOLVED June 23 eve |
| F38. HEARTBEAT.md cron warning missing | MEDIUM | ✅ RESOLVED June 23 eve |
| F39. Discord Components V2 post-upgrade | INFO | 🔬 Post-upgrade capability |
| F40. Group chat context every turn | INFO | 🔬 Auto in 2026.6.9 |
| 37. TOOLS.md stale upgrade warning | INFO | ✅ RESOLVED June 22 |
| 34. AlphaClaw git sync fix | POSITIVE | ✅ Auto-applied |
| 32. iMessage SQLite migration auto-fix | POSITIVE | Confirmed — no action needed |
| 33. 2026.6.10-beta.2 auto fast mode | INFO | 🔬 Monitor — see F46 |
| 35. AlphaClaw in-app update removed | INFO | VPS-only path confirmed |
| 36. Dreaming config key path | LOW | Verify before applying |
| 31. Same-provider fallback chain gap | MEDIUM | ⏳ Fix with F43 model migration |
| 30. BRAVE_API_KEY not set | MEDIUM-HIGH | ⏳ Fix anytime (AlphaClaw UI) |
| 29. **2026.6.9-stable — Day 5 of window** | HIGH | ⏳ Upgrade window open |
| 2. Connect Google Workspace | CRITICAL | ⏳ Day 95 |
| 27. Heartbeat cron not deployed — Day 10 | HIGH | ⏳ Bundle with upgrade |
| 28. userTimezone not set | MEDIUM-HIGH | ⏳ Bundle with upgrade |
| 22/24. Enable Dreaming | HIGH | ⏳ Bundle with upgrade |
| 4. Add compaction/memoryFlush | HIGH | ⏳ Bundle with upgrade |
| Upgrade to 2026.6.9 (staged, skip 2026.6.8) | HIGH | ⏳ WINDOW OPEN — Day 5 |
| 20. Discord security (open → allowlist) | MEDIUM-HIGH | ⏳ After upgrade |
| 26. 2026.6.8 skip confirmed | INFO | ✅ Skip confirmed |
| 23. AlphaClaw 0.9.17/18 features | INFO | Available now |
| 25. ClawHavoc skill audit | LOW | No skills installed — safe |
| Noah scope fix (Day 14) | FLEET OPS | ⏳ Day 14 |

---

## Remaining Open Action List (June 24 Evening)

### Act TONIGHT — No VPS access needed
0. **[CRITICAL]** Check `gemini-3-flash-preview` at https://ai.google.dev/gemini-api/docs/deprecations
   If listed: migrate primary to `gemini-3.5-flash` via AlphaClaw Browse tab (F43)

### Can do NOW — AlphaClaw UI only
1. **[MEDIUM-HIGH]** Set BRAVE_API_KEY in AlphaClaw UI → Envars tab (Finding 30)
2. **[MEDIUM]** Add monthly model health check to HEARTBEAT.md via Browse tab (Rec 17)
3. **[LOW]** Add silent model failure guidance to SOUL.md via Browse tab (Rec 18)

### Requires Josh — bundle in ONE VPS session
4. **[CRITICAL]** Connect Google Workspace OAuth at https://5.78.142.81.sslip.io#general
5. **[HIGH]** Add `userTimezone: "America/Los_Angeles"` to `agents.defaults` (Finding 28 — FIRST)
6. **[HIGH]** Add `compaction/memoryFlush` block (Finding 4)
7. **[HIGH]** Verify dreaming key path (Finding 36), add dreaming config (Finding 22/24)
8. **[HIGH]** Add heartbeat cron job to `cron.jobs` (Finding 27 — updated format above)
9. **[CRITICAL/F43]** Migrate primary: `gemini-3-flash-preview` → `gemini-3.5-flash` + fix fallback chain
10. **[HIGH]** Run staged upgrade: 2026.3.22 → 2026.5.27 → 2026.6.2 → 2026.6.5 → 2026.6.6 → 2026.6.9
    - Verify first: `npm show openclaw@latest version` = `2026.6.9`

### After upgrade to 2026.6.9
11. **[MEDIUM-HIGH]** Tighten Discord allowFrom: `["*"]` → Josh's Discord user ID (Finding 20)
12. **[LOW]** Enable Discord streaming: `"streaming": "progress"`
13. **[LOW]** Enable auto-thread titles
14. **[LOW]** Apply Rec 13, 14: post-upgrade SOUL.md, TOOLS.md, AGENTS.md updates

### Fleet operations
15. **[FLEET OPS]** Fix Noah session scope: noah--repo (404) → Noahrepo2 (Day 14)

---

*Sources: [OpenClaw GitHub Releases](https://github.com/openclaw/openclaw/releases) · [Google Gemini Deprecations](https://ai.google.dev/gemini-api/docs/deprecations) · [OpenClaw Memory Docs](https://docs.openclaw.ai/concepts/memory) · [Brave Search API](https://brave.com/search/api/) · [OpenClaw Cron Docs](https://docs.openclaw.ai/automation/cron-jobs) · [AlphaClaw GitHub](https://github.com/chrysb/alphaclaw) · [ClawHavoc Security](https://thehackernews.com/2026/02/researchers-find-341-malicious-clawhub.html)*
