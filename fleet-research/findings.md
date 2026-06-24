# Fleet Research Findings — Josh / Heather Schwartz

**Last updated:** 2026-06-24 (morning scan — F47/F48/F49 added)
**Researcher:** AlphaClaw Fleet Agent
**Instance:** josh_repo (Heather Schwartz — personal assistant)
**Current version:** 2026.3.22
**Safe upgrade target:** **2026.6.10-stable** ✅ Released June 24, 2026 at 03:01 UTC — upgrade window OPEN Day 0 (skip 2026.6.8 AND 2026.6.9)
**Previous target:** 2026.6.9 (now superseded — skip due to critical regressions)

> ⚠️ UPGRADED TARGET (June 24 morning): F47 — 2026.6.10 went stable TODAY at 03:01 UTC. Skip 2026.6.9 (own critical regressions). New safe target is 2026.6.10.
> ✅ DOWNGRADED (June 24 morning): F43 CRITICAL → MEDIUM-HIGH — gemini-3-flash-preview has NO announced shutdown date on Google's deprecation page. Migration to 3.5-flash still recommended (proactive), but not emergency.
> ⚠️ CRITICAL (June 24 eve): F43 — gemini-3.1-flash-image-preview + gemini-3-pro-image-preview shut down June 25 — primary model `gemini-3-flash-preview` confirmed NOT on same shutdown list
> ✅ RESOLVED (June 23 eve): F41 — MEMORY.md day counts updated (was stale 2 days)
> ✅ RESOLVED (June 23 eve): F38 — HEARTBEAT.md cron-not-deployed warning applied
> ✅ RESOLVED (June 22): Finding 37 — TOOLS.md stale "HOLD/STOP" upgrade warning removed
> ✅ RESOLVED (June 21): 2026.6.9-stable shipped — upgrade hold lifted (now superseded by F47)
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
> 🆕 NEW (June 24 morning): F47 — 2026.6.10 went stable TODAY — upgrade target updated, skip 2026.6.9 (CRITICAL)
> 🆕 NEW (June 24 morning): F48 — F43 downgraded: gemini-3-flash-preview NOT on shutdown list (CORRECTION)
> 🆕 NEW (June 24 morning): F49 — Noah Day 15 + Alpaca MCP Server v2 opportunity (FLEET OPS)
> 🆕 NEW (June 24 eve): F43 — Gemini preview SISTER MODELS shut down June 25 (MEDIUM-HIGH, not CRITICAL per F48)
> 🆕 NEW (June 24 eve): F44 — Noah session scope broken — Day 14 (FLEET OPS)
> 🆕 NEW (June 24 eve): F45 — SkillSpector now standard on all ClawHub installs (INFO/POSITIVE)
> 🆕 NEW (June 24 eve): F46 — 2026.6.10-beta.2 auto fast mode — NOW STABLE per F47
> 🆕 NEW (June 23 morning): F42 — Gemini preview sunset wave — ESCALATED → see F43/F48
> 🆕 NEW (June 23 eve): F41 — MEMORY.md day counts were stale 2 days — RESOLVED ✅
> 🆕 NEW (June 23 eve): F38 — HEARTBEAT.md cron-not-deployed warning — RESOLVED ✅
> 🆕 NEW (June 23 eve): F39 — Discord Components V2 post-upgrade (buttons, modals, confirmations)
> 🆕 NEW (June 23 eve): F40 — Group chat context injection on every turn (auto in 2026.6.9+)
> 🆕 NEW (June 22 morning): Finding 37 — TOOLS.md stale hold removed (RESOLVED)
> 🆕 NEW (June 22 morning): Finding 36 — dreaming config key path needs verification before applying (LOW)
> 🆕 NEW (June 22 morning): Finding 35 — AlphaClaw in-app update removed, VPS-only upgrade confirmed (INFO)
> 🆕 NEW (June 22 morning): Finding 34 — AlphaClaw git sync reliability fix (POSITIVE — auto-applied)
> 🆕 NEW (June 21 morning): Finding 32 — iMessage SQLite migration auto-fix path confirmed (POSITIVE)
> 🆕 NEW (June 21 morning): Finding 31 — same-provider fallback chain gap (MEDIUM)
> 🆕 NEW (June 21 morning): Finding 30 — BRAVE_API_KEY not set, web search disabled (MEDIUM-HIGH)
> ⛔ Still open: Google Workspace OAuth not connected — email/calendar inaccessible (Day 95)
> ⛔ Still open: OpenClaw 95 days outdated (2026.3.22 vs 2026.6.10 safe target)
> ⛔ Still open: heartbeat-state.json all null — Day 10 (cron not deployed to VPS)
> ⛔ Still open: userTimezone not set in openclaw.json (Finding 28)
> ⛔ Still open: Dreaming not enabled in openclaw.json (Finding 22/24)
> ⛔ Still open: compaction/memoryFlush not configured (Finding 4)
> ⛔ Still open: Discord security open to all — groupPolicy: open (Finding 20)
> ⛔ Still open: iMessage paused since ~April 27, 2026 (Day 60 — auto-fix on upgrade, Finding 32)
> ⛔ Still open: Noah session scope broken (noah--repo 404 — Day 15)

