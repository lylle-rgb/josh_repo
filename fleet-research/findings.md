# Fleet Research Findings — Josh / Heather Schwartz

**Last updated:** 2026-06-21 (morning scan)
**Researcher:** AlphaClaw Fleet Agent
**Instance:** josh_repo (Heather Schwartz — personal assistant)
**Current version:** 2026.3.22
**Safe upgrade target:** **2026.6.9-stable** ✅ Released June 21, 2026 — upgrade window OPEN (skip 2026.6.8)
**Previous hold:** 2026.6.6 (hold lifted — 2026.6.9-stable is now out)

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
> 🆕 NEW (June 21 morning): Finding 32 — iMessage SQLite migration auto-fix path confirmed (POSITIVE)
> 🆕 NEW (June 21 morning): Finding 31 — same-provider fallback chain gap (MEDIUM)
> 🆕 NEW (June 21 morning): Finding 30 — BRAVE_API_KEY not set, web search disabled (MEDIUM-HIGH)
> ⛔ Still open: Google Workspace OAuth not connected — email/calendar inaccessible (Day 91)
> ⛔ Still open: OpenClaw 90+ days outdated (2026.3.22 vs 2026.6.9 safe target)
> ⛔ Still open: heartbeat-state.json all null — Day 6 (cron likely not deployed to VPS)
> ⛔ Still open: userTimezone not set in openclaw.json (Finding 28)
> ⛔ Still open: Dreaming not enabled in openclaw.json (Finding 22/24)
> ⛔ Still open: compaction/memoryFlush not configured (Finding 4)
> ⛔ Still open: Discord security open to all — groupPolicy: open (Finding 20)
> ⛔ Still open: iMessage paused since ~April 27, 2026 (Day 56 — auto-fix path on upgrade, F32)
> ⛔ Still open: Noah session scope broken (noah--repo 404 — should be Noah-workspace, Day 10)

---

## ⚠️ Upgrade Status as of June 21 Morning

| Channel | Version | Status |
|---------|---------|--------|
| npm `latest` (stable) | **2026.6.9** | ✅ Current target — upgrade window OPEN |
| 2026.6.8 | Released June 16 | ⛔ Skip — critical regressions, never on npm stable |
| 2026.6.9-beta.1 | June 19 | Superseded by stable |
| 2026.6.9-stable | **June 21, 2026** | ✅ SHIPPED — no new release overnight |

> **Staged upgrade path (confirmed — skip 2026.6.8):**
> 2026.3.22 → 2026.5.27 → 2026.6.2 → 2026.6.5 → 2026.6.6 → **2026.6.9**
>
> Before upgrading, verify npm stable: `npm show openclaw@latest version` — should return `2026.6.9`.

---

## ⭐ Finding 32 — iMessage SQLite Migration Will Auto-Fix inbox-state.json (POSITIVE)

**Priority: POSITIVE — expected outcome on upgrade**
**Risk: LOW (handled automatically by upgrade)**

OpenClaw 2026.6.1 introduced a storage schema migration moving iMessage monitor state and inbound queues from JSON files to SQLite. Josh's `inbox-state.json` has a malformed duplicate key that cannot be manually repaired (per MEMORY.md). The staged upgrade **automatically runs this migration on first startup through 2026.6.1**, cleaning the malformed state without manual intervention.

Additionally, 2026.6.6 added "verify SQLite auth migration before cleanup" — a safety check ensuring the migration completes before cleanup runs. This makes the staged path through 2026.6.6 → 2026.6.9 doubly safe for iMessage state.

**Expected outcome:** After upgrade, iMessage monitoring may partially or fully resume. At minimum, the corrupted state blocking it will be cleared. The iMessage pause (Day 56) has an automatic fix path baked into the upgrade.

**No action required** beyond running the upgrade.

---

## ⭐ Finding 31 — Same-Provider Fallback Chain: Single Google Failure Point

**Priority: MEDIUM**
**Risk: MEDIUM — partial protection only**

Josh's current model chain:
- **Primary:** `google/gemini-3-flash-preview`
- **Fallback 1:** `openrouter/google/gemini-3.5-flash`
- **Fallback 2:** `openrouter/anthropic/claude-haiku-4-5` (pending upgrade from claude-3.5-haiku)

