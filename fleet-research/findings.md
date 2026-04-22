# Fleet Research Findings — Josh / Heather Schwartz

> Morning scan: 2026-04-21 | Evening update: 2026-04-22 | Agent: AlphaClaw Fleet Research

---

## EVENING SCAN — 2026-04-22

### Morning Findings Status Check

None of the morning findings have been applied yet. All 6 items remain pending as of the evening scan.

| # | Finding | Status |
|---|---------|--------|
| 1 | Update OpenClaw 2026.3.22 → 2026.4.14 | ⏳ Pending |
| 2 | Install memory-lancedb | ⏳ Pending |
| 3 | Enable Discord streaming | ⏳ Pending |
| 4 | Add cron.json morning briefing | ⏳ Pending |
| 5 | Upgrade fallback model (claude-3.5-haiku → claude-haiku-4-5) | ⏳ Pending |
| 6 | Fill in TOOLS.md | ⏳ Pending |

---

### Evening Finding E1: MEMORY.md Missing — Continuity Broken

**Finding:** No `workspace/MEMORY.md` file exists. The `memory/` directory contains only `memory/onboarding-google.md` from initial setup. No daily session logs (`memory/YYYY-MM-DD.md`) exist either.

**Why it matters:** AGENTS.md explicitly instructs Heather to read `MEMORY.md` at session startup for long-term context — but the file doesn't exist. Every session starts completely cold regardless of what's been learned in prior sessions. The memory directory is present but effectively unused.

