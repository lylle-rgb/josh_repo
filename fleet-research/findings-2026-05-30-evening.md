# Fleet Research: Findings — 2026-05-30 Evening Scan

## Scan Metadata

| Field | Value |
|---|---|
| Scan Date | 2026-05-30 (evening) |
| Scan Type | Incremental + Persistent Review |
| Instance | Heather Schwartz (Josh Meyers) |
| Repo | lylle-rgb/josh_repo |
| Previous Scan | 2026-05-29 evening |
| Scanner | AlphaClaw Fleet Research |
| AlphaClaw UI | https://5.78.142.81.sslip.io |

---

## Platform Status

| Item | Current | Latest Stable | Gap | Notes |
|---|---|---|---|---|
| OpenClaw version | 2026.3.22 | 2026.5.27 | **69 days** | Upgrade HIGH priority |
| Beta track | — | 2026.5.28-beta.2 | — | Not stable; do not target |
| AlphaClaw UI | Active | — | — | Accessible at sslip.io address |
| Primary model | google/gemini-3-flash-preview | — | — | As configured in openclaw.json |
| OpenRouter fallback | openrouter/anthropic/claude-3.5-haiku | — | — | Dead endpoint — remove |
| iMessage | Paused | — | — | Fix available in 2026.5.27 |
| Email | Active | — | — | Gmail checked, one thread drafted |

---

## New Findings — 2026-05-30 Evening (JOSH-71 through JOSH-76)

### JOSH-71 — Beta Releases 2026.5.28-beta.1 and beta.2 Detected (INFO)

**Priority:** INFO
**Status:** New — Do Not Act
**Detail:**
OpenClaw released two beta builds on 2026-05-29:
- `2026.5.28-beta.1` — subagent cwd/workspace separation
- `2026.5.28-beta.2` — session locks releasing on timeout; hook context staying prompt-local

These are not stable. The upgrade target remains `2026.5.27`. Tracking here for awareness. If beta.2 clears a stabilization window (typically 7–10 days without a hotfix), it may become the new stable target before Josh's upgrade is executed.

**Action:** Monitor only. No upgrade action until stable.

---

### JOSH-72 — Active Memory Plugin Available Post-Upgrade (HIGH)

**Priority:** HIGH
**Status:** New — Blocked on upgrade to 2026.5.27
**Detail:**
The Active Memory Plugin (introduced in OpenClaw 2026.4.12) will become available to Heather immediately upon upgrading from 2026.3.22 → 2026.5.27. This is directly relevant given JOSH-30 (MEMORY.md never created, 69+ days).

The plugin runs a dedicated memory sub-agent BEFORE each reply, pre-fetching relevant context from MEMORY.md and any daily note files matching the current date. This directly addresses Heather's zero long-term memory retention problem.

**Recommended config (to add to openclaw.json after upgrade):**
```json
{
  "plugin": "active-memory",
  "scope": "main",
  "channels": ["dm"],
  "queryMode": "recent",
  "timeoutMs": 15000,
  "maxSummaryChars": 220
}
```

Full config block and file path documented in `soul-improvements-2026-05-30-evening.md`.

**Action:** Prepare config now; apply immediately post-upgrade.

---

### JOSH-73 — inbox-state.json Confirms iMessage Paused, Email Active (MEDIUM)

**Priority:** MEDIUM
**Status:** New confirmation of known iMessage issue
**Detail:**
`workspace/memory/inbox-state.json` was read and confirms:
- `imessage_monitoring_paused: true` — iMessage has been suspended. The iMessage fix is included in 2026.5.27. No workaround exists on current version.
- Email is actively being checked — recent timestamps present.
- One Gmail thread has been drafted (not yet sent or confirmed sent).

**Action:** iMessage restoration is tied to the 2026.5.27 upgrade (JOSH-39/66). No independent action required here, but flag for Josh that iMessage is NOT currently operational.

---

### JOSH-74 — Google Integration: API Key Mode, Not gog/OAuth (INFO)

**Priority:** INFO
**Status:** New clarification
**Detail:**
`hooks/bootstrap/TOOLS.md` states "No Google accounts are currently configured." This is technically accurate for the `gog`-cli OAuth flow, but Josh's Google connectivity (Gmail, Calendar) is handled via native API key mode — not the gog/OAuth path. This is not a bug or misconfiguration.

However, this creates a potential confusion point: any automated diagnostic or future team member reading TOOLS.md may incorrectly conclude Google is disconnected. The file should note the API key mode integration to avoid false alarms.