---

## ⚠️ Upgrade Status as of June 24 Morning

| Channel | Version | Status |
|---------|---------|--------|
| npm `latest` (stable) | **2026.6.10** | ✅ NEW stable as of 03:01 UTC June 24 — upgrade target UPDATED |
| 2026.6.9 | Released June 21 | ⛔ SKIP — critical regressions (memory relocation, email corruption, cron failures) |
| 2026.6.8 | Released June 16 | ⛔ SKIP — critical regressions, never on npm stable |
| 2026.3.22 | Josh's current | ⛔ 95 days outdated |

> **Staged upgrade path (UPDATED — skip BOTH 2026.6.8 and 2026.6.9):**
> 2026.3.22 → 2026.5.27 → 2026.6.2 → 2026.6.5 → 2026.6.6 → **2026.6.10**
>
> Before upgrading: `npm show openclaw@latest version` must return `2026.6.10`.
> 2026.6.10 is Day 0 of stable (June 24). Run smoke test: Discord replies, longer agent runs,
> model fallback, cron delivery, memory search.

---

## ⭐ Finding F47 — 2026.6.10 Stable TODAY: Upgrade Target Updated + Skip 2026.6.9

**Priority: CRITICAL — Added June 24 Morning**

OpenClaw 2026.6.10 went stable at **03:01 UTC on June 24, 2026** (today). This was just hours after
the evening scan that still recommended 2026.6.9. The upgrade target has changed.

