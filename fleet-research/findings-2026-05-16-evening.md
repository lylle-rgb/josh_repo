# Fleet Research — Josh / Heather Schwartz — Evening Scan

**Scan Date:** 2026-05-16 (Evening — Day 29)
**Agent:** AlphaClaw Apex Fleet Research Agent
**Instance:** Josh / Heather Schwartz — Discord bot personal assistant (iMessage, email, calendar, contacts)
**Previous Findings:** findings-2026-05-15-evening.md (Day 28 Evening, Findings 1–55)
**Cumulative Open Findings:** 60 (5 new this evening, 0 resolved)

---

## Platform News — New Since Yesterday's Evening Scan

| Item | Detail |
|---|---|
| OpenClaw 2026.5.14-beta.2 | Beta branch is now seven patch versions ahead of stable 2026.5.7. Josh is on 2026.3.22 — effectively 14+ release points behind on the stable channel alone. |
| AlphaClaw 0.8.0 — Chrome DevTools MCP | Confirmed released. Enables control of Josh's Mac from the Hetzner VPS via Chrome's DevTools protocol — no VNC, no screen sharing required. Requires OpenClaw upgrade first. |
| Heartbeat cadence improvements (May 12 stream) | Upstream heartbeat system now supports provider stream drains and deterministic update recovery. More reliable keep-alive during idle periods. Directly relevant to Finding 52 (no active heartbeat on this instance). |
| Anthropic conversation amnesia fix | New OpenClaw integration reseeds Claude fresh-session retries from bounded prior transcript history — prevents context loss on session restart. Not directly applicable to Josh's primary model (Google Gemini) but affects behavior through the OpenRouter Anthropic fallback chain. |
| Security update — explicit browser device pairing | OpenClaw now requires explicit browser device pairing before proxy-scoped access is granted. Relevant to AlphaClaw UI access at https://5.78.142.81.sslip.io. Verify pairing is current after any session gap. |
| Context engine plugins (2026.3.7) | Allows third-party plugins to provide alternative context management strategies (compaction, assembly). Josh's instance at 2026.3.22 is just ahead of this release but has not configured any context plugins. |

---

## New Findings — Evening Scan (56–60)

---

### Finding 56 — Google Account Still Not Connected: Day 29 (Escalated to CRITICAL)

**Risk:** CRITICAL (escalated from HIGH)
**Days Pending:** 29
**Previous:** Finding 48 (Day 28 Evening)

**Description:**
Finding 48 was filed yesterday as the single highest-priority action in the entire 28-day backlog. The required action was a browser visit to `https://5.78.142.81.sslip.io#general` to connect Josh's Google account via OAuth. This action has not been taken. The bot has now operated for 29 consecutive days without Gmail, Google Calendar, or Google Contacts — the three stated primary use cases of the Heather Schwartz deployment.

This finding is escalated to CRITICAL. The situation is not degraded functionality — it is zero functionality on the advertised capabilities. Every day this remains unresolved, Heather is drafting and responding in Discord as if she has access to Josh's inbox, calendar, and contacts, when in fact every `gog` tool call has been silently failing or not being attempted since deployment.

**Action:**
1. Open `https://5.78.142.81.sslip.io#general` in a browser.
2. Locate the Google account integration section.
3. Connect Josh's Google account via OAuth.
4. After connection, verify `workspace/hooks/bootstrap/TOOLS.md` no longer reads "No Google accounts are currently configured."
5. Run `gog gmail list` from the VPS shell to confirm the credential is live.
6. Restart the bot session so the updated TOOLS.md is injected at next startup.

**Risk Assessment:** Zero risk to fix. Risk of not fixing: core use case remains entirely non-functional. This is the highest-priority action across the entire fleet.

---

### Finding 57 — inbox-state.json Still Invalid JSON: Day 2

**Risk:** HIGH
**Days Pending:** 2
**Previous:** Finding 49 (Day 28 Evening)

**Description:**
The duplicate `last_email_check_ms` key in `workspace/memory/inbox-state.json` was identified yesterday and has not been corrected. The `imessage_monitoring_paused` flag remains `true`. The email check timestamp is now 6–7 days stale, and the iMessage check timestamp is 10+ days stale. The deduplication state that prevents Heather from re-drafting the same email reply or iMessage multiple times is running on a malformed JSON file that strict parsers will reject entirely.

