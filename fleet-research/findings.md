# Fleet Research Findings — Josh / Heather Schwartz

**Scan date:** 2026-06-11  
**Researcher:** AlphaClaw Fleet Agent  
**Instance:** josh_repo (Heather Schwartz — personal assistant)  
**Current version:** 2026.3.22  
**Latest stable:** 2026.6.5 (June 9, 2026)  
**Latest beta:** 2026.6.6-beta.1 (June 10, 2026)

---

## Finding 1 — Version Outdated (3 Months Behind)

**Risk: HIGH**

Heather is running OpenClaw `2026.3.22`. The current stable is `2026.6.5` and a beta `2026.6.6-beta.1` dropped yesterday. That's a 3-month gap with ~8 releases in between.

**Why it matters for Heather:**
Several fixes in this window directly affect the personal assistant use case:
- **iMessage recovery** (2026.6.5 / 2026.6.6-beta.1): Private-API failures and send timeouts now explain themselves; split-send coalescing honors balloon metadata. If Heather ever fails silently on iMessage, this is likely the fix.
- **Parallel web search bundled** (2026.6.5): Web search is now a first-class built-in; no separate setup required.
- **MCP tool result coercion** (2026.6.5): Non-text/image MCP blocks no longer poison session history with errors.
- **Cron state bug** (prior releases): Cron state was wiped during a SQLite migration — any scheduled reminders or tasks may have been silently lost.
- **Model /model override drop on idle rollover** (prior releases): User model overrides were dropped on daily session rollover — fixed.

**Action:**
```bash
# On the AlphaClaw host
openclaw update
# or via AlphaClaw watchdog auto-update if configured
```
Alternatively, update via the AlphaClaw Watchdog tab: `https://5.78.142.81.sslip.io#watchdog`

---

## Finding 2 — Google Workspace Not Connected (Critical Gap)

**Risk: CRITICAL**

The bootstrap TOOLS.md shows: **"No Google accounts are currently configured."**

Heather's entire value proposition is managing Josh's iMessage, email, and calendar. Without Google Workspace connected, she cannot access Gmail, Google Calendar, or Google Contacts. She's operating blind on the most important parts of her job.

**Why it matters:**  
Josh is a Founder/CEO (Bliss, Oben HiFi) based in LA. Email and calendar management for a founder is high-value and time-sensitive. A personal assistant who can't access the calendar or inbox is severely limited.

**Action:**  
1. Go to the AlphaClaw UI General tab: `https://5.78.142.81.sslip.io#general`
2. Under Google Workspace, provide OAuth client credentials from Google Cloud Console
3. Authorize: Gmail, Google Calendar, Google Contacts (minimum); Drive and Tasks recommended
4. Confirm the account appears under Available Google Accounts in TOOLS.md

Once connected, Heather gains access to the `gog` CLI for calendar/email/contacts operations.

---

## Finding 3 — Concurrent Web Search Bug (Gemini-3-Flash)

**Risk: MEDIUM**

A known OpenClaw issue (#30675) affects the primary model `google/gemini-3-flash-preview`: when a subagent fires multiple parallel `web_search` tool calls in one turn, the first succeeds but subsequent concurrent calls fail with `missing_gemini_api_key`.

**Why it matters:**  
When Heather does research tasks for Josh (looking up restaurants, checking news, researching contacts), she may be running multi-step searches internally. Silent search failures mean she returns incomplete answers without surfacing an error.

**Action:**  
Update to 2026.6.5 first (Finding 1) — the parallel web search bundled in 2026.6.5 includes an updated provider with better API key discovery that addresses this. After updating, monitor for `missing_gemini_api_key` errors in logs.

As a secondary mitigation, add this to `openclaw.json` under `agents.defaults` if the issue persists:
```json
"webSearch": {
  "maxConcurrentCalls": 1
}
```

---

## Finding 4 — No Memory Protection Before Compaction

**Risk: HIGH**

Josh's `openclaw.json` has no compaction settings at all. Noah's instance (for comparison) has:
```json
"compaction": {
  "reserveTokensFloor": 40000,
  "memoryFlush": {
    "enabled": true,
    "softThresholdTokens": 4000
  }
}
```
Without `memoryFlush`, OpenClaw does not trigger a memory-write turn before compaction. When Heather's session hits the context limit, everything from that session is silently lost — preferences Josh mentioned, pending tasks, context about ongoing projects.

**Why it matters:**  
For a personal assistant, continuity is the entire product. If Heather forgets what Josh told her last session, the relationship breaks down. The memory flush is described as "the single most impactful configuration change you can make" by the OpenClaw memory docs.

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
The 6h TTL is appropriate for a personal assistant with long-running or recurring conversations. Noah's 5m TTL is too aggressive for this use case.

---

## Finding 5 — TOOLS.md Is a Blank Template

**Risk: LOW**

The workspace `TOOLS.md` (at `workspace/TOOLS.md`) contains only the template placeholder text — no actual entries. This file is meant to document Josh's specific environment: devices, SSH aliases, voice preferences, speaker names, etc.

**Why it matters:**  
Heather has to guess or ask about Josh's setup on every session. A filled-in TOOLS.md means she knows which devices are which, how to refer to them, and can act without clarifying questions.

**Action:**  
Have Heather (or Josh directly) populate `workspace/TOOLS.md` with:
- Josh's devices (phone, laptop, home hardware)
- Any SSH aliases or server access
- Preferred communication tone/format preferences  
- Any shortcuts or nicknames for places/people Josh mentions frequently

---

## Finding 6 — Discord Streaming Disabled

**Risk: LOW**

In `openclaw.json`, Discord streaming is explicitly set to `"off"`:
```json
"channels": {
  "discord": {
    "streaming": "off"
  }
}
```

**Why it matters:**  
With streaming off, Heather's replies appear all at once after full generation — which can feel slow for longer responses. Streaming gives the impression of faster response and lets Josh see progress on longer tasks.

**Action:**  
Change `"streaming": "off"` to `"streaming": "on"` in `openclaw.json`. Low-risk config change. Test with a longer response to verify it works well in Josh's Discord client.

---

## Summary Table

| Finding | Priority | Effort | Impact |
|---|---|---|---|
| Update to 2026.6.5 | HIGH | Low (one command) | iMessage fixes, web search, MCP fixes |
| Connect Google Workspace | CRITICAL | Medium (OAuth setup) | Unlocks email + calendar access |
| Concurrent search bug | MEDIUM | Low (update first) | More reliable research tasks |
| Add compaction/memoryFlush | HIGH | Low (config change) | Persistent memory across sessions |
| Populate TOOLS.md | LOW | Low (5 min fill-in) | Fewer clarifying questions |
| Enable Discord streaming | LOW | Low (one line) | Better perceived response speed |

---

*Sources: [OpenClaw Releases](https://github.com/openclaw/openclaw/releases), [OpenClaw 2026.6.5 Release Notes](https://agentriot.com/news/ai-tools/openclaw-v2026-6-5-ships-with-channel-hardening-provider-fixes-and-new-calver-numbering), [OpenClaw Memory Management](https://mem0.ai/blog/openclaw-memory-management-live-data-compaction-and-best-practices), [Concurrent search issue](https://github.com/openclaw/openclaw/issues/30675)*
