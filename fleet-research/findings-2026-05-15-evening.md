# Fleet Research — Josh / Heather Schwartz — Evening Scan

**Scan Date:** 2026-05-15 (Evening — Day 28)
**Agent:** AlphaClaw Apex Fleet Research Agent
**Instance:** Josh / Heather Schwartz — Discord bot personal assistant (iMessage, email, calendar, contacts)
**Previous Findings:** findings-2026-05-15-morning.md (Day 28 Morning, Findings 1–47)

---

## Platform News — New Since Morning Scan

| Version | Released | Headline |
|---|---|---|
| v2026.5.3 | Recent | File transfer plugin: `file_fetch`, `dir_list`, `dir_fetch`, `file_write` — binary ops between paired nodes, 16 MB ceiling |
| v2026.5.4 | Recent | Opt-in response caching via `X-OpenRouter-Cache` headers on OpenRouter-routed requests |
| AlphaClaw 0.8.0 | Recent | Chrome DevTools MCP — control Josh's Mac from the VPS |
| v2026.4.24 | Prior | Google Meet/Voice agent capability — bot can join Google Meet calls |
| Hermes Agent v0.8.0 | Reference | Background task notifications, live model switching, stronger security controls — relevant for heartbeat design |

Josh's instance is on OpenClaw 2026.3.22, now **13 releases behind** current (2026.5.7). Every item in the table above is unavailable until the version is updated.

---

## New Findings — Evening Scan (48–55)

---

### Finding 48 — Google Account Never Connected: Core Use Case Non-Functional Since Day 1

**Risk:** HIGH
**Days Pending:** 28 (existed since initial deployment)

**Description:**
`workspace/hooks/bootstrap/TOOLS.md` — the file injected at every session startup — contains the line:

> No Google accounts are currently configured.

This is not a degraded state. This is a zero-function state for the primary use case. Heather Schwartz was deployed as a personal assistant for Gmail, Google Calendar, and Google Contacts. The `gog` CLI, which is the tool that interfaces with all three of those services, requires a connected Google account. No Google account has ever been connected. Every session for 28 days has started with the bot silently unable to perform its core job. There is no error surfaced to Josh. The bot has been operating — drafting, responding, logging — as if the Gmail, Calendar, and Contacts capabilities exist, but any tool call that reaches `gog` has been failing silently or not being attempted at all.

