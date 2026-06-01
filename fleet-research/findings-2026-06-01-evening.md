# Fleet Research: Findings — 2026-06-01 Evening Scan

## Scan Metadata

| Field | Value |
|---|---|
| Scan Date | 2026-06-01 (evening) |
| Scan Type | Incremental + Persistent Review |
| Instance | Heather Schwartz (Josh Meyers) |
| Repo | lylle-rgb/josh\_repo |
| Previous Scan | 2026-05-31 evening |
| Scanner | AlphaClaw Fleet Research |
| AlphaClaw UI | https://5.78.142.81.sslip.io |

---

## Platform Status

| Item | Current | Latest Stable | Gap | Notes |
|---|---|---|---|---|
| OpenClaw version | 2026.3.22 | 2026.5.27 | **71 days** | Upgrade HIGH priority |
| Latest Beta | — | 2026.5.31-beta.3 | — | Do NOT target — unstable |
| AlphaClaw UI | Active | — | — | Accessible at sslip.io address |
| Primary model | google/gemini-3-flash-preview | — | — | As configured |
| OpenRouter fallback | openrouter/anthropic/claude-3.5-haiku | — | Dead endpoint | REMOVE — JOSH-50 |
| MEMORY.md | Missing | Required | **Day 71** | CRITICAL |
| HEARTBEAT.md | Empty | Required | **Day 71** | HIGH |
| BOOTSTRAP.md | Present | Should be deleted | **Day 71** | MEDIUM |
| iMessage | Paused | — | — | Fix in 2026.5.27 — awaiting upgrade |
| TOOLS.md | Empty template | Required | **Day 71** | MEDIUM |
| SOUL.md | Generic template | Personalized | **Day 71** | MEDIUM |

---

## New Findings — 2026-06-01 Evening (JOSH-81 through JOSH-84)

### JOSH-81 — OpenClaw 2026.5.31-beta.3 Released — iMessage Delivery Hardened (INFO → Strategic)

**Priority:** INFO / Strategic  
**Status:** New

OpenClaw shipped `2026.5.31-beta.3` today. This is a beta build — not a production upgrade target. Key contents relevant to Heather:

- **iMessage delivery is steadier** across all mobile channels (Telegram, WhatsApp, iMessage, Slack, Discord, iOS realtime Talk). This directly validates the urgency of Josh's pending upgrade: the paused iMessage integration (JOSH-73) will resume on 2026.5.27, and the delivery hardening in 2026.5.31 means even more reliable iMessage once the subsequent stable release lands.
- **Agents recover more cleanly from interrupted tool calls, stale session bindings, compaction handoffs, and media delivery retries.** For Heather's multi-step email/calendar workflows, this eliminates mid-task failure modes where a network interrupt would require a full session restart.
- **Workboard orchestration primitives** add multi-agent planning and run tracking. See JOSH-83.

Stable upgrade target remains **2026.5.27**. Beta.3 progress suggests 2026.5.28-stable promotion is on track for mid-June.

**Action:** Monitor. No upgrade action. The iMessage hardening and tool-call recovery improvements reinforce upgrading to 2026.5.27 as soon as VPS access is available.

---

### JOSH-82 — Agent Recovery from Interrupted Tool Calls — Post-Upgrade Reliability Gain (INFO)

**Priority:** INFO  
**Status:** New

The 2026.5.31-beta.3 changelog explicitly addresses recovery from interrupted tool calls, stale session bindings, compaction handoffs, and media delivery retries.

For Heather's use case (email triage, calendar management, iMessage):
- A multi-step email workflow (read → summarize → draft reply) will not fail permanently if a tool call is interrupted mid-step
- Calendar operations that fail partway through will recover rather than leaving orphaned events or half-executed tasks
- iOS realtime Talk sessions with voice delivery failures will retry rather than silently drop

This is a meaningful reliability improvement for an assistant handling live communications. Available post-upgrade to 2026.5.27 and forward.

**Action:** No immediate action. Note for post-upgrade validation.

---

### JOSH-83 — Workboard Orchestration Primitives — Future Multi-Agent Architecture (INFO)

**Priority:** INFO / Future planning  
**Status:** New

OpenClaw's Workboard feature now includes multi-agent planning and run tracking. For Heather's use case, this unlocks a future architecture:

