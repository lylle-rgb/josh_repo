# Fleet Research Findings — Josh / Heather Schwartz

**Last updated:** 2026-06-27 (morning scan — F59/F60/F61/F62 added)
**Researcher:** AlphaClaw Fleet Agent
**Instance:** josh_repo (Heather Schwartz — personal assistant)
**Current version:** 2026.3.22
**Safe upgrade target:** **2026.6.10-stable** ✅ Day 4 of stable (June 27) — upgrade window FULLY OPEN, clean community signal
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
> 🆕 NEW (June 27 morning): F59 — 2026.6.10 CLI features not yet documented (explicit compaction, dry-run previews, session rename, SSH preflight)
> 🆕 NEW (June 27 morning): F60 — AlphaClaw 0.9.18 confirmed current; Remote MCP alternative path for Google Workspace
> 🆕 NEW (June 27 morning): F61 — Google Workspace Day 98 — Day 100 in 2 days — escalation framing ready for Heather
> 🆕 NEW (June 27 morning): F62 — Noah trading ecosystem: Agent Mesh pattern, Alpaca MCP v2, 311+ finance skills
> 🆕 NEW (June 27 evening): F53 — PR #96233 heartbeat_prompt_contribution fix in 2026.6.10 — direct fix for known issue
> 🆕 NEW (June 26 morning): F56 — 2026.6.10 Day 2 stable: upgrade window FULLY OPEN (POSITIVE HIGH)
> 🆕 NEW (June 26 morning): F57 — Google Workspace OAuth: Day 97 — 3 days to Day 100 (CRITICAL)
> 🆕 NEW (June 26 morning): F58 — Noah Day 17 — scope fix still pending (FLEET OPS)
> 🆕 NEW (June 25 evening): F50 — 2026.6.11-beta.1: per-DM model overrides + file-driven workflows (INFO)
> 🆕 NEW (June 25 evening): F51 — HEARTBEAT.md stale 2026.6.9 ref fixed → 2026.6.10 (RESOLVED ✅)
> 🆕 NEW (June 25 evening): F52 — Gemini sister models confirmed shut down June 25, primary safe (CONFIRMED ✅)
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
> ⛔ Still open: Google Workspace OAuth not connected — email/calendar inaccessible (**Day 98 — Day 100 in 2 days, June 29**)
> ⛔ Still open: OpenClaw 98 days outdated (2026.3.22 vs 2026.6.10 safe target)
> ⛔ Still open: heartbeat-state.json all null — Day 14+ (cron not deployed to VPS)
> ⛔ Still open: userTimezone not set in openclaw.json (Finding 28)
> ⛔ Still open: Dreaming not enabled in openclaw.json (Finding 22/24)
> ⛔ Still open: compaction/memoryFlush not configured (Finding 4)
> ⛔ Still open: Discord security open to all — groupPolicy: open (Finding 20)
> ⛔ Still open: iMessage paused since ~April 27, 2026 (Day 63 — auto-fix on upgrade, Finding 32)
> ⛔ Still open: Noah session scope broken (noah--repo 404 — Day 18)

---

## ⚠️ Upgrade Status as of June 27 Morning

| Channel | Version | Status |
|---------|---------|--------|
| npm `latest` (stable) | **2026.6.10** | ✅ Day 4 of stable (June 27) — upgrade window FULLY OPEN, 96h clean |
| 2026.6.11-beta.1 | Released June 24 | 🔬 Beta — monitor for stable; do not install |
| 2026.6.9 | Released June 21 | ⛔ SKIP — critical regressions (memory relocation, email corruption, cron failures) |
| 2026.6.8 | Released June 16 | ⛔ SKIP — critical regressions, never on npm stable |
| 2026.3.22 | Josh's current | ⛔ 98 days outdated |

