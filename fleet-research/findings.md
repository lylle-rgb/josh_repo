# Fleet Research Findings — Josh / Heather Schwartz

**Scan date:** 2026-06-15 (morning) · Previous scan: 2026-06-14 evening
**Researcher:** AlphaClaw Fleet Agent
**Instance:** josh_repo (Heather Schwartz — personal assistant)
**Current version:** 2026.3.22
**Latest stable:** 2026.6.5 (June 3, 2026)
**Latest beta:** 2026.6.5-beta.6 (June 9, 2026)

> ⛔ DEADLINE IN 2 DAYS: gemini-2.5-flash deprecates June 17. One line in openclaw.json. 30 seconds.
> All 12 prior findings remain unresolved (Day 11). Findings 13–15 are new this morning.

---

## Finding 1 — Version Outdated (3 Months Behind)

**Risk: HIGH**

Heather is running OpenClaw `2026.3.22`. The current stable is `2026.6.5` and beta `2026.6.5-beta.6` dropped June 9. That's a 3-month gap with ~8 releases in between.

**Why it matters for Heather:**
Several fixes in this window directly affect the personal assistant use case:
- **iMessage recovery** (2026.6.5): Private-API failures and send timeouts now explain themselves; split-send coalescing honors balloon metadata. Silent iMessage failures are likely this bug.
- **Parallel web search bundled** (2026.6.5): Web search is now a first-class built-in; no separate setup required.
- **MCP tool result coercion** (2026.6.5): Non-text/image MCP blocks no longer poison session history with errors.
- **Cron state bug** (prior releases): Cron state was wiped during a SQLite migration — any scheduled reminders or tasks may have been silently lost.
- **Model override drop on idle rollover** (prior releases): User model overrides were dropped on daily session rollover — fixed.
- **Meeting Notes** (2026.5.26): Real-time Discord voice call transcription — missed entirely at current version.

**Action:**
```bash
openclaw update
```
Or via the AlphaClaw Watchdog tab: `https://5.78.142.81.sslip.io#watchdog`

Recommended staged upgrade: 2026.3.22 → 2026.5.27 → 2026.6.2 → 2026.6.5

---

## Finding 2 — Google Workspace Not Connected (Critical Gap)

**Risk: CRITICAL**

The bootstrap TOOLS.md shows no Google accounts configured. Heather's entire value proposition is managing Josh's iMessage, email, and calendar. Without Google Workspace, she cannot access Gmail, Google Calendar, or Google Contacts.

**Update (June 14):** Analysis of `workspace/memory/inbox-state.json` confirms email and iMessage have been offline for **85+ days** (iMessage paused ~April 27, email last checked ~April 30). This is not a recent issue.

**Action:**
1. Go to AlphaClaw UI: `https://5.78.142.81.sslip.io#general`
2. Under Google Workspace, provide OAuth client credentials from Google Cloud Console
3. Authorize: Gmail, Google Calendar, Google Contacts (minimum); Drive and Tasks recommended
4. Confirm the account appears in TOOLS.md

**Alternative path if OAuth is blocked:** See Finding 14 (Nylas CLI).

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
Populate `workspace/TOOLS.md` with Josh's devices, any SSH aliases, preferred communication format preferences, and shortcuts for frequently mentioned places or people (Bliss HQ, Oben HiFi contacts, etc.).

---

## Finding 6 — Discord Streaming Disabled

**Risk: LOW**

`openclaw.json` has `"streaming": "off"`. Heather's replies appear all at once after full generation, which feels slow for longer responses.

**Updated action (see also Finding 13 for recommended mode):**
Change to `"streaming": "progress"` rather than just `"on"` — progress mode is available in v2026.5.3+ and produces cleaner output when tools are used mid-response.

---

## Finding 7 — Dreaming (Memory Consolidation) Not Enabled

**Risk: HIGH**

OpenClaw's optional "Dreaming" feature runs a background memory consolidation pass on a configurable schedule. It is disabled by default. Currently there is no MEMORY.md at all. Both need to be set up together for long-term memory to work.

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

`workspace/HEARTBEAT.md` contains only the template placeholder. Heather returns `HEARTBEAT_OK` on every heartbeat poll without doing any proactive work. The entire proactive assistant pipeline — email urgency checks, calendar alerts, weather — is completely dormant. This has been the case for 85+ days.

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

## Finding 9 — 2026.6.5 Stable; 2026.6.5-beta.6 Latest Beta (Monitor)