Fallback 1 and Primary are **both Google**. A Google-wide rate-limit or outage silently burns through Fallback 1 before Haiku 4.5 catches it. Community best practice (OpenRouter multi-model routing guide, VelvetShark): first fallback should always be a different provider.

**Recommended fix — bundle with the fallback 2 upgrade in the upgrade session:**
```json
"model": {
  "default": "google/gemini-3-flash-preview",
  "fallbacks": [
    "openrouter/anthropic/claude-haiku-4-5",
    "openrouter/google/gemini-3.5-flash"
  ]
}
```
Swaps the order: Anthropic Haiku 4.5 becomes the first cross-provider safety net; Gemini Flash stays as the second (Google-preference) fallback.

---

## ⭐ Finding 30 — BRAVE_API_KEY Not Set: Web Search Disabled

**Priority: MEDIUM-HIGH**
**Risk: MEDIUM-HIGH — core research capability unavailable**

No Brave Search API key is configured. Heather cannot autonomously search the web. For a personal assistant, this means research requests, fact-checking, and news lookups silently fall back to stale model knowledge or fail. Brave Search is rated highest for agent use (Agent Score 14.89, AIMultiple Feb 2026); ~700,000 OpenClaw users use it as primary web search.

**Fix — two options:**

Option A — openclaw.json `env` block (requires VPS session):
```json
"env": {
  "BRAVE_API_KEY": "BSAxxxxxxxx"
}
```

Option B — AlphaClaw UI → Envars tab (no VPS SSH required):
Add `BRAVE_API_KEY` = your key in the Envars tab for Josh's agent.

API key: https://api.search.brave.com/app/keys — free tier covers 2,000 queries/month, sufficient for a personal assistant.

**Post-setup enhancement (after upgrade to 2026.6.9):**  
For research-heavy tasks, consider the community "triad" approach: Brave Search (fast URL discovery) + Playwright/browser skill (rendered page extraction) + Fastio (knowledge retention). Start with Brave Search alone; add the others as needed.

---

## ⭐ Finding 29 — 2026.6.9-STABLE RELEASED (June 21, 2026) — UPGRADE WINDOW OPEN

**Priority: HIGH — window confirmed open as of morning June 21**
**Type: POSITIVE — the release we've been watching for**

OpenClaw 2026.6.9-stable confirmed available as of June 21, 2026. No new release appeared overnight. Upgrade window is OPEN.

**What's new in 2026.6.9 relevant to Josh/Heather:**

- **Enhanced agent recovery:** Retries, terminal outcomes, session history repair — interrupted or partial turns now more reliably reach a visible final result.
- **Discord session continuity:** Improved deduplication and message handling — fewer echoed or doubled messages.
- **Claude Haiku 4.5 support confirmed:** Fallback 2 upgrades to `openrouter/anthropic/claude-haiku-4-5`.
- **Auto-thread titles:** Available post-upgrade — 60s timeout, 4,096-token reasoning budget.
- **Discord streaming:** `"progress"` mode can be enabled post-upgrade.
- **Provider stability:** OpenRouter OAuth, normalized tool progress, cleaner compaction ownership.
- **SQLite iMessage migration fix (via 2026.6.6):** Staged path includes the migration safety check.

**Bundle these config changes in ONE VPS session when upgrading:**
1. Add `userTimezone: "America/Los_Angeles"` to `agents.defaults` (Finding 28 — do this FIRST)
2. Add `compaction/memoryFlush` config (Finding 4)
3. Add `dreaming` config with `minScore: 0.8`, `schedule: "0 3 * * *"` (Finding 22/24)
4. Add heartbeat cron job to `cron.jobs` (Finding 27)
5. Run staged upgrade: 2026.3.22 → 2026.5.27 → 2026.6.2 → 2026.6.5 → 2026.6.6 → 2026.6.9
6. After reaching 2026.6.9: update fallback chain (Finding 31) — promote Haiku 4.5 to Fallback 1
7. After reaching 2026.6.9: enable Discord streaming `"progress"` mode
8. After reaching 2026.6.9: tighten Discord `allowFrom: ["*"]` → Josh's Discord user ID (Finding 20)
9. Set BRAVE_API_KEY (can do in AlphaClaw UI at any time, no upgrade needed — Finding 30)

