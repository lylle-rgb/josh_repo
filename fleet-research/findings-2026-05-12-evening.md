# Fleet Research — Josh / Heather Schwartz — Evening Scan

**Scan Date:** 2026-05-12 (Evening)  
**Agent:** AlphaClaw Apex Fleet Research Agent  
**Instance:** Josh — Discord bot Heather Schwartz (personal assistant: iMessage, email, calendar, contacts)  
**Previous findings:** `findings.md` (Day 24, 2026-05-11). All prior findings remain unresolved.

---

## Platform News (New Since Day 24 Evening Scan)

### OpenClaw 2026.5.10 Beta Series — Active Development (Not Production-Ready)

Four beta releases on May 10–11 preview the next stable:

- **v2026.5.10-beta.5 (May 11):** Fly Machines container detection, Plugin SDK `extractStructuredWithModel()` media-understanding, Fal provider routing improvements, deprecated subpath cleanup.
- **v2026.5.10-beta.4 (May 11):** pnpm 11 upgrades, **per-agent message tool overrides**, Slack unfurl config options, Codex app-server code-mode enhancements.
- **v2026.5.10-beta.3 (May 11):** **`/context map` treemap visualization**, stricter TypeScript checks, **A2A ping-pong raised to 20 turns**, Telegram VNC recording improvements.
- **v2026.5.10-beta.2 (May 10):** Realtime voice diagnostics, Discord voice Opus decoder options, **ACPX startup probe fixes**, OpenAI-compatible model compatibility.

**Current stable remains 2026.5.7.** Do not apply betas to Josh's production instance.

**Upcoming features relevant to Heather (plan ahead):**

| Feature | Beta | Value for Heather |
|---|---|---|
| `/context map` treemap | beta.3 | Visualize token usage in long email+calendar+iMessage sessions; inform compaction decisions |
| Per-agent message tool overrides | beta.4 | Restrict calendar writes / email sends to DM only; disallow from group channel context |
| A2A 20-turn ping-pong | beta.3 | Sub-agent delegation for parallel research tasks once heartbeats are active |
| ACPX startup probe fix | beta.2 | More reliable AlphaClaw watchdog on Josh's VPS (5.78.142.81) |

### No New Stable Release
2026.5.7 confirmed current stable. **Josh version gap: 2026.3.22 → 2026.5.7 = 13 releases, 82 days.**

### iMessage Monitoring — Day 17 Silent, Email Day 14 Lapsed
Timestamps from `inbox-state.json` unchanged:
- iMessage last active: ~April 26 — **17 days silent**
- Email last polled: ~April 29 — **14 days lapsed**
- Thread `19db60d96d2118c8` still has a pending draft reply

---

## New Findings — Josh Instance (Day 25)

### Finding 29. `/context map` Coming — Long-Session Token Visibility
**Risk: OPPORTUNITY (Post-2026.5.10-stable)**

Josh's sessions interleave email, calendar, and iMessage context in a single turn. The `/context map` command (arriving in beta.3) will display a token usage treemap for the current session — enabling Heather to surface context pressure before hitting the compaction floor during a long assist session.

**Why it matters:** Without visibility into token distribution, Heather's current compaction config (pending — never applied) operates blind. `/context map` turns context management from reactive to proactive.

**Exact change to apply (when 2026.5.10 stable):** Add to HEARTBEAT.md:
```
### Context Health (every 30 min in active sessions)
- If session is >25 min old, run /context map
- Surface context pressure summary to Josh if >70% used
```
**Risk level:** None — additive only, post-stable.

---

### Finding 30. Per-Agent Tool Overrides — Discord Group vs DM Security Boundary
**Risk: OPPORTUNITY (Post-2026.5.10-stable)**

Beta.4 introduces per-agent message tool overrides. For Heather, this enables:
- **Discord group channels:** Allow web search, file reads, emoji reactions only. Block calendar writes, email sends, iMessage sends.
- **Discord DM (Josh direct):** Full tool access.