> **Staged upgrade path (UPDATED — skip BOTH 2026.6.8 and 2026.6.9):**
> 2026.3.22 → 2026.5.27 → 2026.6.2 → 2026.6.5 → 2026.6.6 → **2026.6.10**
>
> Before upgrading: `npm show openclaw@latest version` must return `2026.6.10`.
> Day 4 of stable as of June 27 — clean signal, proceed confidently.
> Run smoke test after upgrade: Discord replies, longer agent runs, model fallback, cron delivery, memory search.

---

## ⭐ Finding F62 — Noah Trading Ecosystem Intelligence (Collected While Scope Broken)

**Priority: HIGH (when scope restored) — Added June 27 Morning**

Noah scope is broken (Day 18 — see F58). Intelligence gathered proactively for Market Catalyst Agent:

**ClawHub finance ecosystem (June 2026):**
- 13,700+ total skills on ClawHub marketplace; 311+ finance/investing skills specifically
- `sec-filing-watcher` confirmed operational — EDGAR monitoring for 8-K/10-Q/10-K/S-1; Discord alerts with summaries
- `alpaca-trading` on clawbot.ai confirmed operational — full Alpaca API (paper + live, all order types, portfolio management, market data)

**Agent Mesh architecture for trading bots (emerging March 2026+):**
```
Research Agent (SEC/news signal detection)
  → Risk Agent (position sizing, drawdown limits)
    → Execution Agent (Alpaca order routing)
```
Noah is currently a single-agent design. Splitting into a 3-stage pipeline would improve reliability and allow separate model per stage (fast/cheap for signal detection, reasoning-capable for risk sizing). OpenClaw's 3-layer architecture natively supports this pattern.

**Alpaca MCP Server v2 (when scope restored):**
- 65 tools vs 43 in v1; auto-updates from OpenAPI specs
- New: order replacements, option chain exploration, market screening, account activity logs, API changelog tracking
- Install: `uvx alpaca-mcp-server`

**Action when scope is fixed:**
1. `openclaw skill list` — ClawHavoc audit (Noah is highest-risk customer)
2. Check Alpaca integration version; upgrade to MCP v2 if on v1 or direct API
3. Install `sec-filing-watcher` if not present
4. Evaluate Agent Mesh restructuring for more reliable catalyst detection

---

## ⭐ Finding F61 — Google Workspace Day 98: Day 100 Escalation Imminent

**Priority: CRITICAL — Added June 27 Morning**

Day 98 of Google Workspace OAuth disconnect. **Day 100 arrives June 29** — 2 days from now.

Per MEMORY.md (June 27 evening lesson): "At Day 100 milestones and every 10 days after, surface persistently unresolved gaps to Josh proactively with their concrete fix steps."

**Escalation framing (ready for Heather to use on next main session):**
> "We're 2 days from Day 100 without email or calendar. That's a 100-day blind spot on your inbox and schedule. The fix is 5 minutes in the browser: AlphaClaw General tab → Google Workspace OAuth. I can do everything else — but until then, I can't see your email or calendar."

**Fix paths:**
1. **Primary (5 min):** https://5.78.142.81.sslip.io#general → Google Workspace OAuth
2. **Alternative (AlphaClaw 0.9.18 — no OAuth required):** Envars tab → `REMOTE_MCP_URL` + `REMOTE_MCP_API_TOKEN` pointing to a Google Workspace MCP server

---

## ⭐ Finding F60 — AlphaClaw 0.9.18 Confirmed Current; Remote MCP Google Workspace Path

**Priority: INFO + MEDIUM action item — Added June 27 Morning**

AlphaClaw **0.9.18** (June 1, 2026) confirmed as the current stable release. No 0.9.19 or 0.9.20 in June 2026.

**New intelligence — Remote MCP as Google Workspace alternative:**
AlphaClaw 0.9.18 added managed remote MCP server support via env vars. This is an undocumented alternative to the AlphaClaw OAuth flow for Google Workspace:
```
REMOTE_MCP_URL=<Google Workspace MCP endpoint>
REMOTE_MCP_API_TOKEN=<token>
```
Set in AlphaClaw Envars tab → gives Heather email/calendar/contacts access without the AlphaClaw OAuth flow. Worth exploring if OAuth has friction.