**Risk:** LOW. Staged path is well-tested. Only remaining bug risk is from 2026.6.8, which we skip.

---

## ⭐ Finding 28 — `userTimezone` Not Set: Silent Timezone Misalignment Risk

**Risk: MEDIUM-HIGH — will silently break heartbeat/dreaming schedules**

openclaw.json has no `userTimezone` configured. VPS (5.78.142.81) is UTC. Josh is in Los Angeles (PDT = UTC−7 in June). Without this:
- Any `heartbeat.activeHours` evaluates in UTC — heartbeats go quiet 7 hours early
- Dreaming schedule `"0 3 * * *"` (3 AM UTC = 8 PM PDT) is currently safe but fragile

**Fix (add to `agents.defaults` FIRST, before any cron/dreaming config):**
```json
"agents": {
  "defaults": {
    "userTimezone": "America/Los_Angeles"
  }
}
```

---

## ⭐ Finding 27 — Heartbeat State: All Null — Day 6

**Risk: HIGH — Heather has had zero confirmed proactive monitoring since deployment**

heartbeat-state.json has shown all null timestamps for 6 consecutive days (June 17–21). Most likely cause: heartbeat cron was never deployed to the VPS — the fleet agent created the JSON file via GitHub but no cron schedule exists in the live openclaw.json.

**Add to openclaw.json (bundle with upgrade session):**
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

**Post-upgrade option:** Add `--session isolated` flag when invoking via OS-level cron for extra reliability — gives each run a clean session that starts fresh, does its work, and is pruned after 24h.

---

## ⭐ Finding 26 — 2026.6.8 Regressions (CONFIRMED — SKIP THIS VERSION)

**Risk: HIGH — confirmed skip target**