The AlphaClaw UI General tab (https://5.78.142.81.sslip.io#general) is where the Google account OAuth connection is completed.

**Action:**
1. Open https://5.78.142.81.sslip.io#general in a browser.
2. Locate the Google account integration section.
3. Connect Josh's Google account via OAuth.
4. After connection, verify `workspace/hooks/bootstrap/TOOLS.md` no longer reads "No Google accounts are currently configured."
5. Run a manual `gog gmail list` from the VPS to confirm the credential is live.
6. Restart the bot session so the updated TOOLS.md is injected.

**Risk Assessment:** No risk to fix. Risk of not fixing: the bot's advertised capabilities (email, calendar, contacts) remain entirely non-functional. This is the single highest-priority action in the entire 28-day backlog.

---

### Finding 49 — inbox-state.json Is Invalid JSON (Duplicate Key)

**Risk:** HIGH
**Days Pending:** Unknown — file is in this state now

**Description:**
`workspace/memory/inbox-state.json` contains a duplicate JSON key, which makes the file invalid JSON:

```json
{
  "already_drafted_imessage_guids": [],
  "already_drafted_thread_ids": ["19db60d96d2118c8"],
  "imessage_monitoring_paused": true,
  "last_email_check_ms": 1777087800000,
  "last_imessage_check_ms": 1777271400000,
  "last_email_check_ms": 1777551900000
}
```

`last_email_check_ms` appears twice. Strict JSON parsers will reject this file entirely. Permissive parsers will silently use whichever value they encounter last (1777551900000), discarding the earlier one. The bot's deduplication logic — what prevents it from re-drafting the same email or iMessage reply twice — depends on this file being readable and accurate. If the file cannot be parsed, the bot loses track of its drafting state and may send duplicate responses or skip responses entirely.

Additionally: `imessage_monitoring_paused` is `true`. The last iMessage check timestamp (1777271400000) corresponds to approximately May 6–7 — **9 days ago**. The last email check timestamp corresponds to approximately May 9–10 — **5 days ago**. Neither service has been checked recently by any record in this file.

**Action:**
1. Fix the duplicate key by merging: keep `last_email_check_ms: 1777551900000` (the later, more recent value) and remove the earlier duplicate entry.
2. Set `imessage_monitoring_paused` to `false` unless iMessage monitoring is intentionally suspended.
3. Validate the resulting JSON with `python3 -m json.tool workspace/memory/inbox-state.json` before saving.
4. Audit what wrote this file — a bot action likely produced the malformed write. Add a JSON validation step to whatever code path updates this file.

**Risk Assessment:** Low risk to fix. Risk of not fixing: duplicate processing, missed messages, or silent crashes on any code path that reads this file.

---

### Finding 50 — Memory System Effectively Absent

**Risk:** MEDIUM
**Days Pending:** 28

**Description:**
`workspace/memory/` contains exactly two files:

- `inbox-state.json` — malformed (see Finding 49)
- `onboarding-google.md` — a setup artifact, not operational memory

There are no daily log files (no `YYYY-MM-DD.md` pattern). `MEMORY.md` was never created. After 28 days of operation, the bot has accumulated zero persistent memory about Josh's preferences, Heather's behavioral patterns, recurring contacts, email habits, calendar patterns, or anything else that would allow the assistant to improve over time. Every session starts cold. The bot cannot recall that it has seen a contact before, cannot build a preference model for Josh, and cannot refer back to what happened in previous sessions except through whatever the Discord channel history exposes.

This is the difference between a stateless tool and a personal assistant. After 28 days, this instance should have meaningful memory. It has none.

**Action:**
1. Create `workspace/memory/MEMORY.md` with sections for: Josh's preferences, recurring contacts, Heather's behavioral guidelines, known calendar patterns, and a session log index.
2. Create a memory-write hook or convention: at session end, Heather should append a brief entry to `workspace/memory/YYYY-MM-DD.md` summarizing what happened.
3. Update `SOUL.md` (or `AGENTS.md`) to instruct Heather to consult and update memory files at the start and end of each session.
4. Backfill as much as can be reconstructed from Discord history.

**Risk Assessment:** No risk to fix. Risk of not fixing: the assistant never develops context about Josh. The personal assistant value proposition requires persistent memory to materialize.

---

### Finding 51 — AGENTS.md Identical Across Both Repos, Not Customized for Josh

**Risk:** MEDIUM
**Days Pending:** 28

**Description:**
Both repos (primary and bootstrap) share byte-identical AGENTS.md files:

- Primary: SHA `3faead9716a2c168df79c2fba558bd04cd8c76d0`
- Bootstrap: SHA `1cfc2b557ee654714939481c2b039cecc42a3ee7`

These are unmodified template files. They contain no reference to Josh, to Heather Schwartz, to the specific tools in use (gog, iMessage tooling), to Discord as the interaction channel, or to the personal assistant use case. The agent operating from these files has no explicit instruction set tailored to this deployment. Whatever behavioral customization exists lives in SOUL.md alone (and SOUL.md has its own gaps — see soul-improvements-2026-05-14-evening.md, all of which remain unimplemented).

**Action:**
1. Edit `AGENTS.md` in both repos to replace template content with Josh-specific guidance: Heather's name and role, the tools available (gog, iMessage CLI, Discord), Josh's communication style preferences, escalation rules, and references to the memory system.
2. Ensure the bootstrap `AGENTS.md` and primary `AGENTS.md` are in sync (or intentionally differentiated if bootstrap serves a different purpose).
3. Cross-reference with SOUL.md to avoid contradictions.

**Risk Assessment:** No risk to fix. Risk of not fixing: the agent operates from generic instructions indefinitely, producing generic behavior.

---

### Finding 52 — HEARTBEAT.md Confirms No Active Heartbeat

**Risk:** MEDIUM
**Days Pending:** Unknown

**Description:**
`HEARTBEAT.md` is 168 bytes and contains only comments. There is no active heartbeat process. A heartbeat — a recurring background check that verifies the bot is alive, that monitored services are reachable, and that queued work is being processed — is a standard reliability primitive for an always-on assistant. Without it, the bot can silently stop functioning (as appears to have happened: iMessage last checked 9 days ago, email last checked 5 days ago) with no alert to Josh.

The Hermes Agent v0.8.0 pattern (background task notifications) is the reference implementation for how this should work in this ecosystem.

**Action:**
1. Design a minimal heartbeat: a scheduled task (cron or OpenClaw scheduler) that runs every 15–30 minutes, checks that the bot process is alive, attempts a lightweight tool call (e.g., `gog calendar list --days 1`), and posts a brief status to a Discord monitoring channel if anything is wrong.
2. Update `HEARTBEAT.md` with the actual heartbeat schedule, last-run timestamp, and status.
3. Consider alerting Josh directly (Discord DM) if the heartbeat fails two consecutive checks.

**Risk Assessment:** Low risk to implement. Risk of not fixing: silent failures continue. Josh has no visibility into whether the assistant is running at all.

---

### Finding 53 — Fallback Model List Includes Retired claude-3.5-haiku

**Risk:** LOW–MEDIUM
**Days Pending:** Unknown

**Description:**
The fallback chain includes `openrouter/anthropic/claude-3.5-haiku`. Claude 3.5 Haiku has been retired. If the primary model (google/gemini-3-flash-preview) fails and the first fallback (openrouter/google/gemini-2.5-flash) also fails, the system will attempt to route to a retired model. Depending on OpenRouter's behavior for retired models, this will either silently fail or return an error. In either case, the fallback is non-functional.

**Action:**
1. Replace `openrouter/anthropic/claude-3.5-haiku` in the fallback configuration with a currently-active Haiku-tier model. Current options: `openrouter/anthropic/claude-haiku-4-5` or an equivalent low-cost fast model.
2. Verify the full fallback chain with a forced-failure test on the primary model.
3. Document the fallback chain in AGENTS.md or a config note so it is visible during future fleet scans.

**Risk Assessment:** Low risk to fix (model string swap). Risk of not fixing: if primary and first fallback both fail, the bot hard-errors rather than degrading gracefully.

---

### Finding 54 — Version Gap (13 Releases) Blocks Material New Capabilities

**Risk:** LOW–MEDIUM
**Days Pending:** Up to ~53 days

**Description:**
Josh's instance is on OpenClaw 2026.3.22. Current is 2026.5.7. Thirteen releases separate them. The capabilities gated behind this gap include:

- **File transfer plugin** (v2026.5.3): `file_fetch`, `dir_list`, `dir_fetch`, `file_write` — binary file ops between paired nodes up to 16 MB. For Josh's use case, this enables iMessage attachment forwarding once iMessage is restored.
- **Response caching** (v2026.5.4): Opt-in via `X-OpenRouter-Cache` headers. Josh's fallback models are OpenRouter-routed. For recurring patterns (same email thread checked multiple times), this reduces token spend.
- **Google Meet/Voice agent** (v2026.4.24): Bot can join Google Meet calls. Relevant for Josh's CEO workflow.
- **Chrome DevTools MCP** (AlphaClaw 0.8.0): Control Josh's Mac from the VPS — a capability that did not exist at deployment time.

None of these are available at 2026.3.22.

**Action:**
1. Review the full OpenClaw changelog from 2026.3.22 to 2026.5.7 for any breaking changes before upgrading.
2. Back up `workspace/` and current configuration before the upgrade.
3. Upgrade to 2026.5.7.
4. After upgrade, enable the file transfer plugin and configure `X-OpenRouter-Cache` headers for the OpenRouter fallback profile.
5. Evaluate Chrome DevTools MCP for Josh's Mac control use case.

**Risk Assessment:** Moderate risk during upgrade (test in staging if possible). Risk of not upgrading: Josh continues to miss capabilities that are directly relevant to his stated use case.

---

### Finding 55 — Soul Improvements from 2026-05-14-Evening Remain Entirely Unimplemented

**Risk:** LOW
**Days Pending:** 1+

**Description:**
All recommendations from `soul-improvements-2026-05-14-evening.md` are unimplemented as of this evening scan:

- No-emoji rule is not in SOUL.md
- TOOLS.md remains a blank template
- AGENTS.md has not been customized (see Finding 51)
- MEMORY.md has not been created (see Finding 50)

These were filed as improvements 24+ hours ago. The soul improvement process is generating recommendations that are not being acted on. This creates a growing backlog of unfiled behavioral debt.

**Action:**
1. Add the no-emoji rule to SOUL.md immediately — this is a one-line edit.
2. Populate TOOLS.md with descriptions of the actual tools available to Heather (gog subcommands, iMessage CLI, Discord interaction patterns).
3. Action Findings 50 and 51 (MEMORY.md creation and AGENTS.md customization) in the same session.
4. Establish a cadence: soul improvement recommendations should be implemented within 24 hours of filing, or explicitly deferred with a reason.

**Risk Assessment:** No risk to fix. Risk of not fixing: soul improvement recommendations become performative — they are generated but never actioned, and the agent's behavior never improves.

---

## Persistent Findings — Full Status Table

| # | Title | Risk | Days Pending | Status |
|---|---|---|---|---|
| 1 | Primary model on preview/unstable channel | MEDIUM | 28 | Open |
| 2 | OpenClaw 13 releases behind (2026.3.22 vs 2026.5.7) | MEDIUM | ~53 | Open — see Finding 54 |
| 3 | google:default profile using api_key mode (not oauth) | LOW | 28 | Open |
| 4 | No staging environment for bot changes | MEDIUM | 28 | Open |
| 5 | Discord bot token rotation never performed | LOW | 28 | Open |
| 6 | No automated backup of workspace/ | MEDIUM | 28 | Open |
| 7 | SOUL.md missing no-emoji rule | LOW | 28 | Open — see Finding 55 |
| 8 | SOUL.md missing explicit escalation protocol | LOW | 28 | Open |
| 9 | No rate limiting on outbound email drafts | MEDIUM | 28 | Open |
| 10 | iMessage monitoring paused with no documented reason | HIGH | 9+ | Open — see Finding 49 |
| 11 | No deduplication audit since deployment | MEDIUM | 28 | Open |
| 12 | TOOLS.md is blank template | MEDIUM | 28 | Open — see Finding 55 |
| 13 | AGENTS.md not customized for Josh | MEDIUM | 28 | Open — see Finding 51 |
| 14 | MEMORY.md never created | MEDIUM | 28 | Open — see Finding 50 |
| 15 | No daily memory logs in workspace/memory/ | MEDIUM | 28 | Open — see Finding 50 |
| 16 | Bootstrap AGENTS.md identical to primary | LOW | 28 | Open — see Finding 51 |
| 17 | Fallback includes retired claude-3.5-haiku | MEDIUM | Unknown | Open — see Finding 53 |
| 18 | No heartbeat or liveness check | MEDIUM | Unknown | Open — see Finding 52 |
| 19 | No alerting to Josh on bot failure | MEDIUM | 28 | Open |
| 20 | gog CLI never verified functional (post-setup) | HIGH | 28 | Root cause now known: Finding 48 |
| 21 | Calendar integration untested | HIGH | 28 | Root cause now known: Finding 48 |
| 22 | Contacts integration untested | HIGH | 28 | Root cause now known: Finding 48 |
| 23 | No test suite for bot behaviors | LOW | 28 | Open |
| 24 | No canary message workflow | LOW | 28 | Open |
| 25 | inbox-state.json never validated | MEDIUM | 28 | Root cause confirmed: Finding 49 |
| 26 | No Discord command to check bot health | LOW | 28 | Open |
| 27 | No Discord command to pause/resume monitoring | LOW | 28 | Open |
| 28 | Email draft review workflow undefined | MEDIUM | 28 | Open |
| 29 | No max-drafts-per-hour safeguard | MEDIUM | 28 | Open |
| 30 | Google Calendar write access scope unverified | HIGH | 28 | Blocked on Finding 48 |
| 31 | No timezone handling specification in SOUL.md | LOW | 28 | Open |
| 32 | VPS SSH key rotation never performed | LOW | 28 | Open |
| 33 | No log rotation configured for bot logs | LOW | 28 | Open |
| 34 | OpenRouter API key exposure risk (config file) | MEDIUM | 28 | Open |
| 35 | No cost monitoring on OpenRouter fallback usage | LOW | 28 | Open |
| 36 | google/gemini-3-flash-preview may be deprecated without notice | MEDIUM | 28 | Open |
| 37 | No model performance baseline established | LOW | 28 | Open |
| 38 | iMessage GUID deduplication list empty | MEDIUM | 28 | Open |
| 39 | Single thread ID in drafted list — volume concern | LOW | 28 | Open |
| 40 | No weekly review cadence established | LOW | 28 | Open |
| 41 | Josh has no visibility into what Heather did each day | MEDIUM | 28 | Open |
| 42 | No session continuity between Discord interactions | MEDIUM | 28 | Open |
| 43 | Chrome DevTools MCP not available at current version | MEDIUM | ~53 | Open — see Finding 54 |
| 44 | File transfer plugin not available at current version | LOW | ~53 | Open — see Finding 54 |
| 45 | Google Meet agent capability not available at current version | LOW | ~33 | Open — see Finding 54 |
| 46 | Response caching not configured for OpenRouter fallbacks | LOW | Unknown | Open — see Finding 54 |
| 47 | No documented runbook for VPS or bot restart | MEDIUM | 28 | Open |
| 48 | Google account never connected — core use case non-functional | HIGH | 28 | **NEW — Evening** |
| 49 | inbox-state.json invalid JSON (duplicate key) | HIGH | Unknown | **NEW — Evening** |
| 50 | Memory system effectively absent | MEDIUM | 28 | **NEW — Evening** |
| 51 | AGENTS.md identical across repos, not customized | MEDIUM | 28 | **NEW — Evening** |
| 52 | HEARTBEAT.md confirms no active heartbeat | MEDIUM | Unknown | **NEW — Evening** |
| 53 | Fallback model list includes retired claude-3.5-haiku | LOW–MEDIUM | Unknown | **NEW — Evening** |
| 54 | Version gap (13 releases) blocks material new capabilities | LOW–MEDIUM | ~53 | **NEW — Evening** |
| 55 | Soul improvements from 2026-05-14-evening entirely unimplemented | LOW | 1+ | **NEW — Evening** |

---

## Implementation Order — Today's Priority (Evening)

**1. Connect Google Account (Finding 48) — Do this now**
Open https://5.78.142.81.sslip.io#general, connect Josh's Google account via OAuth. Verify TOOLS.md updates. Run `gog gmail list` from the VPS to confirm. This single action unlocks Gmail, Calendar, and Contacts — the entire stated purpose of this deployment.

**2. Fix inbox-state.json (Finding 49) — Do this tonight**
Remove the duplicate `last_email_check_ms` key. Set `imessage_monitoring_paused` to `false`. Validate JSON before saving.

**3. Replace Retired Fallback Model (Finding 53) — 5 minutes**
Swap `openrouter/anthropic/claude-3.5-haiku` for a current model.

**4. Add No-Emoji Rule to SOUL.md (Finding 55) — 2 minutes**
One-line edit. Pending since yesterday evening.

**5. Create MEMORY.md (Finding 50) — 30 minutes**
Bootstrap the memory system with a minimal structure.

**6. Customize AGENTS.md (Finding 51) — 45 minutes**
Replace template content with Josh-specific instructions.

**Defer to tomorrow:**
- OpenClaw upgrade to 2026.5.7 (Finding 54) — plan and test first
- Heartbeat implementation (Finding 52) — design before building
- Full soul improvements pass

---

*Generated by AlphaClaw Apex Fleet Research Agent — Evening Scan — 2026-05-15 (Day 28)*