**0.9.18 full feature list:**
- OpenAI-compatible API proxy (`/v1/chat/completions`, `/v1/embeddings`) — disabled by default; enable in General → Features
- Remote MCP: `REMOTE_MCP_URL` + `REMOTE_MCP_API_TOKEN` in Envars tab
- Security: timing-safe token comparison, rate-limiting, header stripping
- 637-test coverage

---

## ⭐ Finding F59 — 2026.6.10 Additional CLI Features (Not Yet in Docs)

**Priority: MEDIUM — Added June 27 Morning**

2026.6.10 includes several CLI features not in prior findings that add direct value:

| Feature | What it does | Value for Josh/Heather |
|---------|-------------|------------------------|
| **Explicit compaction** | Manual context compaction command | Heather controls long-session memory proactively |
| **Dry-run message previews** | Preview external messages before sending | Safer external actions; serves Josh's "ask first" SOUL.md rule |
| **Session renaming** | Rename sessions in AlphaClaw UI | Cleaner history browsing |
| **Duration display** | Turn and session duration in CLI | Diagnose slow turns, routing issues |
| **SSH tunnel preflight** | Error detection before tunnel failure | Fewer silent SSH connectivity surprises |

**No config required** — all available immediately post-upgrade to 2026.6.10.

---

## ⭐ Finding F53 — PR #96233: Heartbeat Prompt Contribution Fix in 2026.6.10

**Priority: HIGH — Added June 27 Evening**

- **PR #96233:** `fix(agents): run heartbeat_prompt_contribution on harness prompt builds` — merged into 2026.6.10
- Heartbeat prompt was NOT being correctly applied to harness prompt builds prior to 2026.6.10
- Combined with undeployed cron: double blocker on Heather's proactive monitoring
- Upgrading to 2026.6.10 fixes the prompt-side issue; deploying cron in openclaw.json fixes scheduling
- **Also in 2026.6.10:** PR #93051 `fix(cron): honor configured retry.backoffMs` — more reliable cron retry

**Action:** Bundle cron deployment with the 2026.6.6→2026.6.10 upgrade hop. See sample cron config in Finding 27.

---

## ⭐ Finding F56 — 2026.6.10 Day 4 Stable: Upgrade Window FULLY OPEN

**Priority: HIGH (POSITIVE) — Updated June 27 Morning**

2026.6.10 has been on npm stable for ~96 hours (June 24 03:01 UTC → June 27 morning) with no critical regression reports.

**Current release state:**
- `npm latest`: 2026.6.10 (Day 4 of stable — 96h clean)
- `beta`: 2026.6.11-beta.1 (June 24 — 3 days old, still beta)
- No new stable or beta in the last 24h

**What's in 2026.6.10 (confirmed stable):**
- Auto fast mode for short conversational turns
- Explicit compaction, dry-run message previews, session renaming, SSH preflight (F59)
- Better provider routing and session/channel state fixes
- Cron reliability: backoff honored, overdue jobs rescheduled
- Claude Haiku 4.5 support in fallback chains
- Auto-thread titles in Discord
- SQLite migration safety improvements (critical for iMessage auto-fix at 2026.6.6 hop)
- PR #96233 heartbeat prompt contribution fix (F53)
- PR #93051 cron retry backoff fix (F53)

**Action:** Upgrade window fully open. Day 4 with clean signal — green light.

---

## ⭐ Finding F57 — Google Workspace OAuth: Day 98 — 2 Days to Day 100

**Priority: CRITICAL — Updated June 27 Morning**

Day 98 without Google Workspace OAuth. Day 100 arrives **June 29, 2026** (2 days from now).

Blocked capabilities (unchanged since Day 1): Gmail, Google Calendar, Google Contacts.
Three of five heartbeat check categories permanently blocked.