**Action:** Add a clarifying comment to `hooks/bootstrap/TOOLS.md` (GitHub-only edit).

---

### JOSH-75 — 69 Days of Email Activity, Zero Long-Term Memory (CRITICAL ESCALATION)

**Priority:** CRITICAL — Escalating from HIGH
**Status:** Confirmed by filesystem scan
**Detail:**
`workspace/memory/` contains exactly two files:
- `inbox-state.json`
- `onboarding-google.md`

There are NO daily `YYYY-MM-DD.md` files and NO `MEMORY.md`. This means 69 days of email processing, calendar events, and assistant interactions have produced zero persistent memory. Every session Heather starts is completely blank — no recollection of Josh's ongoing projects, threads, relationships, or preferences beyond what is in USER.md.

Combined with JOSH-72 (Active Memory Plugin) and JOSH-30 (MEMORY.md missing), this is the single highest-impact gap in the deployment.

**Action:** Create MEMORY.md immediately (GitHub-only, template in soul-improvements doc). Configure Active Memory Plugin post-upgrade.

---

### JOSH-76 — SEC AI Monitoring Context: Proactive Heartbeat Validated (INFO)

**Priority:** INFO / Strategic context
**Status:** New external context
**Detail:**
The SEC is deploying AI-powered "zero-tolerance" market monitoring infrastructure. This is externally relevant context validating the importance of proactive monitoring posture for all AI assistants operating in business/financial contexts. Josh runs Bliss Lifestyle and partners on Oben HiFi — both consumer-facing businesses where brand reputation and communication responsiveness matter.

Heather's HEARTBEAT.md being empty for 69 days (JOSH-31/69) means there is no proactive monitoring loop at all — no scheduled email checks, no calendar awareness, no memory maintenance. This is the exact gap this external trend makes more acute.

**Action:** Reinforces priority of populating HEARTBEAT.md with proactive monitoring tasks (template in soul-improvements doc). GitHub-only edit.

---

## Persistent Findings — All Unresolved Items

| ID | Summary | Priority | Days Open | Action Type | Status |
|---|---|---|---|---|---|
| JOSH-30 | MEMORY.md never created | CRITICAL | 69+ | GitHub-only | Unresolved |
| JOSH-31/69 | HEARTBEAT.md empty | HIGH → ESCALATING | 69 | GitHub-only | Unresolved |
| JOSH-34/70 | Emoji contradiction: AGENTS.md vs USER.md | MEDIUM | 69 | GitHub-only | Unresolved |
| JOSH-37 | SOUL.md never personalized | MEDIUM | 69 | GitHub-only | Unresolved |
| JOSH-39/66 | Upgrade to 2026.5.27 (iMessage fix, memory plugin) | HIGH | 69 | VPS-required | Unresolved |
| JOSH-42 | ClawHub skills security advisory | MEDIUM | — | VPS-required | Unresolved |
| JOSH-50 | Dead OpenRouter fallback in openclaw.json | MEDIUM | — | GitHub-only | Unresolved |
| JOSH-55 | TOOLS.md completely empty | MEDIUM | — | GitHub-only | Unresolved |
| JOSH-63 | BOOTSTRAP.md never deleted | MEDIUM | 69 | GitHub-only | Unresolved |
| JOSH-67 | Security group prompt isolation (post-upgrade) | HIGH | — | VPS-required (post-upgrade) | Blocked on upgrade |
| JOSH-68 | Discord voice/wake improvements (post-upgrade) | INFO | — | VPS-required (post-upgrade) | Blocked on upgrade |
| JOSH-71 | Beta 2026.5.28-beta.1/.2 detected | INFO | 0 | Monitor only | New — tracking |
| JOSH-72 | Active Memory Plugin available post-upgrade | HIGH | 0 | GitHub-only (prep) + VPS (apply) | New — blocked on upgrade |
| JOSH-73 | iMessage paused confirmed in inbox-state.json | MEDIUM | 0 | VPS-required (via upgrade) | New — confirmed |
| JOSH-74 | Google API key mode vs gog/OAuth clarification | INFO | 0 | GitHub-only | New |
| JOSH-75 | 69 days email activity, zero memory retention | CRITICAL | 0 | GitHub-only + VPS (post-upgrade) | New — escalated |
| JOSH-76 | SEC AI monitoring context — heartbeat urgency | INFO | 0 | Strategic note | New |

---

## Immediate Action List