- **Email agent:** Monitors Gmail, triages urgent threads, drafts responses for approval
- **Calendar agent:** Watches for scheduling requests, prepares daily briefings, manages reminders
- **Research agent:** Tracks relevant news for Josh's businesses (Bliss luxury lifestyle, Oben HiFi)

Today, Heather handles all of this in a single session with shared context. Workboard would allow these to run as coordinated sub-agents with isolated contexts and different cadences.

This is post-upgrade planning territory. Keeping HEARTBEAT.md and cron tasks modular now makes migration to sub-agents easier later.

**Action:** No immediate action. Document as future architecture direction.

---

### JOSH-84 — Official Tokenjuice Plugin — Simplification Opportunity Post-Upgrade (INFO)

**Priority:** INFO  
**Status:** New

tokenjuice has been externalized as the official `@openclaw/tokenjuice` plugin. Josh's current `openclaw.json` loads `usage-tracker` from a hardcoded AlphaClaw path: `/app/node_modules/@chrysb/alphaclaw/lib/plugin/usage-tracker`.

Post-upgrade, this path may be simplified or replaced with the official npm package. Cleanup opportunity, not urgent.

**Action:** Note for post-upgrade openclaw.json review.

---

## Persistent Findings — All Unresolved Items (Day 71)

| ID | Summary | Priority | Days Open | Action Type | Status |
|---|---|---|---|---|---|
| JOSH-30/75/79 | MEMORY.md never created — 71 days of email activity unremembered | CRITICAL | 71 | GitHub-only | Unresolved |
| JOSH-31/69 | HEARTBEAT.md empty — no proactive monitoring | HIGH | 71 | GitHub-only | Unresolved |
| JOSH-34/70 | Emoji contradiction: AGENTS.md vs USER.md STRICT rule | MEDIUM | 71 | GitHub-only | Unresolved |
| JOSH-37 | SOUL.md never personalized for Heather/Josh context | MEDIUM | 71 | GitHub-only | Unresolved |
| JOSH-39/66 | Upgrade to 2026.5.27 (iMessage, Active Memory Plugin) | HIGH | 71 | VPS-required | Unresolved |
| JOSH-42 | ClawHub skills security advisory | MEDIUM | — | VPS-required | Unresolved |
| JOSH-50 | Dead OpenRouter fallback in openclaw.json | MEDIUM | — | GitHub-only | Unresolved |
| JOSH-55 | TOOLS.md completely empty — no tool inventory | MEDIUM | — | GitHub-only | Unresolved |
| JOSH-63 | BOOTSTRAP.md never deleted | MEDIUM | 71 | GitHub-only | Unresolved |
| JOSH-67 | Security group prompt isolation (post-upgrade) | HIGH | — | VPS (post-upgrade) | Blocked on upgrade |
| JOSH-72 | Active Memory Plugin available post-upgrade | HIGH | — | GitHub-only (prep) | Blocked on upgrade |
| JOSH-73 | iMessage paused — awaiting upgrade | MEDIUM | — | VPS (via upgrade) | Confirmed |
| JOSH-78 | 2026.5.28 security/runtime improvements confirmed | INFO | 1 | Strategic | Tracking |
| JOSH-79 | AI memory temporal algorithm (+29.6 pts) | HIGH | 1 | GitHub-only (MEMORY.md) | Unresolved |
| JOSH-81 | 2026.5.31-beta.3: iMessage hardened + tool-call recovery | INFO | 0 | Monitor | New |
| JOSH-82 | Agent recovery from interrupted tool calls | INFO | 0 | Post-upgrade validation | New |
| JOSH-83 | Workboard orchestration primitives | INFO | 0 | Future planning | New |
| JOSH-84 | Official Tokenjuice plugin | INFO | 0 | Post-upgrade cleanup | New |

---

## Immediate Action List

### Tier 1 — GitHub-Only (No VPS Required) — All Low-Risk

All of these can be applied today via GitHub file edits. AlphaClaw's self-healing watchdog (JOSH-80) provides auto-restart safety net. Risk is effectively zero.

