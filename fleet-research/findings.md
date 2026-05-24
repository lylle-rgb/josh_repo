# Fleet Research Findings — Josh (Heather Schwartz)
**Date:** 2026-05-24 (Evening Scan)
**Analyst:** AlphaClaw Apex Fleet Manager
**Repo:** lylle-rgb/josh_repo
**Bot:** Heather Schwartz — Personal Assistant (Discord, iMessage, email, calendar)

---

## CRITICAL — Fix Now (No Restart Required)

### [JOSH-C1] MEMORY.md Does Not Exist — 34+ Sessions of Context Lost
**Risk:** CRITICAL | **Effort:** 5 min | **Upgrade Required:** No

Every session, AGENTS.md instructs Heather to read `workspace/MEMORY.md`. The file has never been created. 34+ sessions of accumulated context — Josh's preferences, integration states, known issues, business context — are re-derived from scratch every single session.

**Fix:** Create `workspace/MEMORY.md`. See `soul-improvements.md` for exact seeded content.

---

### [JOSH-C2] Dead Fallback Model — Silent Errors on Every Fallback
**Risk:** CRITICAL | **Effort:** 3 min | **Upgrade Required:** No

`openclaw.json` specifies `openrouter/anthropic/claude-3.5-haiku` as a fallback. This model has been retired. Every fallback attempt silently fails.

**Before:**
```json
"fallbacks": ["openrouter/google/gemini-2.5-flash", "openrouter/anthropic/claude-3.5-haiku"]
```

**After:**
```json
"fallbacks": ["openrouter/google/gemini-3.1-flash-lite-preview", "openrouter/google/gemini-2.5-flash", "openrouter/anthropic/claude-haiku-4-5"]
```

---

### [JOSH-C3] False Google Auth Statement Poisons Every Session
**Risk:** CRITICAL | **Effort:** 2 min | **Upgrade Required:** No

`workspace/hooks/bootstrap/TOOLS.md` ends with "No Google accounts are currently configured." This is false — `google:default` is connected. Heather starts every session believing she has no Gmail or Calendar access, which is the likely root cause of her failure to use Google tools proactively.

**Fix — replace the `## Available Google Accounts` section with:**
```markdown
## Available Google Accounts

Josh's Google Workspace is connected via api_key mode (profile: google:default).
Available services: Gmail, Calendar, Drive, Contacts, Tasks.
Use `--client google --account default` for all gog commands.
Note: gmailWatch (push inbox monitoring) is not yet enabled — use polling via gog for now.
```

---

## HIGH PRIORITY

### [JOSH-H1] OpenClaw 2 Months Behind — v2026.5.22 Released TODAY
**Risk:** HIGH | **Effort:** 30 min | **Upgrade Required:** Yes

Current version: `2026.3.22`. Latest stable: `2026.5.22` (released 2026-05-24). Notable losses:
- Discord final-message delivery fixes
- Streaming progress drafts (`streaming: "progress"`) — bot appears frozen during long tasks
- Multi-turn voice continuity
- Discord voice channel-following + Meeting Notes plugin (auto-captures voice sessions)
- Policy plugin for per-scope tool permissions
- AlphaClaw 0.9.15 config restoration on fresh boot
- AlphaClaw 0.9.16 file tree lazy-loading
- Model listing: 20s → 5ms via pre-warmed auth-state (4,100x improvement)
- Tool-call delta buffering fix for parallel multi-tool turns

**Pre-upgrade backup:** `cp /data/.openclaw/openclaw.json /data/.openclaw/openclaw.json.bak-pre-5.22`
**Upgrade:** `openclaw upgrade` on VPS. Test Discord connectivity after.

---

### [JOSH-H2] HEARTBEAT.md Is Empty — Zero Proactive Monitoring
**Risk:** HIGH | **Effort:** 5 min | **Upgrade Required:** No

`workspace/HEARTBEAT.md` contains only 3 comment lines. Every heartbeat cycle fires, reads this file, and returns `HEARTBEAT_OK`. Heather performs zero proactive monitoring of Gmail, Google Calendar, or iMessage bridge status. Josh receives no proactive alerts between messages he initiates.

This is the core function of a personal assistant on a heartbeat system.

**Fix:** See `soul-improvements.md` for exact HEARTBEAT.md content with Gmail urgency checks (throttled to every 2h), calendar look-ahead alerts (<2h), iMessage bridge status monitoring (once/day), and quiet hours (23:00–08:00 PST).

---

