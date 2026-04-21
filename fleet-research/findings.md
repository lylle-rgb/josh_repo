# Fleet Research Findings — Josh / Heather Schwartz

> Scan date: 2026-04-21 | Agent: AlphaClaw Fleet Research

---

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
# In alphaclaw shell or workspace
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
        "autoCapture": true
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

**Suggested starter cron.json** (`workspace/cron.json`):
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

**Finding:** OpenRouter fallback is `openrouter/anthropic/claude-3.5-haiku` — this model is retired/old. The current equivalent is `claude-haiku-4-5`.

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

## Priority Summary

| # | Item | Impact | Risk | Effort |
|---|------|--------|------|--------|
| 1 | Install memory-lancedb plugin | **High** — core reliability for personal assistant | Low | 15 min |
| 2 | Add cron.json morning briefing | **High** — transforms reactive → proactive | Medium | 30 min |
| 3 | Enable Discord streaming | Medium — UX quality | Very Low | 5 min |
| 4 | Update OpenClaw to 2026.4.14 | Medium — bug fixes, stability | Low | 10 min |
| 5 | Upgrade fallback model | Low — only hits on failures | Low | 5 min |
| 6 | Fill in TOOLS.md | Low → Medium over time | None | Ongoing |