**Why skip 2026.6.9 (now documented):**
ClawStat.us confirmed: skip 2026.6.9. Three critical regressions:
1. Memory store silently relocates with no migration, forcing full re-embed of existing corpora (#95495)
2. Memory search intermittently fails with 'index metadata is missing' due to search/reindex race (#90361)
3. Upgrading corrupts email channel config with a spurious field that prevents Gateway from starting (#95515)

Additional issues in 2026.6.9: isolated cron fails with "LLM request failed", model fallback chain bypasses.

**2026.6.10 new features (now STABLE, not beta):**
- **Auto fast mode for conversations:** Short conversational turns automatically use faster inference,
  then return to normal for longer work. Directly reduces Heather's response latency on quick Discord exchanges with Josh.
- **Better provider routing:** Tightens Zhipu GLM overload failover and native reasoning-level selection.
- **Session/channel state fixes:** Channel switches clear stale origin fields; cron delivery stays tied to target session.

**Updated staged path (skip both 2026.6.8 and 2026.6.9):**
```
2026.3.22 → 2026.5.27 → 2026.6.2 → 2026.6.5 → 2026.6.6 → 2026.6.10
```

**Risk level:** LOW (2026.6.10 fixes known regressions, no new breaking changes from 2026.6.9).
CAUTION: Day 0 of stable — run smoke test before broad reliance. Heather on 2026.3.22 is far enough
behind that staged intermediate stops are essential regardless.

**Action:** Update all references to 2026.6.9 upgrade target → 2026.6.10. MEMORY.md and TOOLS.md
have been updated in this commit.

---

## ⭐ Finding F48 — F43 DOWNGRADED: gemini-3-flash-preview NOT on Shutdown List

**Priority: MEDIUM-HIGH (downgraded from CRITICAL) — Added June 24 Morning**

**CORRECTION to F43:** Morning scan confirmed via Google's official deprecation page:
- `gemini-3-flash-preview` has **NO announced shutdown date**
- Only the image/video generation sister models are shutting down June 25:
  - `gemini-3.1-flash-image-preview` → SHUTDOWN JUNE 25 ✅ confirmed
  - `gemini-3-pro-image-preview` → SHUTDOWN JUNE 25 ✅ confirmed
- These are different model IDs from Heather's primary (`google/gemini-3-flash-preview`)

**F43 status revised:** Not an overnight emergency. Heather's primary will continue to work.
Fallback chain (gemini-3.5-flash via OpenRouter) is intact if primary ever fails silently.

**Still recommended (MEDIUM-HIGH, not CRITICAL):**
Migration to `google/gemini-3.5-flash` (GA stable) is still the right proactive move because:
- `gemini-3-flash-preview` is a preview model — Google retires previews on rolling schedule
- GA models have longer support windows and stability guarantees
- Migration also resolves F31 (same-provider fallback gap)
- Can be done via AlphaClaw Browse tab anytime, no upgrade required

**Recommended config (same as F43, just not urgent tonight):**
```json
"model": {
  "primary": "google/gemini-3.5-flash",
  "fallbacks": [
    "openrouter/anthropic/claude-haiku-4-5",
    "openrouter/google/gemini-3.5-flash"
  ]
}
```

**Risk level:** MEDIUM-HIGH. No immediate shutdown threat but preview model with no GA support SLA.

---

## ⭐ Finding F49 — Noah Session Scope: Day 15 + Alpaca MCP Server v2 Gap

**Priority: FLEET OPS — Added June 24 Morning (extends F44)**

Noah's repo scope remains broken (noah--repo 404). Now Day 15 without fleet coverage.

**New development this morning:** Alpaca launched MCP Server v2 with 65 tools (up from 43):
- Auto-updates from OpenAPI specs — stays compatible without client-side changes
- New: order replacements, option chain exploration, market screening, account activity logs
- As of June 16: Trading MCP Server expanded, dedicated docs added, API changelogs introduced

Noah's Market Catalyst Agent relies on Alpaca paper trading. Without repo access, fleet agent cannot:
- Verify Noah's current Alpaca integration version or whether MCP v2 is configured
- Check Noah's OpenClaw version (last known git sync: March 2026 — ~108 days ago)
- Review installed skills for ClawHavoc exposure (Noah is highest-risk: trading + external APIs)
- Confirm model configuration, fallback chain, or cron/heartbeat status

**Noah repos found via GitHub search (outside current session scope):**
- `lylle-rgb/Noahrepo2` — last updated 2026-03-08 (most recent, likely primary)
- `lylle-rgb/Noah-workspace` — last updated 2026-03-07

**Action:**
1. Fleet admin: fix session scope to include `lylle-rgb/Noahrepo2`
2. On next scan: full Noah workspace audit (SOUL.md, MEMORY.md, AGENTS.md, TOOLS.md, openclaw.json)
3. Verify Alpaca MCP Server v2 integration opportunity for Noah's trading bot

---

## ⭐ Finding F43 — Gemini Preview Sister Models Shut Down June 25 (MEDIUM-HIGH per F48)

**Priority: MEDIUM-HIGH (downgraded from CRITICAL per F48) — Added June 24 Evening**

> ⚠️ UPDATE (June 24 morning, F48): gemini-3-flash-preview itself is NOT on the shutdown list.
> F43 priority has been downgraded. Migration still recommended proactively. See F48.

Two Gemini preview models confirmed shutting down **June 25, 2026**:
- `gemini-3.1-flash-image-preview` → SHUTDOWN JUNE 25
- `gemini-3-pro-image-preview` → SHUTDOWN JUNE 25

Josh's primary model is `google/gemini-3-flash-preview` — confirmed NOT on the shutdown list as of
June 24 morning. Fallback chain (openrouter/google/gemini-3.5-flash, openrouter/anthropic/claude-3.5-haiku)
remains intact if primary ever fails silently.

**See F48 for corrected action plan.** Migration to gemini-3.5-flash still recommended (proactive, not urgent).

---

## ⭐ Finding F44 — Noah Session Scope: Broken — Day 14

**Priority: FLEET OPS — See F49 for Day 15 update**

The configured Noah repo (`lylle-rgb/noah--repo`) returns 404. See F49.

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

---

## ⭐ Finding F46 — 2026.6.10 Auto Fast Mode: NOW STABLE (superseded by F47)

**Priority: INFO — Updated June 24 Morning (see F47)**

2026.6.10 graduated from beta to stable at 03:01 UTC June 24. Auto fast mode is now part of the
stable release. See F47 for details and upgrade guidance.

---

## ⭐ Finding F42 — Gemini Preview Model Sunset Wave (see F43/F48)

**Priority: MEDIUM-HIGH (see F48 for correction) — Originally added June 23 Morning**

Google is systematically retiring Gemini preview models. See F43 for original escalation and F48
for morning scan correction: gemini-3-flash-preview has no confirmed shutdown date.

---

## ⭐ Finding F39 — Discord Components V2: Interactive Actions Post-Upgrade

After upgrading to 2026.6.10, Heather gains Discord Components V2: buttons, select menus, modals,
and attachment-backed file blocks. Directly serves Josh's "ask before acting externally" preference.

---

## ⭐ Finding F40 — Group Chat Context: Every Turn Now (Informational)

In OpenClaw 2026.6.x, context in group chats is injected on **every turn**, not just the first.
No action required — auto-applied after upgrade to 2026.6.10.

---

## ⭐ Finding 36 — Dreaming Config: Verify Key Path Before Applying

**Priority: LOW — clarifies Finding 22/24**

Dreaming config may live under `plugins.entries.memory-core.config.dreaming` rather than as a
top-level `"dreaming"` key. Before applying:
```
openclaw config schema | grep -A 10 "dreaming"
```

---

## ⭐ Finding 35 — AlphaClaw In-App OpenClaw Update Removed

Josh's upgrade **must go through VPS CLI** (`openclaw update`), not the AlphaClaw control UI.

---

## ⭐ Finding 34 — AlphaClaw Git Sync Reliability Fix (Auto-Applied) ✅

AlphaClaw's hourly git sync now resolves the real git binary at runtime. Josh's hourly workspace
backup to `josh_repo` is more reliable. No action required.

---

## ⭐ Finding 32 — iMessage SQLite Migration Will Auto-Fix inbox-state.json (POSITIVE)

OpenClaw 2026.6.1 introduced a storage schema migration that automatically cleans Josh's malformed
`inbox-state.json` duplicate key. After staged upgrade through 2026.6.6, iMessage monitoring may partially or fully resume.
**No action required** beyond running the staged upgrade.

---

## ⭐ Finding 31 — Same-Provider Fallback Chain: Single Google Failure Point

**Priority: MEDIUM — bundle with F48 model migration**

Current chain: Primary (Google) → Fallback 1 (Google via OpenRouter) → Fallback 2 (Haiku).
Two Google endpoints can fail together on a Google outage. Fix bundled with F48 model migration:
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

## ⭐ Finding 29 — 2026.6.10-STABLE: UPGRADE WINDOW OPEN (Day 0)

**Priority: HIGH — Updated June 24 Morning (was 2026.6.9)**

Key 2026.6.10 improvements for Josh/Heather:
- **Auto fast mode:** Short conversational turns automatically use faster inference (graduated from beta)
- Enhanced agent recovery: retries, session history repair, interrupted turns reach visible result
- Discord Components V2: buttons, select menus, modals (F39)
- Group chat context on every turn (F40)
- Cron reliability: backoff honored, overdue jobs rescheduled on startup
- Heartbeat de-duplication in main session
- Claude Haiku 4.5 support for fallback 2
- Auto-thread titles (60s timeout, 4,096-token reasoning budget)
- Discord streaming `"progress"` mode
- SQLite iMessage migration safety check (via 2026.6.6 in staged path)
- Better provider routing: Zai synthesis, Zhipu GLM failover, reasoning-level selection
- Session/channel state fixes: stale origin field clearing, cron delivery awareness

**Bundle in ONE VPS session:**
1. Add `userTimezone: "America/Los_Angeles"` to `agents.defaults` (Finding 28 — FIRST)
2. Add `compaction/memoryFlush` block (Finding 4)
3. Verify dreaming key path (Finding 36), add dreaming config (Finding 22/24)
4. Add heartbeat cron job to `cron.jobs` (Finding 27)
5. Migrate primary model to `gemini-3.5-flash` + fix fallback chain (F48 + F31)
6. Run staged upgrade: 2026.3.22 → 2026.5.27 → 2026.6.2 → 2026.6.5 → 2026.6.6 → 2026.6.10
   - Verify first: `npm show openclaw@latest version` = `2026.6.10`
   - Day 0 of stable — run smoke test after: Discord replies, longer agent runs, model fallback
7. After 2026.6.10: enable Discord streaming `"progress"` mode
8. After 2026.6.10: tighten Discord `allowFrom` (Finding 20)

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
Add with upgrade session:
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

## ⭐ Finding 26 — 2026.6.8 AND 2026.6.9 Regressions (CONFIRMED SKIP BOTH)

**2026.6.8:** Discord image tools (#94266), memory-search (#94316), sub-agent tools (#94158), cron isolation,
misleading fallback (#94176). Never promoted to npm stable.

**2026.6.9:** Memory store silent relocation (#95495), memory search race (#90361), email config
corruption (#95515), isolated cron LLM errors, model fallback chain bypasses. ClawStat.us: skip.

Jump directly from 2026.6.6 to 2026.6.10.

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

## Summary Table (Updated June 24 Morning)

| Finding | Priority | Status |
|---------|----------|--------|
| **F47. 2026.6.10 stable TODAY — skip 2026.6.9** | **HIGH** | ⏳ Upgrade target updated |
| **F48. F43 downgraded — gemini-3-flash-preview NOT deprecated** | **CORRECTION** | ✅ Downgraded to MEDIUM-HIGH |
| **F49. Noah Day 15 + Alpaca MCP v2 gap** | **FLEET OPS** | ⏳ Fix scope |
| F43. Gemini sister models shut down June 25 | MEDIUM-HIGH | ✅ Primary model safe (per F48) |
| F44. Noah session scope broken | FLEET OPS | ⏳ See F49 |
| F45. SkillSpector standard on ClawHub | POSITIVE | ✅ Auto post-upgrade |
| F46. 2026.6.10 auto fast mode | INFO | ✅ NOW STABLE (see F47) |
| F42. Gemini preview sunset | MEDIUM-HIGH | ⏳ See F48 |
| F39. Discord Components V2 post-upgrade | INFO | 🔬 Post-upgrade capability |
| F40. Group chat context every turn | INFO | 🔬 Auto in 2026.6.10 |
| 34. AlphaClaw git sync fix | POSITIVE | ✅ Auto-applied |
| 32. iMessage SQLite migration auto-fix | POSITIVE | Confirmed — no action needed |
| 35. AlphaClaw in-app update removed | INFO | VPS-only path confirmed |
| 36. Dreaming config key path | LOW | Verify before applying |
| 31. Same-provider fallback chain gap | MEDIUM | ⏳ Fix with F48 model migration |
| 30. BRAVE_API_KEY not set | MEDIUM-HIGH | ⏳ Fix anytime (AlphaClaw UI) |
| 29. **2026.6.10-stable — Day 0 of window** | HIGH | ⏳ Upgrade window open |
| 2. Connect Google Workspace | CRITICAL | ⏳ Day 95 |
| 27. Heartbeat cron not deployed — Day 10 | HIGH | ⏳ Bundle with upgrade |
| 28. userTimezone not set | MEDIUM-HIGH | ⏳ Bundle with upgrade |
| 22/24. Enable Dreaming | HIGH | ⏳ Bundle with upgrade |
| 4. Add compaction/memoryFlush | HIGH | ⏳ Bundle with upgrade |
| Upgrade to 2026.6.10 (staged, skip 2026.6.8 + 6.9) | HIGH | ⏳ WINDOW OPEN — Day 0 |
| 20. Discord security (open → allowlist) | MEDIUM-HIGH | ⏳ After upgrade |
| 26. 2026.6.8 + 2026.6.9 skip confirmed | INFO | ✅ Skip both confirmed |
| 23. AlphaClaw 0.9.17/18 features | INFO | Available now |
| 25. ClawHavoc skill audit | LOW | No skills installed — safe |

---

## Remaining Open Action List (June 24 Morning)

### Can do NOW — AlphaClaw UI only (no VPS access needed)
1. **[MEDIUM-HIGH]** Set BRAVE_API_KEY in AlphaClaw UI → Envars tab (Finding 30)
2. **[MEDIUM-HIGH]** Migrate primary model: `gemini-3-flash-preview` → `gemini-3.5-flash` (F48 + F31)
   Browse tab → `.openclaw/workspace/../openclaw.json` → edit model block → save → gateway restart
   ```json
   "model": {
     "primary": "google/gemini-3.5-flash",
     "fallbacks": [
       "openrouter/anthropic/claude-haiku-4-5",
       "openrouter/google/gemini-3.5-flash"
     ]
   }
   ```

### Requires Josh — bundle in ONE VPS session
3. **[CRITICAL]** Connect Google Workspace OAuth at https://5.78.142.81.sslip.io#general
4. **[HIGH]** Add `userTimezone: "America/Los_Angeles"` to `agents.defaults` (Finding 28 — FIRST)
5. **[HIGH]** Add `compaction/memoryFlush` block (Finding 4)
6. **[HIGH]** Verify dreaming key path (Finding 36), add dreaming config (Finding 22/24)
7. **[HIGH]** Add heartbeat cron job to `cron.jobs` (Finding 27)
8. **[HIGH]** Run staged upgrade: 2026.3.22 → 2026.5.27 → 2026.6.2 → 2026.6.5 → 2026.6.6 → 2026.6.10
   - Verify first: `npm show openclaw@latest version` = `2026.6.10`
   - Day 0 of stable — run smoke test after upgrade

### After upgrade to 2026.6.10
9. **[MEDIUM-HIGH]** Tighten Discord allowFrom: `["*"]` → Josh's Discord user ID (Finding 20)
10. **[LOW]** Enable Discord streaming: `"streaming": "progress"`
11. **[LOW]** Enable auto-thread titles

### Fleet operations
12. **[FLEET OPS]** Fix Noah session scope: noah--repo (404) → Noahrepo2 (Day 15)

---

*Sources: [OpenClaw Releases](https://github.com/openclaw/openclaw/releases) · [ClawStat.us](https://clawstat.us/) · [Google Gemini Deprecations](https://ai.google.dev/gemini-api/docs/deprecations) · [OpenClaw Cron Docs](https://docs.openclaw.ai/automation/cron-jobs) · [AlphaClaw GitHub](https://github.com/chrysb/alphaclaw) · [Alpaca MCP Server v2](https://alpaca.markets/blog/alpaca-launches-mcp-server-v2/) · [OpenRouter OpenClaw Guide](https://openrouter.ai/blog/tutorials/openclaw-openrouter/) · [ClawHavoc Security](https://thehackernews.com/2026/02/researchers-find-341-malicious-clawhub.html)*