**Risk: INFO**

2026.6.5 is the current stable (June 3). 2026.6.5-beta.6 dropped June 9. No new stable since June 3. Do not chase the beta. Upgrade target remains 2026.6.5 stable.

---

## Finding 10 — AGENTS.md Emoji Rule Contradicts USER.md Strict Preference

**Risk: HIGH**

This is a direct behavioral contradiction:

- `workspace/USER.md` states: **"STRICT: DO NOT SEND EMOJI REACTIONS TO MESSAGES."**
- `workspace/AGENTS.md` (section "React Like a Human!") states: **"use emoji reactions naturally"** with specific cases when to react (👍, ❤️, 😂, etc.).

Because AGENTS.md has more detailed and enthusiastic guidance with examples, Heather is likely defaulting to AGENTS.md behavior and violating Josh's explicit preference.

**Action — immediate:**
1. Add to the top of `workspace/SOUL.md` under a new "Josh-Specific Hard Rules" section:
```markdown
## Josh-Specific Hard Rules (NEVER override)

**No emoji reactions. Ever.** Josh's explicit instruction: STRICT: DO NOT SEND EMOJI REACTIONS TO MESSAGES.
Not 👍. Not ❤️. Nothing. This overrides the "React Like a Human" guidance in AGENTS.md.
```

2. Add an exception to the "React Like a Human!" section in AGENTS.md:
```markdown
**Exception:** If USER.md or SOUL.md contains an explicit no-emoji rule, skip all reactions entirely.
Always check your human's hard rules before applying any defaults.
```

SOUL.md fix is higher priority — do that one first.

---

## Finding 11 — No gog-cli Skill in Josh's Repo

**Risk: MEDIUM-HIGH**

Unlike Noah's repo which has `skills/gog-cli/` with a full Google Workspace CLI skill, Josh's repo has no `skills/` directory at all. There is no structured Gmail/Calendar/Contacts interface defined for Heather.

**Action:**
1. Check AlphaClaw General tab → Google Workspace section to confirm connection status
2. If connected: install gog-cli skill and confirm it appears in `skills/`
3. If not connected: complete Google Workspace OAuth (Finding 2) or Nylas CLI (Finding 14)
4. Once installed, populate `workspace/TOOLS.md` with the connected account details

---

## Finding 12 — Prolonged Stagnation (11 Days, No Action)

**Risk: ESCALATION**

As of this June 15 morning scan, all findings first identified in the June 4–12 window remain completely unresolved 11 days later. Priority batch that can be done via GitHub file editor alone (no VPS access needed):

- Fix gemini-2.5-flash fallback → gemini-3.5-flash (30 sec, ⛔ June 17 deadline)
- Create `workspace/MEMORY.md` (5 min)
- Populate `workspace/HEARTBEAT.md` (5 min)
- Add Josh hard rules to `workspace/SOUL.md` (5 min)
- Delete `workspace/BOOTSTRAP.md` (reduces wasteful context on every session startup)
- Add emoji override to `workspace/AGENTS.md` (2 min)

Total GitHub-only effort: ~20–30 minutes. These six actions resolve Findings 10, 7, 8, 12, and JOSH-50.

---

## Finding 13 — Discord Streaming: Use "progress" Mode ⭐ NEW 2026-06-15 Morning

**Risk: LOW**

Finding 6 documented `"streaming": "off"` → `"streaming": "on"` as the fix. Web research this morning reveals a better approach: `"progress"` mode, unified across Discord/Telegram/Slack/Matrix/Teams since v2026.5.3.

**Why "progress" is better than "on":**
- `"on"` sends raw chunks; produces an edit storm in Discord when tools fire mid-response (Heather uses Google Workspace tools regularly)
- `"progress"` is progress-aware; batches tool-use turns; produces cleaner, more readable responses

**Updated action:**
```json
"channels": {
  "discord": {
    "streaming": "progress"
  }
}
```

**Dependency:** OpenClaw ≥ 2026.5.3, included in the 2026.6.5 upgrade target (Finding 1).

---

## Finding 14 — Nylas CLI: Alternative Email/Calendar Integration Path ⭐ NEW 2026-06-15 Morning

**Risk: MEDIUM**