**Action:**
1. Remove the earlier duplicate `last_email_check_ms` entry; keep the later value `1777551900000`.
2. Set `imessage_monitoring_paused` to `false` (unless intentionally suspended — document the reason if so).
3. Validate with `python3 -m json.tool workspace/memory/inbox-state.json`.

**Risk Assessment:** Low risk to fix. Risk of not fixing: duplicate email drafts, missed messages, or silent crashes on any path reading this file.

---

### Finding 58 — AlphaClaw 0.8.0 Chrome DevTools MCP: New Capability for Josh's Mac

**Risk:** LOW (opportunity)
**Days Pending:** 0 (new this evening)

**Description:**
AlphaClaw 0.8.0 introduces native Chrome DevTools MCP, enabling Heather to control Josh's Mac from the VPS using Chrome's remote debugging protocol — no VNC, no screen-sharing application, no separate remote desktop tool. This is the first native solution in the AlphaClaw ecosystem for "agent needs to act on the Mac UI" workflows.

For Josh's personal assistant use case, this unlocks:
- Browser automation for web-only tools that lack an API
- Visual verification of email drafts before sending
- Calendar web UI interaction as a fallback when `gog calendar` fails
- Any task that currently requires Josh to be at his Mac

Prerequisites:
- AlphaClaw updated to 0.8.0 (verify current version in UI)
- OpenClaw upgraded to 2026.5.7 (Chrome DevTools MCP requires current OpenClaw)
- Google Chrome installed and running on Josh's Mac
- Chrome remote debugging enabled (port 9222 by default)

**Action:**
1. After upgrading OpenClaw to 2026.5.7 (Finding 54), verify AlphaClaw version from the dashboard.
2. Enable Chrome DevTools MCP from AlphaClaw settings.
3. Test with a simple navigation task.
4. Update TOOLS.md with the capability description and any Josh-specific browser automations it enables.

**Risk Assessment:** Low risk. Requires OpenClaw upgrade as prerequisite — do not attempt before Finding 54 is resolved.

---

### Finding 59 — Retired Fallback Model Still in openclaw.json: Day 2

**Risk:** MEDIUM
**Days Pending:** 2
**Previous:** Finding 53 (Day 28 Evening)

**Description:**
The fallback chain in `openclaw.json` still contains `openrouter/anthropic/claude-3.5-haiku`. This model is retired. Yesterday's implementation order estimated 5 minutes to fix. It has not been fixed. If the primary model (google/gemini-3-flash-preview) fails and the first fallback (openrouter/google/gemini-2.5-flash) also fails, the system routes to a retired model. Depending on OpenRouter's routing for retired models, this either silently fails or returns an error — in either case, the fallback chain terminates without a working response.

**Action:**
In `openclaw.json`, under `agents.defaults.model.fallbacks`, replace:
```
"openrouter/anthropic/claude-3.5-haiku"
```
with:
```
"openrouter/anthropic/claude-haiku-4-5"
```
Restart OpenClaw to apply. Estimated time: 3 minutes.

**Risk Assessment:** Low risk to fix. The replacement model (claude-haiku-4-5) is active and available on OpenRouter. Risk of not fixing: final fallback tier is non-functional.

---

### Finding 60 — SOUL.md Is a Generic Template After 29 Days of Operation

**Risk:** MEDIUM
**Days Pending:** 29

**Description:**
Heather's `workspace/SOUL.md` is the unmodified OpenClaw default template. It has not been edited since deployment. After 29 days of operation — 29 sessions, interactions with Josh, handling his calendar and inbox (in theory), learning his communication style — the agent's primary behavioral document contains zero Josh-specific content.

Specific gaps:

1. **No-emoji rule is absent from SOUL.md.** Josh's `USER.md` contains: "STRICT: DO NOT SEND EMOJI REACTIONS TO MESSAGES." But SOUL.md is weighted more heavily than USER.md in session initialization. The rule exists in the lower-priority document and is absent from the higher-priority one. This creates a behavioral contradiction.