**Why it matters:** Josh's Guild (1484448262290276464) has `requireMention: false` and `groupPolicy: open` — Heather responds to all messages. A misconfigured group message could accidentally trigger a calendar event or email send. Tool overrides harden this boundary.

**Exact change to apply (when 2026.5.10 stable):** Add to `openclaw.json` under `channels.discord.guilds`:
```json
"toolOverrides": {
  "group": { "allow": ["search", "read", "react"], "deny": ["calendar_write", "email_send", "imessage_send"] },
  "dm": { "allow": "*" }
}
```
**Risk level:** Low. Additive restriction — won't break existing behavior in DMs.

---

### Finding 31. A2A 20-Turn Support — Future Sub-Agent Delegation
**Risk: OPPORTUNITY (Post-2026.5.10-stable)**

Beta.3 raises the A2A ping-pong limit to 20 turns. Once stable and once HEARTBEAT.md is active, Heather can delegate background tasks to a sub-agent (e.g., a research sub-agent that scans Bliss brand mentions while Heather handles Josh's inbox). Combined with the `/goal` command:
```
/goal Monitor Bliss brand mentions across social and news this week. Compile a brief daily summary to workspace/memory/brand-monitor-YYYY-MM-DD.md.
```
**Risk level:** None — future planning only.

---

## Implementation Status — Day 25

Version gap: **2026.3.22 → 2026.5.7 (13 releases, 82 days)**. iMessage dark: **17 days**. Email lapsed: **14 days**. Bootstrap TOOLS.md: **54 days stale**. Zero implementations across 25-day scan window.

| Finding | Severity | Days Pending | Status |
|---|---|---|---|
| OpenClaw 13 releases outdated | HIGH | 25 | ⬜ Pending |
| iMessage monitoring paused (~April 26) | HIGH | 25 | ⬜ Pending |
| HEARTBEAT.md empty | HIGH | 25 | ⬜ Pending |
| No daily memory files (45+ days) | HIGH | 11 | ⬜ Pending |
| MEMORY.md missing | MEDIUM | 25 | ⬜ Pending |
| SOUL.md never evolved | MEDIUM | 11 | ⬜ Pending |
| No-emoji rule not in SOUL.md | MEDIUM | 11 | ⬜ Pending |
| Bootstrap TOOLS.md stale (54 days) | MEDIUM | 25 | ⬜ Pending |
| inbox-state.json malformed + thread pending | MEDIUM | 25 | ⬜ Pending |
| TOOLS.md template only | MEDIUM | 25 | ⬜ Pending |
| Active Memory plugin not installed | MEDIUM | 25 | ⬜ Pending |
| Active Memory admin scope required (5.7) | MEDIUM | 7 | ⬜ Noted |
| Retired claude-3.5-haiku fallback | LOW | 7 | ⬜ Noted |
| Discord streaming off | LOW | 25 | ⬜ Pending |
| No compaction config | LOW | 25 | ⬜ Pending |
| iMessage cloud proxy root cause | INFO | 6 | ⬜ Investigate |
| exec-approval defaults seeded post-update | INFO | 6 | ✅ |
| Update before heartbeats | INFO | 9 | ✅ Validated |
| Gemini 3.1 Flash available | OPPORTUNITY | 7 | ⬜ Post-update |
| File-transfer plugin (paired node) | OPPORTUNITY | 6 | ⬜ Post-update |
| /tts latest — voice briefings | OPPORTUNITY | 6 | ⬜ Post-update |
| oc-path plugin — workspace nav | OPPORTUNITY | 2 | ⬜ Post-update |
| /context map — session visibility | OPPORTUNITY | new | ⬜ Post-stable |
| Per-agent tool overrides (Discord boundary) | OPPORTUNITY | new | ⬜ Post-stable |
| A2A 20-turn sub-agent delegation | OPPORTUNITY | new | ⬜ Post-stable |

**Correct implementation order:** See Day 22 Evening Scan in `findings.md`. All prerequisites validated. Only remaining blocker is execution.

---
*Generated by AlphaClaw Apex Fleet Research Agent — Evening Scan — 2026-05-12*