### [JOSH-H3] SOUL.md Has Never Been Personalized
**Risk:** HIGH | **Effort:** 10 min | **Upgrade Required:** No

`workspace/SOUL.md` SHA `792306ac60f6c600b8ded97899354557ce900f40` is byte-for-byte identical to the upstream OpenClaw generic template. After 34+ days, not a single Josh/Heather-specific word appears in the soul file. The soul file shapes every interaction.

Gap: none of the following appears anywhere in SOUL.md: "Heather," "Bliss," "Oben," "emoji," "luxury," "LA," "Josh."

**Fix:** Append personalization section. See `soul-improvements.md` for exact content.

---

### [JOSH-H4] Emoji Contradiction — USER.md Overridden by AGENTS.md Every Session
**Risk:** HIGH | **Effort:** 5 min | **Upgrade Required:** No

`workspace/USER.md`: *"STRICT: DO NOT SEND EMOJI REACTIONS TO MESSAGES."*
`workspace/AGENTS.md`: Full "React Like a Human!" section actively instructing emoji reactions (👍 ❤️ 😂 🤔 ✅).

Both load every session. Whether Josh gets emoji reactions depends on which instruction wins in a given context window — inherently unpredictable. Josh's explicit preference must always win.

**Fix:** Add `## User Preference Overrides` section to SOUL.md. See `soul-improvements.md`.

---

## MEDIUM PRIORITY

### [JOSH-M1] iMessage Monitoring Paused 28+ Days — Malformed JSON State File
**Risk:** MEDIUM | **Effort:** 2 min | **Upgrade Required:** No

`workspace/memory/inbox-state.json` has two problems:
1. `imessage_monitoring_paused: true` since ~2026-04-26 with no automated alert to Josh
2. Duplicate key `last_email_check_ms` — invalid JSON; strict parsers fail entirely

**Fixed JSON:**
```json
{
  "already_drafted_imessage_guids": [],
  "already_drafted_thread_ids": ["19db60d96d2118c8"],
  "imessage_monitoring_paused": true,
  "last_email_check_ms": 1777551900000,
  "last_imessage_check_ms": 1777271400000
}
```

Investigate bridge status before re-enabling. HEARTBEAT.md fix should include a daily iMessage paused-state alert.

---

### [JOSH-M2] IDENTITY.md Incomplete — Missing Surname and Avatar
**Risk:** LOW | **Effort:** 2 min | **Upgrade Required:** No

`workspace/IDENTITY.md` lists "Heather" but the bot is documented throughout as "Heather Schwartz." Surname missing. Avatar blank. Creature field is generic.

**Fix:**
```markdown
# IDENTITY.md
- **Name:** Heather Schwartz
- **Creature:** AI personal assistant
- **Vibe:** Sharp, helpful, resourceful — gets things done without being asked twice
- **Emoji:** 🫡
- **Avatar:** (set when available)
```

---

### [JOSH-M3] TOOLS.md Is a Template — No Environment Specifics
**Risk:** MEDIUM | **Effort:** 5 min | **Upgrade Required:** No

`workspace/TOOLS.md` is 100% boilerplate. No Google profile, no Discord IDs, no iMessage state, no AlphaClaw URL. Heather re-discovers her own setup every session.

**Fix:** See `soul-improvements.md` for exact content.

---

### [JOSH-M4] No contextPruning Configured — Token Debt Accumulates Silently
**Risk:** MEDIUM | **Effort:** 2 min | **Upgrade Required:** No

No `contextPruning` block in `openclaw.json`. Context grows unbounded until model hits window limit and compaction fires — disruptive, response quality degrades.

**Fix — add to `agents.defaults` in `openclaw.json`:**
```json
"contextPruning": {
  "mode": "cache-ttl",
  "ttl": "35m",
  "keepLastAssistants": 3
}
```

---

## LOW PRIORITY

### [JOSH-L1] BOOTSTRAP.md Should Be Deleted
**Risk:** LOW | **Effort:** 1 min | **Upgrade Required:** No

`workspace/BOOTSTRAP.md` still exists after 34+ days. Its own instructions say to delete it after onboarding. Consumes tokens and could confuse a fresh session into re-onboarding.

---

### [JOSH-L2] Discord Streaming Disabled — Bot Appears Frozen During Long Tasks
**Risk:** LOW | **Effort:** 1 min | **Upgrade Required:** Yes

`openclaw.json` has `"streaming": "off"`. During long tasks (email search, calendar lookup, research), Heather goes completely silent until done.

