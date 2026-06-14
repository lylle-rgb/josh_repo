# Fleet Research Findings — Josh / Heather Schwartz

**Scan date:** 2026-06-14 (evening) · Previous scan: 2026-06-12 (morning)
**Researcher:** AlphaClaw Fleet Agent
**Instance:** josh_repo (Heather Schwartz — personal assistant)
**Current version:** 2026.3.22
**Latest stable:** 2026.6.5 (June 9, 2026)
**Latest beta:** 2026.6.6-beta.1 (June 10, 2026)

> ⚠️ All 9 findings from the June 12 scan remain unresolved. Findings 10–12 are new this evening.

---

## Finding 1 — Version Outdated (3 Months Behind)

**Risk: HIGH**

Heather is running OpenClaw `2026.3.22`. The current stable is `2026.6.5` and a beta `2026.6.6-beta.1` dropped June 10. That's a 3-month gap with ~8 releases in between.

**Why it matters for Heather:**
Several fixes in this window directly affect the personal assistant use case:
- **iMessage recovery** (2026.6.5): Private-API failures and send timeouts now explain themselves; split-send coalescing honors balloon metadata. Silent iMessage failures are likely this bug.
- **Parallel web search bundled** (2026.6.5): Web search is now a first-class built-in; no separate setup required.
- **MCP tool result coercion** (2026.6.5): Non-text/image MCP blocks no longer poison session history with errors.
- **Cron state bug** (prior releases): Cron state was wiped during a SQLite migration — any scheduled reminders or tasks may have been silently lost.
- **Model override drop on idle rollover** (prior releases): User model overrides were dropped on daily session rollover — fixed.

**Action:**
```bash
openclaw update
```
Or via the AlphaClaw Watchdog tab: `https://5.78.142.81.sslip.io#watchdog`

---

## Finding 2 — Google Workspace Not Connected (Critical Gap)

**Risk: CRITICAL**

The bootstrap TOOLS.md shows no Google accounts configured. Heather's entire value proposition is managing Josh's iMessage, email, and calendar. Without Google Workspace, she cannot access Gmail, Google Calendar, or Google Contacts.

**Why it matters:**
Josh is a Founder/CEO (Bliss, Oben HiFi) based in LA. Email and calendar management for a founder is high-value and time-sensitive. A personal assistant who can't access the calendar or inbox is severely limited.

**Action:**
1. Go to AlphaClaw UI: `https://5.78.142.81.sslip.io#general`
2. Under Google Workspace, provide OAuth client credentials from Google Cloud Console
3. Authorize: Gmail, Google Calendar, Google Contacts (minimum); Drive and Tasks recommended
4. Confirm the account appears in TOOLS.md

---

## Finding 3 — Concurrent Web Search Bug (Gemini-3-Flash)

**Risk: MEDIUM**