**The fix is 5 minutes:**
1. Josh opens https://5.78.142.81.sslip.io#general
2. Clicks Google Workspace → completes OAuth flow
3. Gmail, Calendar, and Contacts activate immediately

**Alternative path (AlphaClaw 0.9.18 — see F60):** Remote MCP via Envars tab — no OAuth required if Josh has a Google Workspace MCP endpoint.

---

## ⭐ Finding F58 — Noah Day 18: Scope Fix Still Pending

**Priority: FLEET OPS — Updated June 27 Morning**

Noah session scope still broken. Day 18 without fleet coverage.
- `lylle-rgb/noah--repo` → 404 (has never existed)
- Correct repo confirmed: `lylle-rgb/Noahrepo2` (last updated 2026-03-08)
- Last known OpenClaw version: unknown (~111+ days without git sync)
- ClawHavoc risk unverified — Noah is highest-risk customer (trading APIs + external data)

**Fleet admin action:** Fix session scope to include `lylle-rgb/Noahrepo2`. First scan after fix = full workspace audit.

---

## ⭐ Finding F50 — 2026.6.11-beta.1: Per-DM Model Overrides + Expanded Automation

**Priority: INFO/POSITIVE — Added June 25 Evening**

OpenClaw 2026.6.11-beta.1 shipped on June 24. Preview of upcoming capabilities:

**New capabilities:**
- **Per-DM model overrides:** Configure different AI models per Discord DM — lighter/faster for casual conversation, primary for complex tasks
- **File-driven operator workflows:** `openclaw agent --message-file` — scripted batch automation without interactive sessions
- **Richer Discord output:** HTML tables, markdown preservation, progress drafts, improved rendering
- **Codex partial deltas + prompt-cache stability:** More reliable on interrupted or long streaming agent turns
- **RAFT CLI wake bridge:** Remote agent activation via CLI
- **Slack relay mode, native Mattermost `/oc_queue`:** Expanded platform support

**Action:** Monitor for 2026.6.11-stable. **Do not install beta.** Upgrade to 2026.6.10 first.

---

## ⭐ Finding F51 — HEARTBEAT.md Stale Version Reference Fixed ✅

**Priority: LOW — RESOLVED June 25 Evening**

HEARTBEAT.md stale 2026.6.9 reference corrected to 2026.6.10-stable. No further action.

---

## ⭐ Finding F52 — Gemini Sister Models Confirmed Shut Down June 25 — Primary Unaffected ✅

**Priority: INFORMATIONAL — Added June 25 Evening**