**Fix (post-upgrade to 2026.5.22):**
```json
"channels": { "discord": { "streaming": "progress" } }
```

---

### [JOSH-L3] No Cron Configuration Exists
**Risk:** LOW | **Effort:** 10 min | **Upgrade Required:** Yes (stable isolated sessions in 2026.5.x)

No `cron/jobs.json`. Morning briefing and evening digest are obvious wins for a personal assistant. See `soul-improvements.md` for exact cron config. Stagger from fleet-wide :00 marks — use :07 and :13 offsets to avoid rate-limit collisions.

---

## WEB RESEARCH FINDINGS — Platform Updates & Opportunities

### OpenClaw 2026.5.22 — Released Today (2026-05-24)
Key new capabilities:
- **Model listing: 20s → 5ms** via provider auth-state pre-warming (4,100x improvement)
- **Meeting Notes plugin** — auto-captures Discord voice sessions into structured notes
- **Gateway lazy-loading** — startup no longer blocked by unused plugin handler trees
- **Sub-agent bootstrap limits** — sub-agents receive only AGENTS.md + TOOLS.md by default (reduces token bloat)
- **Tool-call delta buffering** — parallel tool-call arguments no longer corrupt each other
- **Session archive failure surfacing** — silent transcript rotation failures now visible

### AlphaClaw 0.9.16 — Released May 15, 2026
- File tree depth capping + lazy-loading for large workspaces
- Better browser responsiveness in admin UI
- OpenClaw pinned to 2026.5.6 in this release — manual pin to 2026.5.22 recommended

### Dreaming Plugin (v2026.4.5+)
Background memory consolidation — Light/REM/Deep sleep phases. Directly addresses the MEMORY.md gap by automatically consolidating session notes into long-term memory. Pairs with a proper MEMORY.md file for maximum effectiveness.
- Install post-upgrade: `openclaw plugin install dreaming`
- Configure REM phase for 02:00 PST nightly

### Lossless-Claw (LCM) Plugin (v2026.3.7+)
SQLite-backed DAG of summaries. Agents run thousands of turns without losing history. Replaces built-in context compression.
- Source: github.com/Martian-Engineering/lossless-claw

### QMD Semantic Memory Mode
Graph+vector hybrid retrieval (up from keyword-only). +29.6pt improvement on temporal queries, +23.1pt on multi-hop reasoning. Enable in QMD config post-upgrade.

### Discord: Voice Follow + Meeting Notes (v2026.5.20/5.22)
Bot can follow configured users into voice channels. Multi-user handoff supported. Meeting Notes plugin auto-captures sessions. Useful if Josh uses Discord voice.

### iMessage: Poke (April 2026, $15M seed)
Purpose-built iMessage AI assistant. Not self-hosted — OpenClaw native iMessage channel remains the recommended path. Monitor for integration opportunities.

---

## Summary Table

| ID | Finding | Priority | Effort | Upgrade? |
|---|---|---|---|---|
| JOSH-C1 | MEMORY.md missing — 34+ sessions lost | CRITICAL | 5 min | No |
| JOSH-C2 | Dead fallback model | CRITICAL | 3 min | No |
| JOSH-C3 | False Google auth statement | CRITICAL | 2 min | No |
| JOSH-H1 | OpenClaw 2 months behind (5.22 out TODAY) | HIGH | 30 min | Yes |
| JOSH-H2 | HEARTBEAT.md empty — zero proactive monitoring | HIGH | 5 min | No |
| JOSH-H3 | SOUL.md never personalized | HIGH | 10 min | No |
| JOSH-H4 | Emoji contradiction USER.md vs AGENTS.md | HIGH | 5 min | No |
| JOSH-M1 | iMessage paused + malformed JSON | MEDIUM | 2 min | No |
| JOSH-M2 | IDENTITY.md missing surname | LOW | 2 min | No |
| JOSH-M3 | TOOLS.md is blank template | MEDIUM | 5 min | No |
| JOSH-M4 | No contextPruning configured | MEDIUM | 2 min | No |
| JOSH-L1 | BOOTSTRAP.md not deleted | LOW | 1 min | No |
| JOSH-L2 | Discord streaming off | LOW | 1 min | Yes |
| JOSH-L3 | No cron jobs | LOW | 10 min | Yes |

**No-upgrade fixes (C1–C3, H2–H4, M1–M4, L1): ~34 minutes total effort. All documented for 14–34 days. Zero implementations to date.**

---

*Fleet scan by AlphaClaw Apex Fleet Manager — 2026-05-24 Evening*