### GitHub-Only (No VPS Access Required) — Do These First

1. **[CRITICAL] Create MEMORY.md** — `MEMORY.md` in repo root. Template in soul-improvements doc. Resolves JOSH-30 and partially addresses JOSH-75.
2. **[HIGH] Populate HEARTBEAT.md** — Replace empty file with full proactive monitoring task list. Template in soul-improvements doc. Resolves JOSH-31/69 and JOSH-76.
3. **[MEDIUM] Fix AGENTS.md emoji contradiction** — Add Josh-specific override block at TOP of `AGENTS.md` disabling emoji reactions, referencing USER.md rule. Exact text in soul-improvements doc. Resolves JOSH-34/70.
4. **[MEDIUM] Personalize SOUL.md** — Add Heather-specific personality notes for luxury brand founder context. Additions in soul-improvements doc. Resolves JOSH-37.
5. **[MEDIUM] Populate TOOLS.md** — Document actual tool integrations (Gmail API key mode, Discord, iMessage paused). Resolves JOSH-55.
6. **[MEDIUM] Fix openclaw.json dead fallback** — Remove `openrouter/anthropic/claude-3.5-haiku` from models list. Resolves JOSH-50.
7. **[MEDIUM] Delete BOOTSTRAP.md** — Should have been removed at go-live 69 days ago. Resolves JOSH-63.
8. **[INFO] Clarify hooks/bootstrap/TOOLS.md** — Add note that Google is connected via API key mode, not gog/OAuth. Resolves JOSH-74.

### VPS-Required (Require Server Access)

1. **[HIGH] Upgrade OpenClaw 2026.3.22 → 2026.5.27** — Resolves JOSH-39/66, enables iMessage (JOSH-73), enables Active Memory Plugin (JOSH-72), enables security group prompt isolation (JOSH-67), enables Discord improvements (JOSH-68). Do NOT upgrade to beta.
2. **[HIGH — post-upgrade] Apply Active Memory Plugin config** — Add plugin entry to openclaw.json after upgrade. Config in soul-improvements doc. Resolves JOSH-72.
3. **[HIGH — post-upgrade] Enable security group prompt isolation** — Resolves JOSH-67.
4. **[HIGH — post-upgrade] Verify iMessage resumes** — Confirm `imessage_monitoring_paused` clears after upgrade.
5. **[MEDIUM] Review ClawHub skills security advisory** — Resolves JOSH-42.

---

## Platform Research Notes

### OpenClaw Beta Tracking (as of 2026-05-30)

- `2026.5.27` — Current stable. Upgrade target for Josh.
- `2026.5.28-beta.1` — Released 2026-05-29. Subagent cwd/workspace separation.
- `2026.5.28-beta.2` — Released 2026-05-29. Session locks releasing on timeout; hook context staying prompt-local.
- Beta stabilization window typically 7–10 days. If no hotfix by ~2026-06-08, beta.2 may be promoted to stable. At that point, reconsider upgrade target before executing Josh's upgrade.

### Active Memory Plugin — Background

Introduced in 2026.4.12. Not available on Josh's current 2026.3.22. The plugin addresses a long-standing limitation of OpenClaw base installs: without it, there is no mechanism for the agent to consult accumulated memory before replying. All context must be in the system prompt or the current conversation. With the plugin enabled:

- A memory sub-agent runs BEFORE each reply.
- It reads MEMORY.md and any matching daily note (`workspace/memory/YYYY-MM-DD.md`).
- It injects a compact summary (capped at `maxSummaryChars`) into the reply context.
- The `timeoutMs` setting (15000ms recommended) ensures the main agent is not blocked if the memory file is large or the sub-agent is slow.

Scoping to `main` agent + `dm` channels only avoids memory overhead in automated/hook contexts where it is not useful.

### Google Integration Mode

Josh's instance uses Google native API key integration, not the `gog`-cli OAuth flow. The `gog` path shows "no accounts configured" which is accurate for that specific path only. Gmail and Calendar access are functional via the API key configured at install. No action required on Google connectivity — but documentation should reflect this to avoid false alerts in future scans.

### iMessage Status

iMessage monitoring was paused at some point during the 69-day deployment. The `inbox-state.json` file shows `imessage_monitoring_paused: true`. The 2026.5.27 release notes include an iMessage fix. Once upgraded, iMessage should resume automatically — confirm in the post-upgrade checklist that the flag clears.

*Document generated: 2026-05-30 evening — fleet research*