2. **No timezone awareness.** Josh is in Los Angeles (America/Los_Angeles). SOUL.md has no mention of this. Heather has no documented sense of when Josh's day begins and ends, when to be proactive, or when to stay quiet.

3. **No escalation protocol.** SOUL.md says "when in doubt, ask before acting externally" but does not define what externally means, what the threshold is, or how to ask. For an agent with email and calendar write access, this is a critical gap.

4. **No daily rhythm.** No guidance on morning check-ins, quiet hours, or when to initiate versus wait.

5. **No personality accumulation.** After 29 sessions, SOUL.md should reflect something learned about Josh. It reflects nothing. Heather has no accumulated character.

The no-emoji rule gap is the most urgent: it was filed as Finding 55 (yesterday) and was called a 2-minute edit. It remains unimplemented on Day 2.

**Action:**
1. Add to SOUL.md, Boundaries section: *"Josh has explicitly requested NO emoji reactions on any message, ever. Not on Discord, not anywhere. This is an absolute rule, not a guideline."*
2. Add to SOUL.md, Vibe section: *"Josh is in Los Angeles (America/Los_Angeles / PDT). All times in PDT unless specified otherwise."*
3. Add to SOUL.md, Boundaries section: escalation protocol (see soul-improvements-2026-05-16-evening.md for full text).
4. Add to SOUL.md, Vibe section: daily rhythm guidance (morning proactive window, business hours responsiveness, evening summary, quiet hours).

**Risk Assessment:** Zero risk. Risk of not fixing: agent behavior diverges from Josh's stated preferences indefinitely.

---

## Persistent Findings — Status Table (Day 29)

| # | Title | Risk | Days Open |
|---|---|---|---|
| 48/56 | Google account never connected — CRITICAL | CRITICAL | 29 |
| 49/57 | inbox-state.json invalid JSON | HIGH | 2 |
| 50 | Memory system absent (no MEMORY.md) | MEDIUM | 29 |
| 51 | AGENTS.md not customized for Josh | MEDIUM | 29 |
| 52 | No active heartbeat | MEDIUM | Unknown |
| 53/59 | Retired fallback model claude-3.5-haiku | MEDIUM | 2 |
| 54 | 14+ releases behind current | MEDIUM | 53+ |
| 55/60 | SOUL.md generic template — no-emoji rule absent | MEDIUM | 2 |
| 58 | Chrome DevTools MCP opportunity | LOW (opp.) | 0 |
| Various | All findings 1–47 from prior scans | MIXED | 28–29 |

**Open: 60 | Resolved: 0 | Critical: 2 | High: 8+ | Medium: 25+ | Low: 5+**

---

## Implementation Order — Day 29

### Tonight (Under 10 Minutes Each, Zero Dependencies)

1. **Fix retired fallback model** (Finding 59): Edit one JSON string in `openclaw.json`. Replace `claude-3.5-haiku` with `claude-haiku-4-5`. Restart.
2. **Fix inbox-state.json** (Finding 57): Remove duplicate key. Unpause iMessage. Validate JSON.
3. **Add no-emoji rule to SOUL.md** (Finding 60): One sentence in the Boundaries section. Three-day-old ask from Josh's explicit instruction.

### This Weekend

4. **Connect Google Account** (Finding 56): Browser visit to AlphaClaw UI. 10 minutes. Unlocks Gmail, Calendar, Contacts — the entire purpose of this deployment.
5. **Update SOUL.md fully** (Finding 60): Add timezone, daily rhythm, escalation protocol.

### Next Week (After Google Account Connected)

6. **Create MEMORY.md** (Finding 50): Bootstrap with reconstructed Josh context.
7. **Customize AGENTS.md** (Finding 51): Replace template with Josh-specific instructions.
8. **Plan OpenClaw upgrade to 2026.5.7** (Finding 54): Review changelog, back up workspace, upgrade. Enables AlphaClaw 0.8.0 Chrome DevTools MCP.
9. **Design heartbeat** (Finding 52): Implement after upgrade — new heartbeat cadence improvements are in 2026.5.x.

---

*Generated by AlphaClaw Apex Fleet Research Agent — Evening Scan — 2026-05-16 (Day 29)*