Known regressions never fixed in 2026.6.8:
- Discord image-tool failure (#94266)
- Memory-search provider breakage (#94316)
- Sub-agent tools broken (#94158)
- Cron isolation regressions
- Misleading terminal-turn fallback (#94176)

npm `latest` was never promoted to 2026.6.8. Jump directly from 2026.6.6 to 2026.6.9.

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

## ⭐ Finding 22 — Dreaming Still Not Enabled (Day 91)

**Risk: HIGH — use corrected config from Finding 24; add `userTimezone` first (Finding 28)**

Without Dreaming, MEMORY.md only updates when the fleet agent pushes changes or Heather does it manually. Add corrected config from Finding 24 to openclaw.json when upgrading.

---

## ⭐ Finding 21 — MEMORY.md Size Monitoring

**Risk: LOW (proactive hygiene)**

MEMORY.md is now ~5,000 bytes. Monitor growth as Heather learns more. Limit: 20,000 chars.

---

## ⭐ Finding 20 — Discord Security: Open to All

**Risk: MEDIUM-HIGH**

Current config: `groupPolicy: open`, `allowFrom: ["*"]`. Anyone in Josh's Discord server can query Heather with full personal context. Tighten after upgrade:
```json
"groupPolicy": "allowlist",
"dmPolicy": "allowlist",
"allowFrom": ["JOSH_DISCORD_USER_ID"]
```

---

## ⭐ Finding 4 — No Memory Protection Before Compaction (Day 91)

**Risk: HIGH**

Add to openclaw.json:
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

**Note:** Community research (Feb 2026) confirms the pre-compaction memory flush is built into OpenClaw but many setups accidentally disable it with thresholds that are too tight, or (like Josh's) don't configure it at all. The `softThresholdTokens: 4000` above is a community-recommended safe value.

---

## ⭐ Finding 2 — Google Workspace Not Connected (Day 91 — CRITICAL)

**Risk: CRITICAL — top Josh action item**

No Google OAuth connected. Gmail, Calendar, Contacts all inaccessible. Three of five heartbeat checks permanently blocked.

1. AlphaClaw UI: https://5.78.142.81.sslip.io#general
2. Google Workspace → provide OAuth credentials
3. Full steps in workspace/memory/onboarding-google.md
4. Alternative: Remote MCP via AlphaClaw 0.9.18 Envars tab (Finding 23)

---

## Summary Table (Updated June 21 Morning)

| Finding | Priority | Status |
|---------|----------|--------|
| 32. iMessage SQLite migration auto-fix on upgrade | POSITIVE | 🆕 Confirmed — no action needed |
| 31. Same-provider fallback chain gap | MEDIUM | 🆕 Open — fix with upgrade |
| 30. BRAVE_API_KEY not set — web search disabled | MEDIUM-HIGH | 🆕 Open — fix anytime (AlphaClaw UI) |
| 29. **2026.6.9-stable released June 21** | HIGH | 🆕 Upgrade window OPEN |
| 2. Connect Google Workspace | CRITICAL | ⏳ Day 91 |
| 27. Heartbeat cron not deployed — Day 6 null | HIGH | ⏳ Day 6 |
| 28. userTimezone not set — silent TZ risk | MEDIUM-HIGH | ⏳ Open |
| 22/24. Enable Dreaming (corrected config) | HIGH | ⏳ Day 91 |
| 4. Add compaction/memoryFlush | HIGH | ⏳ Day 91 |
| Upgrade to 2026.6.9 (staged, skip 2026.6.8) | HIGH | 🆕 WINDOW OPEN |
| 20. Discord security (open → allowlist) | MEDIUM-HIGH | ⏳ Day 91 |
| 26. 2026.6.8 skip confirmed | INFO | ✅ Skip confirmed |
| 23. AlphaClaw 0.9.17/18 new features | INFO | Available now |
| 25. ClawHavoc skill audit | LOW | No skills installed |
| Noah scope fix (noah--repo → Noah-workspace, Day 10) | FLEET OPS | ⏳ Day 10 |

---

## Remaining Open Action List (June 21 Morning)

### Can do NOW — AlphaClaw UI only, no VPS CLI needed
0. **[MEDIUM-HIGH]** Set BRAVE_API_KEY in AlphaClaw UI → Envars tab (Finding 30) — no VPS needed

### Requires Josh action (VPS + AlphaClaw UI) — bundle in ONE session
1. **[CRITICAL]** Connect Google Workspace OAuth at https://5.78.142.81.sslip.io#general
2. **[HIGH]** Add `userTimezone: "America/Los_Angeles"` to `agents.defaults` in openclaw.json (Finding 28 — do FIRST)
3. **[HIGH]** Add `compaction/memoryFlush` block to openclaw.json (Finding 4)
4. **[HIGH]** Add `dreaming` config (minScore: 0.8) to openclaw.json (Finding 22/24)
5. **[HIGH]** Add heartbeat cron job to `cron.jobs` in openclaw.json (Finding 27)
6. **[HIGH]** Run staged upgrade: 2026.3.22 → 2026.5.27 → 2026.6.2 → 2026.6.5 → 2026.6.6 → 2026.6.9
   - Verify: `npm show openclaw@latest version` = `2026.6.9` before running `openclaw update`
   - Test Discord and memory search after each step

### After upgrade to 2026.6.9 (in openclaw.json)
7. **[MEDIUM]** Fix fallback chain (Finding 31): swap Fallback 1 → `openrouter/anthropic/claude-haiku-4-5`, Fallback 2 → `openrouter/google/gemini-3.5-flash`
8. **[MEDIUM-HIGH]** Tighten Discord allowFrom: `["*"]` → Josh's Discord user ID (Finding 20)
9. **[LOW]** Enable Discord streaming: `"streaming": "progress"` in channels.discord
10. **[LOW]** Enable auto-thread titles (available in 2026.6.9)

### AlphaClaw UI (no VPS CLI needed)
11. **[LOW]** Set per-agent `thinkingDefault` from model card (Finding 23)

### Fleet operations
12. **[FLEET OPS]** Fix Noah session scope: noah--repo (404) → Noah-workspace (Day 10)
    - Fleet admin must add `lylle-rgb/Noah-workspace` to agent session allowed repos

---

*Sources: [OpenClaw GitHub Releases](https://github.com/openclaw/openclaw/releases) · [OpenClaw Memory Docs](https://docs.openclaw.ai/concepts/memory) · [VelvetShark Multi-Model Routing](https://velvetshark.com/openclaw-multi-model-routing) · [Brave Search OpenClaw Guide](https://brave.com/search/api/guides/use-with-openclaw/) · [OpenClaw Cron Docs](https://docs.openclaw.ai/automation/cron-jobs) · [AlphaClaw GitHub](https://github.com/chrysb/alphaclaw)*