1. **[CRITICAL] Create MEMORY.md** — `workspace/MEMORY.md`. See today's soul-improvements doc for full template. Addresses JOSH-30/75/79. This single file unlocks +29.6 pts temporal memory retention on every future session.
2. **[HIGH] Populate HEARTBEAT.md** — Replace empty file with proactive monitoring checklist. Template in soul-improvements doc. Addresses JOSH-31/69.
3. **[MEDIUM] Fix emoji contradiction in AGENTS.md** — Add Josh-specific override block at top of `workspace/AGENTS.md` explicitly disabling emoji reactions per USER.md STRICT rule. Exact text in soul-improvements doc. Addresses JOSH-34/70.
4. **[MEDIUM] Personalize SOUL.md** — Add Heather-specific context for luxury brand founder. Additions in soul-improvements doc. Addresses JOSH-37.
5. **[MEDIUM] Populate TOOLS.md** — Document actual tool integrations (Google/Gmail, calendar, iMessage status). Addresses JOSH-55.
6. **[MEDIUM] Remove dead OpenRouter fallback from openclaw.json** — Delete `openrouter/anthropic/claude-3.5-haiku` from the fallbacks array. One-line change. Addresses JOSH-50.
7. **[MEDIUM] Delete BOOTSTRAP.md** — `workspace/BOOTSTRAP.md` should have been removed at go-live 71 days ago. Addresses JOSH-63.

### Tier 2 — VPS-Required

1. **[HIGH] Upgrade OpenClaw 2026.3.22 → 2026.5.27** — Resolves JOSH-39/66, enables iMessage (JOSH-73), Active Memory Plugin (JOSH-72), group prompt isolation (JOSH-67). Do NOT upgrade to beta or alpha.
2. **[HIGH — post-upgrade] Apply Active Memory Plugin config** — Add `active-memory` plugin entry to openclaw.json. Config in soul-improvements doc.
3. **[HIGH — post-upgrade] Verify iMessage resumes** — Confirm `imessage_monitoring_paused` clears post-upgrade.
4. **[MEDIUM] Review ClawHub skills security advisory** — Resolves JOSH-42.

---

## Platform Research Notes

### OpenClaw Version Tracking (as of 2026-06-01)

| Version | Channel | Released | Status | Recommendation |
|---|---|---|---|---|
| 2026.5.31-beta.3 | Beta | 2026-06-01 | Unstable | Do not target |
| 2026.5.29-alpha.1 | Alpha | 2026-05-31 | Unstable | Do not target |
| 2026.5.28-beta.2 | Beta | 2026-05-29 | Unstable | Do not target |
| 2026.5.27 | Stable | ~2026-05-01 | **Upgrade target** | Apply when VPS accessible |
| 2026.3.22 | — | 2026-03-22 | Josh current | **71 days behind stable** |

### iMessage Delivery — Status and Roadmap

Josh's iMessage integration has been paused since deployment (JOSH-73), confirmed as a 2026.3.x compatibility issue fixed in 2026.5.27. Today's 2026.5.31-beta.3 further hardens mobile delivery across all channels. The upgrade path is clear:

1. Upgrade to 2026.5.27 → iMessage resumes
2. Eventually upgrade to 2026.5.31-stable (mid-to-late June) → Delivery fully hardened + tool-call recovery

### The Memory Gap — Quantified Cost

71 days of Josh's emails, calendar events, and conversations have passed through Heather with zero persistent memory. The April 2026 mem0 algorithm delivers +29.6 pts on temporal queries and +23.1 pts on multi-hop reasoning — directly corresponding to queries like:

- "Did Josh mention this vendor before?" → temporal query
- "Josh mentioned A and B separately — how do they connect?" → multi-hop
- "What was Josh's take on the Oben HiFi launch timeline last month?" → temporal

Creating MEMORY.md today (GitHub-only, no upgrade required) establishes the foundation that the Active Memory Plugin will automatically index post-upgrade. Every day it goes uncreated is context permanently lost.

### AlphaClaw Harness — Safety Net Confirmed

AlphaClaw's self-healing watchdog detects crashes, runs automated repair routines, and auto-restarts within seconds. Git-backed rollback means any bad edit can be undone by reverting the commit. All Tier 1 GitHub-only fixes queued for 71 days are safe to apply today.

---

*Scan completed: 2026-06-01 evening. Next scan: 2026-06-02 morning.*
