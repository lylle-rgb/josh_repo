# Fleet Research Findings — Josh / Heather Schwartz

**Scan date:** 2026-06-12 (morning) · Previous scan: 2026-06-11  
**Researcher:** AlphaClaw Fleet Agent  
**Instance:** josh_repo (Heather Schwartz — personal assistant)  
**Current version:** 2026.3.22  
**Latest stable:** 2026.6.5 (June 9, 2026)  
**Latest beta:** 2026.6.6-beta.1 (June 10, 2026)

> ⚠️ All 6 findings from the June 11 scan remain unresolved. Findings 7–8 are new today.

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

## Finding 7 — Dreaming (Memory Consolidation) Not Enabled ⭐ NEW 2026-06-12

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

## Finding 8 — HEARTBEAT.md Not Populated ⭐ NEW 2026-06-12

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

`2026.6.6-beta.1` dropped June 10 and has not yet promoted to stable as of this scan (June 12). No action needed — update to 2026.6.5 now (Finding 1) and watch for the 2026.6.6 stable announcement.

---

## Summary Table

| Finding | Priority | Effort | Impact | Status |
|---|---|---|---|---|
| Update to 2026.6.5 | HIGH | Low (one command) | iMessage fixes, web search, MCP fixes | ⏳ Unresolved |
| Connect Google Workspace | CRITICAL | Medium (OAuth setup) | Unlocks email + calendar access | ⏳ Unresolved |
| Concurrent search bug | MEDIUM | Low (update first) | More reliable research tasks | ⏳ Unresolved |
| Add compaction/memoryFlush | HIGH | Low (config change) | Persistent memory on compaction | ⏳ Unresolved |
| Populate TOOLS.md | LOW | Low (5 min) | Fewer clarifying questions | ⏳ Unresolved |
| Enable Discord streaming | LOW | Low (one line) | Better perceived response speed | ⏳ Unresolved |
| Enable Dreaming + create MEMORY.md | HIGH | Low-Medium | Automated long-term memory | 🆕 New 06-12 |
| Populate HEARTBEAT.md | MEDIUM | Low (5 min) | Proactive email/calendar alerts | 🆕 New 06-12 |
| Monitor 2026.6.6-beta | INFO | None | Awareness only | 🆕 New 06-12 |

---

*Sources: [OpenClaw Releases](https://github.com/openclaw/openclaw/releases), [OpenClaw Memory docs](https://docs.openclaw.ai/concepts/memory), [Memory config reference](https://docs.openclaw.ai/reference/memory-config), [OpenClaw Memory Masterclass](https://velvetshark.com/openclaw-memory-masterclass), [AlphaClaw Releases](https://github.com/chrysb/alphaclaw/releases)*