**Action:** In the next live session, prompt Heather to:
1. Read `memory/onboarding-google.md`
2. Create `workspace/MEMORY.md` with distilled facts from onboarding (Josh's name, timezone, businesses, no emoji reactions preference)
3. Create today's daily log `memory/2026-04-22.md`

**Risk:** None. High impact.

---

### Evening Finding E2: SOUL.md ↔ USER.md Contradiction — Emoji Reactions (Active Bug)

**Finding:** A direct behavioral contradiction exists between two files loaded at bootstrap:

- **USER.md:** `"STRICT: DO NOT SEND EMOJI REACTIONS TO MESSAGES."`
- **AGENTS.md:** Has a full section "React Like a Human!" explicitly instructing Heather to use emoji reactions on Discord (👍, ❤️, 😂, etc.)

**Why it matters:** Both files are loaded at every session bootstrap. These instructions directly conflict. Heather's behavior on emoji reactions is currently unpredictable — she may react to Josh's messages despite his explicit request not to. This is an active UX bug.

**Action:** Add an explicit override to SOUL.md. See `soul-improvements.md` for the exact recommended text.
**Risk:** Medium — active behavioral conflict affecting user experience today.

---

### Evening Finding E3: Active Memory Plugin Available (2026.4.15 Beta)

**Finding:** OpenClaw 2026.4.15 beta introduces a dedicated **Active Memory Plugin** — a memory sub-agent with configurable context modes (`message`, `recent`, `full`). Unlike basic LanceDB (embedding retrieval), the sub-agent actively decides what past context is relevant to surface during each conversation.

**Why it matters for Heather:** Personal assistant is the highest-value use case for this. The sub-agent proactively surfaces things like "Josh mentioned the Oben HiFi board meeting is this week" during relevant calendar conversations — without requiring explicit retrieval triggers.

**Action:** Install `memory-lancedb` first (morning finding #2, still pending). Watch for 2026.4.15 stable release to evaluate Active Memory Plugin as an upgrade.
**Risk:** None — future upgrade path.

---

### Evening Finding E4: Plugin Storage Path — Memory May Not Survive Restarts

**Finding:** A recent AlphaClaw managed fix now correctly exports `OPENCLAW_STATE_DIR` through startup so plugins write durable artifacts to `/data/.openclaw` instead of ephemeral `/tmp`. Older deployments may not have received this fix properly.

**Why it matters:** If memory-lancedb is installed without explicit path configuration, it may write indexes to `/tmp` — wiped on every container restart. Memory would appear to work but silently reset after every host restart or Railway redeploy.

**Action:** When installing memory-lancedb, explicitly configure the storage path:
```json
"memory-lancedb": {
  "config": {
    "autoRecall": true,
    "autoCapture": true,
    "storagePath": "/data/.openclaw/memory"
  }
}
```
Ensure AlphaClaw is on the latest managed version (self-updates handle the OPENCLAW_STATE_DIR fix — no manual step needed).
**Risk:** Low — only relevant once memory-lancedb is installed.

---

## MORNING SCAN — 2026-04-21

## 1. OpenClaw Version — UPDATE NEEDED

**Finding:** Running `2026.3.22`. Latest stable is `2026.4.14`; beta `2026.4.15` is also available.

**Why it matters for Heather:** Version 2026.4.x includes a critical cron delivery regression fix (Discord cron jobs broke with "Unknown Channel" errors on some 2026.4.x builds and were resolved), the new Model Auth Status card so you can see OAuth health at a glance, and Gemini TTS support (useful for future voice reply features).

**Action:**
```bash
npm install -g openclaw@latest
# or via alphaclaw wizard:
alphaclawctl update
```
**Risk:** Low. No breaking config changes in 2026.3 → 2026.4 for Discord setups.

---

## 2. Memory Plugin — NOT CONFIGURED (workspace/memory/ dir exists but unused)

**Finding:** `workspace/memory/` directory is present but no `memory-lancedb` plugin is installed or enabled in `openclaw.json`. All of Heather's learned context about Josh (preferences, ongoing projects, contacts) evaporates between restarts.

**Why it matters:** Josh's use case (personal assistant, iMessage, email, calendar) is the highest-value memory use case on the fleet. Heather needs to remember things like email preferences, calendar habits, recurring contacts, and project context across sessions.

New in `2026.4.15` beta: `memory-lancedb` now supports **cloud/remote object storage** — meaning memory survives host restarts and migrations.

**Action (install memory plugin):**
```bash
npm install @openclaw/plugin-memory-lancedb
```

Add to `openclaw.json`:
```json
"plugins": {
  "allow": ["discord", "usage-tracker", "memory-lancedb"],
  "entries": {
    "memory-lancedb": {
      "enabled": true,
      "config": {
        "autoRecall": true,
        "autoCapture": true,
        "storagePath": "/data/.openclaw/memory"
      }
    }
  }
}
```
**Risk:** Low. Additive change, no existing config disrupted.

---

## 3. Discord Streaming — DISABLED

**Finding:** `channels.discord.streaming: "off"` — responses are sent as single blocks.

**Why it matters:** With streaming enabled, Josh sees Heather "typing" in real-time, making the interaction feel much more natural and responsive. This is especially noticeable for longer responses (calendar summaries, email drafts).

**Action:** In `openclaw.json`:
```json
"channels": {
  "discord": {
    "streaming": "partial"
  }
}
```
**Risk:** Very low. Can revert instantly.

---

## 4. No Cron Automation — OPPORTUNITY

**Finding:** No `workspace/cron.json` exists. Heather has no proactive scheduled actions.

**Why it matters:** A personal assistant should push information to Josh proactively — not just respond reactively. Josh is a founder/CEO in LA (PST), so timed briefings add significant value.

**Suggested starter `workspace/cron.json`:**
```json
{
  "jobs": [
    {
      "name": "morning_briefing",
      "schedule": "0 8 * * 1-5",
      "task": "Send Josh a morning briefing: today's calendar events, any unread priority emails (flag subject lines), and 1-2 things worth knowing. Keep it tight. DM to Josh on Discord."
    },
    {
      "name": "friday_week_wrap",
      "schedule": "0 16 * * 5",
      "task": "Send Josh a short end-of-week summary: what was accomplished, anything still open, what's on next week. DM to Josh on Discord."
    }
  ]
}
```
**Risk:** Medium. Test the first cron manually with a `/cron run morning_briefing` before enabling. Ensure email/calendar tools are connected.

---

## 5. Fallback Model Upgrade

**Finding:** OpenRouter fallback is `openrouter/anthropic/claude-3.5-haiku` — this model is retired/old. The current equivalent is `claude-haiku-4-5-20251001`.

**Action:** In `openclaw.json` `agents.defaults.model.fallbacks`:
```json
"fallbacks": [
  "openrouter/google/gemini-2.5-flash",
  "openrouter/anthropic/claude-haiku-4-5-20251001"
]
```
**Risk:** Low. Fallbacks only activate when primary fails.

---

## 6. TOOLS.md — Blank Template

**Finding:** `workspace/TOOLS.md` contains only the boilerplate example. No actual tool notes for Heather's setup.

**Why it matters:** TOOLS.md is injected at bootstrap. A blank file wastes context tokens and provides no actual guidance. Heather should have notes on Josh's iMessage contacts aliases, email account names, calendar IDs, etc.

**Action:** Heather (or Josh) should populate TOOLS.md during a session with specifics like:
- Preferred email account labels
- Calendar names/IDs
- Common contacts and their relationship to Josh
- Any SSH or home automation endpoints

**Risk:** None.

---

## Priority Summary (Updated Evening)

| # | Item | Impact | Risk | Effort |
|---|------|--------|------|--------|
| E1 | Create MEMORY.md with seed data | **High** — fixes broken continuity | None | 15 min |
| E2 | Fix emoji reaction contradiction (SOUL.md) | **High** — active behavioral bug | None | 5 min |
| 1 | Install memory-lancedb plugin | **High** — core reliability for personal assistant | Low | 15 min |
| 2 | Add cron.json morning briefing | **High** — transforms reactive → proactive | Medium | 30 min |
| 3 | Enable Discord streaming | Medium — UX quality | Very Low | 5 min |
| 4 | Update OpenClaw to 2026.4.14 | Medium — bug fixes, stability | Low | 10 min |
| 5 | Upgrade fallback model | Low — only hits on failures | Low | 5 min |
| 6 | Fill in TOOLS.md | Low → Medium over time | None | Ongoing |