A known OpenClaw issue (#30675) affects `google/gemini-3-flash-preview`: when a subagent fires multiple parallel `web_search` calls in one turn, subsequent calls fail silently with `missing_gemini_api_key`. Research tasks may return incomplete answers without surfacing an error.

**Action:**
Update to 2026.6.5 first (Finding 1). If it persists, add to `openclaw.json` under `agents.defaults`:
```json
"webSearch": {
  "maxConcurrentCalls": 1
}
```

---

## Finding 4 — No Memory Protection Before Compaction

**Risk: HIGH**

Josh's `openclaw.json` has no compaction settings. Without `memoryFlush`, OpenClaw does not trigger a memory-write turn before compaction. When Heather's session hits the context limit, everything from that session is silently lost.

Noah's instance (for comparison) has:
```json
"compaction": {
  "reserveTokensFloor": 40000,
  "memoryFlush": {
    "enabled": true,
    "softThresholdTokens": 4000
  }
}
```

**Why it matters:**
For a personal assistant, continuity is the product. If Heather forgets what Josh told her last session, the relationship breaks down.

**Action:**
Add to `openclaw.json` under `agents.defaults`:
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
The 6h TTL is appropriate for a personal assistant. Noah's 5m TTL is too aggressive for this use case.

---

## Finding 5 — TOOLS.md Is a Blank Template

**Risk: LOW**

`workspace/TOOLS.md` contains only the default placeholder text — no actual device names, SSH aliases, or environment notes. Heather must ask or guess about Josh's setup every session.

**Action:**
Populate `workspace/TOOLS.md` with Josh's devices, any SSH aliases, preferred communication format preferences, and shortcuts for frequently mentioned places or people.

---

## Finding 6 — Discord Streaming Disabled

**Risk: LOW**

`openclaw.json` has `"streaming": "off"`. Heather's replies appear all at once after full generation, which feels slow for longer responses.

**Action:**
Change `"streaming": "off"` → `"streaming": "on"` in `openclaw.json`.

---

## Finding 7 — Dreaming (Memory Consolidation) Not Enabled

**Risk: HIGH**

OpenClaw's optional "Dreaming" feature runs a background memory consolidation pass on a configurable schedule. It scans recent daily memory files, scores entries for significance, and promotes only items that pass score, recall-frequency, and query-diversity gates into MEMORY.md. It is disabled by default.

**Why it matters for Heather:**
Heather's AGENTS.md explicitly says to periodically review daily memory files and update MEMORY.md with distilled learnings. Right now, this requires Heather to manually spend a heartbeat turn on it. Dreaming automates this completely — running nightly at 3 AM, keeping MEMORY.md high-signal without burning any active session tokens.

Currently there is no MEMORY.md at all. Both need to be set up together for long-term memory to work.

**Action:**

Step 1 — Create `workspace/MEMORY.md`:
```markdown
# MEMORY.md — Heather's Long-Term Memory

Last updated: (maintained by Dreaming)

## About Josh
- Full name: Joshua Meyers
- Titles: Founder & CEO @blisslifestyleofficial, Partner @obenhifi
- Location: Los Angeles (PST/PDT)
- Strict preference: No emoji reactions to messages
- Named me Heather

## Ongoing Projects
_(Dreaming fills this in from daily memory files)_

## Preferences Discovered
_(Dreaming fills this in over time)_

## Hard Rules
_(Things Heather must never forget — updated manually)_
```

Step 2 — Enable Dreaming in `openclaw.json` under `agents.defaults`:
```json
"dreaming": {
  "enabled": true,
  "schedule": "0 3 * * *",
  "maxPromotion": 10,
  "minScore": 0.7
}
```
This runs at 3 AM nightly and promotes up to 10 high-significance items into MEMORY.md.

---

## Finding 8 — HEARTBEAT.md Not Populated

**Risk: MEDIUM**

`workspace/HEARTBEAT.md` contains only the template placeholder. Heather returns `HEARTBEAT_OK` on every heartbeat poll without doing any proactive work. The entire proactive assistant pipeline — email urgency checks, calendar alerts, weather — is completely dormant.

AGENTS.md contains detailed guidance on what to check: emails, calendar (<2h events), mentions, weather, rotated 2-4x per day. None of this runs because HEARTBEAT.md is empty.

**Action:**
Replace `workspace/HEARTBEAT.md` with:
```markdown
# HEARTBEAT.md — Heather's Proactive Checks

Rotate through the checks below, 2-4x per day. Pick 1-2 per heartbeat, most overdue first.
Track state in: memory/heartbeat-state.json

## Checks

- **Email scan** — Check inbox for unread messages. Flag urgent or actionable items.
  Alert Josh in Discord if something important arrived.
- **Calendar check** — Look for events in the next 24-48h.
  Alert if anything is <2h away and Josh hasn't mentioned it.
- **Weather** — Check LA weather if Josh might go out today.

## Quiet hours
Do not message Josh between 23:00-08:00 PST unless urgent.

## Proactive background work (do silently, no message needed)
- Organize memory files
- Review and update MEMORY.md if Dreaming hasn't run recently
- Commit workspace changes to git
```

---

## Finding 9 — 2026.6.6-beta.1 Available (Monitor)

**Risk: INFO**

`2026.6.6-beta.1` dropped June 10 and has not yet promoted to stable as of this scan (June 14). No action needed — update to 2026.6.5 now (Finding 1) and watch for the 2026.6.6 stable announcement.

---

## Finding 10 — AGENTS.md Emoji Rule Contradicts USER.md Strict Preference ⭐ NEW 2026-06-14 Evening

**Risk: HIGH**

This is a direct behavioral contradiction discovered on close read of both files:

- `workspace/USER.md` states: **"STRICT: DO NOT SEND EMOJI REACTIONS TO MESSAGES."**
- `workspace/AGENTS.md` (section "React Like a Human!") states: **"On platforms that support reactions (Discord, Slack), use emoji reactions naturally"** and lists specific cases when to react (👍, ❤️, 😂, etc.).

AGENTS.md is loaded at startup. USER.md is also loaded at startup. These rules directly contradict each other. Because AGENTS.md has the more detailed and enthusiastic guidance (with examples), the agent is likely defaulting to AGENTS.md behavior and violating Josh's explicit preference.

**Why it matters:**
Josh's no-emoji preference is explicit and marked STRICT. If Heather is using emoji reactions, Josh has noticed. This may erode trust faster than any missing feature.

**Action — immediate:**
1. Add the no-emoji rule explicitly to `workspace/SOUL.md` (the highest-priority file loaded at startup) so it overrides AGENTS.md:
```markdown
## Josh-Specific Rules (HARD RULES — never override)

**No emoji reactions. Ever.** Josh explicitly said: STRICT: DO NOT SEND EMOJI REACTIONS TO MESSAGES.
Not thumbs-up. Not hearts. Nothing. This overrides any general guidance about "reacting like a human."
```

2. Update the AGENTS.md "React Like a Human!" section to add an exception note:
```markdown
**Exception:** If USER.md or SOUL.md contains an explicit no-emoji rule for your human, skip all reactions entirely. Always check your human's hard rules before applying defaults.
```

The SOUL.md fix is the higher priority — do that first.

---

## Finding 11 — No gog-cli Skill in Josh's Repo ⭐ NEW 2026-06-14 Evening

**Risk: MEDIUM-HIGH**

The `workspace/memory/inbox-state.json` file shows active email and iMessage tracking (with real timestamps, thread IDs, and a monitored inbox state). This implies Heather is doing some level of email management.

However, unlike Noah's repo which has `skills/gog-cli/` with a full Google Workspace CLI skill (Gmail, Calendar, Contacts, Drive), **Josh's repo has no `skills/` directory at all.** There is no gog-cli skill defined or installed for Heather.

This creates a gap: the inbox-state suggests email integration is happening through some mechanism, but without an explicit gog-cli skill, Heather doesn't have a structured interface to Gmail, Calendar, or Contacts. She may be using a built-in or another integration, but there's no skill documentation for it.

**Why it matters:**
- Heather lacks documented command reference for the tools she's supposed to be using
- If Google Workspace access exists via another mechanism, it's undocumented and fragile
- If it doesn't exist, inbox-state.json may be from a failed integration attempt

**Action:**
1. Check AlphaClaw General tab → Google Workspace section to confirm connection status
2. If connected: install the gog-cli skill (or equivalent) and confirm it appears in `skills/`
3. If not connected: complete the Google Workspace OAuth setup (see Finding 2)
4. Once installed, populate `workspace/TOOLS.md` with the connected account details

---

## Finding 12 — All June 12 Findings Unresolved (2 Days) ⭐ STATUS UPDATE

**Risk: ESCALATION**

The June 12 morning scan identified 8 actionable findings. As of this June 14 evening scan (48 hours later), **all remain unresolved**:

- Version still 2026.3.22 (Finding 1)
- Google Workspace still not confirmed connected (Finding 2)
- No compaction/memoryFlush settings (Finding 4)
- TOOLS.md still blank (Finding 5)
- Dreaming not enabled, MEMORY.md not created (Finding 7)
- HEARTBEAT.md still empty (Finding 8)

**Priority escalation:** Findings 1, 4, 7, and 10 (new today) should be treated as a bundle — they can all be applied in a single session of ~30 minutes:
- `openclaw update` → resolves Finding 1
- Edit `openclaw.json` → adds compaction + dreaming → resolves Findings 4 and 7
- Edit `workspace/SOUL.md` → adds Josh rules + no-emoji override → resolves Finding 10
- Create `workspace/MEMORY.md` → resolves the memory gap (Finding 7)
- Edit `workspace/HEARTBEAT.md` → resolves Finding 8

---

## Summary Table

| Finding | Priority | Effort | Impact | Status |
|---|---|---|---|---|
| Update to 2026.6.5 | HIGH | Low (one command) | iMessage fixes, web search, MCP fixes | ⏳ Unresolved (2 days) |
| Connect Google Workspace | CRITICAL | Medium (OAuth setup) | Unlocks email + calendar access | ⏳ Unresolved (2 days) |
| Concurrent search bug | MEDIUM | Low (update first) | More reliable research tasks | ⏳ Unresolved |
| Add compaction/memoryFlush | HIGH | Low (config change) | Persistent memory on compaction | ⏳ Unresolved (2 days) |
| Populate TOOLS.md | LOW | Low (5 min) | Fewer clarifying questions | ⏳ Unresolved (2 days) |
| Enable Discord streaming | LOW | Low (one line) | Better perceived response speed | ⏳ Unresolved |
| Enable Dreaming + create MEMORY.md | HIGH | Low-Medium | Automated long-term memory | ⏳ Unresolved (2 days) |
| Populate HEARTBEAT.md | MEDIUM | Low (5 min) | Proactive email/calendar alerts | ⏳ Unresolved (2 days) |
| Monitor 2026.6.6-beta | INFO | None | Awareness only | ⏳ Monitoring |
| AGENTS.md emoji rule contradicts USER.md | HIGH | Low (5 min) | Stops Heather violating Josh's hard rule | 🆕 New 06-14 |
| No gog-cli skill in Josh's repo | MEDIUM-HIGH | Medium | Confirms/fixes email tool chain | 🆕 New 06-14 |
| All June 12 findings still unresolved | ESCALATION | — | Batch fix recommended | 🆕 Status 06-14 |

---

*Sources: [OpenClaw Releases](https://github.com/openclaw/openclaw/releases), [OpenClaw Memory docs](https://docs.openclaw.ai/concepts/memory), [Memory config reference](https://docs.openclaw.ai/reference/memory-config), [OpenClaw Memory Masterclass](https://velvetshark.com/openclaw-memory-masterclass), [AlphaClaw Releases](https://github.com/chrysb/alphaclaw/releases), [X: @BunsDev on 2026.5.7](https://x.com/BunsDev/status/2052600614207516752), [X: @chrysb AlphaClaw 0.8.0](https://x.com/chrysb/status/2032943853012136120)*