Finding 2 (Google Workspace not connected) has been open 85+ days. If GCP OAuth setup is the blocker, **Nylas CLI** provides an alternative path:
- 72+ commands across Gmail, Outlook, Exchange, Yahoo, iCloud, IMAP
- Single authentication flow — no Google Cloud Console project required
- Covers email reading, sending, calendar events, and contacts
- OpenClaw skill install: `openclaw skill install nylas-cli`

**Risk:** Nylas is middleware — email transits a third-party API. Appropriate for unblocking the integration while a more direct path is set up, or for lower-sensitivity accounts.

**Why it matters:** Heather has been completely offline from Josh's email for 85+ days. The Nylas path may restore core personal assistant capability faster than completing the GCP OAuth setup.

---

## Finding 15 — NVIDIA SkillSpector Skill Security (Post-Upgrade Passive Benefit) ⭐ NEW 2026-06-15 Morning

**Risk: LOW (passive)**

OpenClaw 2026.6.1 shipped Skill Workshop with NVIDIA SkillSpector integration:
- Every ClawHub skill ships with a Skill Card documenting data access scope
- All skills are scanned for prompt injection, hidden instructions, and agentic risks before reaching production
- Review queue available before skills touch live workflows

**Why it matters for Heather:** Heather has access to Josh's personal calendar, contacts, and business communications. Skill-level security attestation reduces data exfiltration risk from supply chain attacks.

**Action:** No immediate action. Passive security improvement that activates automatically on upgrade to 2026.6.5 (Finding 1).

---

## Summary Table

| Finding | Priority | Effort | Impact | Status |
|---|---|---|---|---|
| ⛔ JOSH-50: gemini-2.5-flash deadline Jun 17 | **CRITICAL** | 30 sec | Fallback chain integrity | ⛔ 2 DAYS LEFT |
| 2. Connect Google Workspace | CRITICAL | Medium | Unlocks all email/calendar | ⏳ Unresolved — Day 11 |
| 7. Enable Dreaming + create MEMORY.md | HIGH | Low-Medium | Automated long-term memory | ⏳ Unresolved — Day 11 |
| 10. AGENTS.md emoji contradicts USER.md | HIGH | Low | Stops violating Josh's rule | ⏳ Unresolved — Day 11 |
| 1. Update to 2026.6.5 | HIGH | Low | iMessage, web search, MCP fixes | ⏳ Unresolved — Day 11 |
| 4. Add compaction/memoryFlush | HIGH | Low | Memory safe on compaction | ⏳ Unresolved — Day 11 |
| 8. Populate HEARTBEAT.md | MEDIUM | Low (5 min) | Proactive email/calendar alerts | ⏳ Unresolved — Day 11 |
| 11. No gog-cli skill | MEDIUM-HIGH | Medium | Email toolchain gap | ⏳ Unresolved — Day 2 |
| 3. Concurrent search bug | MEDIUM | Low (update first) | Research reliability | ⏳ Unresolved |
| 14. Nylas CLI alternative email path | MEDIUM | Low-Medium | Alternative to blocked OAuth | 🆕 New 06-15 morning |
| 12. 11-day stagnation escalation | ESCALATION | — | Batch fix recommended | ⏳ Day 11 |
| 6 / 13. Discord streaming → "progress" mode | LOW | Low | Cleaner streaming responses | 🆕 New 06-15 (replaces Finding 6) |
| 5. Populate TOOLS.md | LOW | Low | Fewer clarifying questions | ⏳ Unresolved — Day 11 |
| 9. Monitor 2026.6.6 stable | INFO | None | Awareness only | ⏳ Monitoring — 2026.6.5 still latest |
| 15. NVIDIA SkillSpector post-upgrade | LOW | None | Passive skill security | 🆕 New 06-15 morning |

---

*Sources: [OpenClaw Releases](https://github.com/openclaw/openclaw/releases), [OpenClaw Streaming docs](https://docs.openclaw.ai/concepts/streaming), [Nylas CLI OpenClaw guide](https://cli.nylas.com/guides/nylas-openclaw-personal-assistant), [OpenClaw NVIDIA Skill Workshop](https://openclaw.ai/blog/openclaw-nvidia-skill-security), [Brave Search/OpenClaw blog](https://brave.com/blog/openclaw/), [OpenClaw Memory docs](https://docs.openclaw.ai/concepts/memory), [AlphaClaw Releases](https://github.com/chrysb/alphaclaw/releases), [SEN-X OpenClaw 2026.6.1](https://senx.ai/openclaw-news/2026-06-02-openclaw-news)*