- `gemini-3.1-flash-image-preview` and `gemini-3-pro-image-preview` → confirmed shut down June 25 ✅
- `google/gemini-3-flash-preview` (Heather's primary) → operational, different model ID, NOT affected

This confirms Gemini preview deprecation cadence: shutdown waves arrive on the announced date with no reprieves. Makes proactive migration to `google/gemini-3.5-flash` more compelling.

**Action:** No immediate action. Primary operational. Migration remains MEDIUM-HIGH priority — can do anytime via Browse tab.

---

## ⭐ Finding F47 — 2026.6.10 Stable: Upgrade Target Updated + Skip 2026.6.9

**Priority: CRITICAL — Added June 24 Morning**

OpenClaw 2026.6.10 went stable at **03:01 UTC on June 24, 2026**.

**Why skip 2026.6.9 (ClawStat.us confirmed):**
1. Memory store silently relocates with no migration (#95495)
2. Memory search intermittently fails with 'index metadata is missing' (#90361)
3. Upgrading corrupts email channel config, preventing Gateway from starting (#95515)
4. Additional: isolated cron failures, model fallback chain bypasses

**Updated staged path (skip both 2026.6.8 and 2026.6.9):**
```
2026.3.22 → 2026.5.27 → 2026.6.2 → 2026.6.5 → 2026.6.6 → 2026.6.10
```

**Risk level:** LOW. Day 4 of stable — fully open window.

---

## ⭐ Finding F48 — F43 DOWNGRADED: gemini-3-flash-preview NOT on Shutdown List

**Priority: MEDIUM-HIGH (downgraded from CRITICAL) — Added June 24 Morning**

`gemini-3-flash-preview` has no announced shutdown date. Only sister image/video models shut down June 25 (F52 confirmed). Migration to `google/gemini-3.5-flash` still recommended proactively.

**Recommended config:**
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

## ⭐ Finding F49 — Noah Session Scope: Day 18 + Alpaca MCP Server v2 Gap

**Priority: FLEET OPS — Updated June 27 Morning (Day 18)**

See F58 for current Noah scope status. Alpaca MCP Server v2 opportunity documented in F62.

---

## ⭐ Finding F43 — Gemini Preview Sister Models Shut Down June 25 (MEDIUM-HIGH per F48)

**Priority: MEDIUM-HIGH (downgraded from CRITICAL per F48) — Added June 24 Evening**

> ✅ UPDATE (June 25 evening, F52): Sister models confirmed shut down June 25. Primary unaffected.

See F48 and F52 for current status. Migration to gemini-3.5-flash remains MEDIUM-HIGH priority.

---

## ⭐ Finding F45 — SkillSpector Now Standard on All ClawHub Installs (POSITIVE)

**Priority: INFO/POSITIVE — Added June 24 Evening**

All ClawHub skills now include Skill Cards and SkillSpector scan results. No action required — automatic in 2026.6.9+.

---

## ⭐ Finding F39 — Discord Components V2: Interactive Actions Post-Upgrade

After upgrading to 2026.6.10, Heather gains Discord Components V2: buttons, select menus, modals, and attachment-backed file blocks. Directly serves Josh's "ask before acting externally" preference.

---

## ⭐ Finding F40 — Group Chat Context: Every Turn Now (Informational)

In OpenClaw 2026.6.x, context in group chats is injected on every turn. Auto-applied after upgrade to 2026.6.10.

---

## ⭐ Finding 36 — Dreaming Config: Verify Key Path Before Applying

**Priority: LOW**

Dreaming config may live under `plugins.entries.memory-core.config.dreaming`. Before applying:
```
openclaw config schema | grep -A 10 "dreaming"
```

---

## ⭐ Finding 35 — AlphaClaw In-App OpenClaw Update Removed

Josh's upgrade **must go through VPS CLI** (`openclaw update`), not the AlphaClaw control UI.

---

## ⭐ Finding 34 — AlphaClaw Git Sync Reliability Fix (Auto-Applied) ✅

AlphaClaw's hourly git sync now resolves the real git binary at runtime. Josh's hourly workspace backup to `josh_repo` is more reliable. No action required.

---

## ⭐ Finding 32 — iMessage SQLite Migration Will Auto-Fix inbox-state.json (POSITIVE)

OpenClaw 2026.6.1 introduced a storage schema migration that automatically cleans Josh's malformed `inbox-state.json`. After staged upgrade through 2026.6.6, iMessage monitoring may partially or fully resume. **No action required** beyond running the staged upgrade.

---

## ⭐ Finding 31 — Same-Provider Fallback Chain: Single Google Failure Point

**Priority: MEDIUM — bundle with F48 model migration**

Current chain: Primary (Google) → Fallback 1 (Google via OpenRouter) → Fallback 2 (Haiku). Fix bundled with F48:
```json
"fallbacks": [
  "openrouter/anthropic/claude-haiku-4-5",
  "openrouter/google/gemini-3.5-flash"
]
```

---

## ⭐ Finding 30 — BRAVE_API_KEY Not Set: Web Search Disabled

**Priority: MEDIUM-HIGH**

No Brave Search API key configured. Fix now — no upgrade needed: AlphaClaw UI → Envars tab → add `BRAVE_API_KEY`.
Free tier: 2,000 queries/month at https://api.search.brave.com/app/keys.

---

## ⭐ Finding 29 — 2026.6.10-STABLE: UPGRADE WINDOW OPEN (Day 4)

**Priority: HIGH — Updated June 27 Morning (Day 4 of stable)**

Key 2026.6.10 improvements for Josh/Heather:
- **Auto fast mode:** Short conversational turns automatically use faster inference
- **Explicit compaction, dry-run previews, session rename, SSH preflight** (F59)
- Enhanced agent recovery: retries, session history repair, interrupted turns
- Discord Components V2: buttons, select menus, modals (F39)
- Cron reliability: backoff honored, overdue jobs rescheduled on startup
- PR #96233 heartbeat prompt contribution fix (F53)
- PR #93051 cron retry backoff fix (F53)
- Claude Haiku 4.5 support for fallback 2
- Auto-thread titles (60s timeout, 4,096-token reasoning budget)
- SQLite iMessage migration safety check (via 2026.6.6 in staged path)

**Bundle in ONE VPS session:**
1. Add `userTimezone: "America/Los_Angeles"` to `agents.defaults` (Finding 28 — FIRST)
2. Add `compaction/memoryFlush` block (Finding 4)
3. Verify dreaming key path (Finding 36), add dreaming config (Finding 22/24)
4. Add heartbeat cron job to `cron.jobs` (Finding 27)
5. Migrate primary model to `gemini-3.5-flash` + fix fallback chain (F48 + F31)
6. Run staged upgrade: 2026.3.22 → 2026.5.27 → 2026.6.2 → 2026.6.5 → 2026.6.6 → 2026.6.10
   - Verify first: `npm show openclaw@latest version` = `2026.6.10`
   - Day 4 of stable — proceed confidently
7. After 2026.6.10: enable Discord streaming `"progress"` mode
8. After 2026.6.10: tighten Discord `allowFrom` (Finding 20)

---

## ⭐ Finding 28 — `userTimezone` Not Set: Silent Timezone Misalignment

**Risk: MEDIUM-HIGH**

VPS is UTC; Josh is in LA (PDT = UTC−7 in June). Add FIRST before any cron/dreaming:
```json
"agents": { "defaults": { "userTimezone": "America/Los_Angeles" } }
```

---

## ⭐ Finding 27 — Heartbeat State: All Null — Day 14+

**Risk: HIGH**

heartbeat-state.json all-null for 14+ consecutive days. Cron never deployed to live openclaw.json. Add with upgrade session:
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

**2026.6.8:** Discord image tools (#94266), memory-search (#94316), sub-agent tools (#94158), cron isolation, misleading fallback (#94176). Never promoted to npm stable.

**2026.6.9:** Memory store silent relocation (#95495), memory search race (#90361), email config corruption (#95515), isolated cron LLM errors, model fallback chain bypasses. ClawStat.us: skip.

Jump directly from 2026.6.6 to 2026.6.10.

---

## ⭐ Finding 25 — ClawHavoc: Audit Installed Skills

1,184 malicious skills found on ClawHub in early 2026. Josh's skills directory is empty — no current risk. Run `openclaw skill list` after upgrade to confirm.

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

## ⭐ Finding 23 — AlphaClaw 0.9.18: New Capabilities (Confirmed Current)

- Per-agent `thinkingDefault`: set in AlphaClaw UI model card
- OpenAI-compatible proxy: toggle in AlphaClaw Setup UI
- Remote MCP: set `REMOTE_MCP_URL` + `REMOTE_MCP_API_TOKEN` in AlphaClaw Envars tab (alternative Google Workspace path — see F60)
- **Confirmed current version:** 0.9.18 (June 1, 2026) — no newer release in June 2026

---

## ⭐ Finding 22 — Dreaming Still Not Enabled (Day 98)

**Risk: HIGH** — without Dreaming, MEMORY.md only updates when fleet agent or Heather manually updates it. Use config from Finding 24; verify key path (Finding 36); add `userTimezone` first (Finding 28).

---

## ⭐ Finding 21 — MEMORY.md Size Monitoring

MEMORY.md now ~12,000 bytes. Monitor growth. Limit: ~20,000 chars before noticeable context budget impact.

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

## ⭐ Finding 4 — No Memory Protection Before Compaction (Day 98)

**Risk: HIGH** — add to openclaw.json:
```json
"compaction": {
  "reserveTokensFloor": 40000,
  "memoryFlush": { "enabled": true, "softThresholdTokens": 4000 }
},
"contextPruning": { "mode": "cache-ttl", "ttl": "6h" }
```

---

## ⭐ Finding 2 — Google Workspace Not Connected (Day 98 — CRITICAL, Day 100 in 2 days)

No Google OAuth connected. Gmail, Calendar, Contacts all inaccessible. Three of five heartbeat checks permanently blocked. **Day 100 arrives June 29.**
1. AlphaClaw UI: https://5.78.142.81.sslip.io#general → Google Workspace → OAuth
2. Full steps in workspace/memory/onboarding-google.md
3. Alternative: Remote MCP via AlphaClaw 0.9.18 Envars tab (see F60)

---

## Summary Table (Updated June 27 Morning)

| Finding | Priority | Status |
|---------|----------|--------|
| **F59. 2026.6.10 CLI features (compaction, dry-run, etc.)** | MEDIUM | 🔬 Available post-upgrade |
| **F60. AlphaClaw 0.9.18 confirmed current; Remote MCP path** | INFO + MEDIUM | ✅ Confirmed; path documented |
| **F61. Google Workspace Day 98 — Day 100 in 2 days** | **CRITICAL** | ⏳ Escalation framing ready for Heather |
| **F62. Noah trading ecosystem intelligence** | HIGH (when scope fixed) | ⏳ Hold for scope fix |
| **F53. PR #96233 heartbeat prompt fix in 2026.6.10** | HIGH | ⏳ Fixed on upgrade |
| **F56. 2026.6.10 Day 4 stable** | HIGH (POSITIVE) | ✅ 96h clean — proceed confidently |
| **F57. Google OAuth: Day 98 — 2 days to Day 100** | **CRITICAL** | ⏳ Fix NOW — 5 min task |
| **F58. Noah Day 18 — scope fix pending** | **FLEET OPS** | ⏳ Fix scope |
| **F50. 2026.6.11-beta.1: per-DM overrides, file workflows** | INFO | 🔬 Monitor — stable TBD |
| F51. HEARTBEAT.md stale 2026.6.9 ref | LOW | ✅ Fixed June 25 |
| F52. Gemini sister models shut June 25 — primary safe | INFO | ✅ Confirmed June 25 |
| F47. 2026.6.10 stable — skip 2026.6.9 | HIGH | ✅ Upgrade target updated (see F56) |
| F48. F43 downgraded — gemini-3-flash-preview NOT deprecated | CORRECTION | ✅ Downgraded to MEDIUM-HIGH |
| F49. Noah Day 18 + Alpaca MCP v2 gap | FLEET OPS | ⏳ Fix scope (see F58/F62) |
| F43. Gemini sister models shut down June 25 | MEDIUM-HIGH | ✅ Primary model safe (per F48/F52) |
| F45. SkillSpector standard on ClawHub | POSITIVE | ✅ Auto post-upgrade |
| F39. Discord Components V2 post-upgrade | INFO | 🔬 Post-upgrade capability |
| F40. Group chat context every turn | INFO | 🔬 Auto in 2026.6.10 |
| 34. AlphaClaw git sync fix | POSITIVE | ✅ Auto-applied |
| 32. iMessage SQLite migration auto-fix | POSITIVE | ✅ Confirmed — no action needed |
| 35. AlphaClaw in-app update removed | INFO | VPS-only path confirmed |
| 36. Dreaming config key path | LOW | Verify before applying |
| 31. Same-provider fallback chain gap | MEDIUM | ⏳ Fix with F48 model migration |
| 30. BRAVE_API_KEY not set | MEDIUM-HIGH | ⏳ Fix anytime (AlphaClaw Envars tab) |
| 29. **2026.6.10-stable — Day 4 of window** | HIGH | ⏳ Upgrade window FULLY OPEN |
| 2. Connect Google Workspace | CRITICAL | ⏳ Day 98 — Day 100 in 2 days |
| 27. Heartbeat cron not deployed — Day 14+ | HIGH | ⏳ Bundle with upgrade |
| 28. userTimezone not set | MEDIUM-HIGH | ⏳ Bundle with upgrade |
| 22/24. Enable Dreaming | HIGH | ⏳ Bundle with upgrade |
| 4. Add compaction/memoryFlush | HIGH | ⏳ Bundle with upgrade |
| Upgrade to 2026.6.10 (staged, skip 2026.6.8 + 6.9) | HIGH | ⏳ WINDOW OPEN — Day 4 |
| 20. Discord security (open → allowlist) | MEDIUM-HIGH | ⏳ After upgrade |
| 26. 2026.6.8 + 2026.6.9 skip confirmed | INFO | ✅ Skip both confirmed |
| 23. AlphaClaw 0.9.18 features | INFO | ✅ Confirmed current (June 1) |
| 25. ClawHavoc skill audit | LOW | No skills installed — safe |

---

## Remaining Open Action List (June 27 Morning)

### Can do NOW — AlphaClaw UI only (no VPS access needed)
1. **[CRITICAL]** Connect Google Workspace OAuth → https://5.78.142.81.sslip.io#general (Day 98 — Day 100 in 2 days)
2. **[MEDIUM-HIGH]** Set BRAVE_API_KEY in AlphaClaw UI → Envars tab (Finding 30)
3. **[MEDIUM-HIGH]** Migrate primary model: `gemini-3-flash-preview` → `gemini-3.5-flash` (F48 + F31)
   Browse tab → openclaw.json → edit model block → save → gateway restart
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
4. **[HIGH]** Add `userTimezone: "America/Los_Angeles"` to `agents.defaults` (Finding 28 — FIRST)
5. **[HIGH]** Add `compaction/memoryFlush` block (Finding 4)
6. **[HIGH]** Verify dreaming key path (Finding 36), add dreaming config (Finding 22/24)
7. **[HIGH]** Add heartbeat cron job to `cron.jobs` (Finding 27)
8. **[HIGH]** Run staged upgrade: 2026.3.22 → 2026.5.27 → 2026.6.2 → 2026.6.5 → 2026.6.6 → 2026.6.10
   - Verify first: `npm show openclaw@latest version` = `2026.6.10`
   - Day 4 of stable — green light, proceed confidently

### After upgrade to 2026.6.10
9. **[MEDIUM-HIGH]** Tighten Discord allowFrom: `["*"]` → Josh's Discord user ID (Finding 20)
10. **[LOW]** Enable Discord streaming: `"streaming": "progress"`
11. **[LOW]** Enable auto-thread titles

### Fleet operations
12. **[FLEET OPS]** Fix Noah session scope: noah--repo (404) → Noahrepo2 (Day 18)

---

*Sources: [OpenClaw Releases](https://github.com/openclaw/openclaw/releases) · [ClawStat.us](https://clawstat.us/) · [Google Gemini Deprecations](https://ai.google.dev/gemini-api/docs/deprecations) · [OpenClaw Cron Docs](https://docs.openclaw.ai/automation/cron-jobs) · [AlphaClaw GitHub](https://github.com/chrysb/alphaclaw) · [Alpaca MCP Server v2](https://alpaca.markets/blog/alpaca-launches-mcp-server-v2/) · [OpenRouter OpenClaw Guide](https://openrouter.ai/blog/tutorials/openclaw-openrouter/) · [ClawHavoc Security](https://thehackernews.com/2026/02/researchers-find-341-malicious-clawhub.html) · [Clawbot.ai Alpaca Skill](https://clawbot.ai/skills/alpaca.html)*
