# Fleet Research: Findings — 2026-05-31 Evening Scan

## Scan Metadata

| Field | Value |
|---|---|
| Scan Date | 2026-05-31 (evening) |
| Scan Type | Incremental + Persistent Review |
| Instance | Heather Schwartz (Josh Meyers) |
| Repo | lylle-rgb/josh\_repo |
| Previous Scan | 2026-05-30 evening |
| Scanner | AlphaClaw Fleet Research |
| AlphaClaw UI | https://5.78.142.81.sslip.io |

---

## Platform Status

| Item | Current | Latest Stable | Gap | Notes |
|---|---|---|---|---|
| OpenClaw version | 2026.3.22 | 2026.5.27 | **70 days** | Upgrade HIGH priority |
| Latest Alpha | — | 2026.5.29-alpha.1 | — | Do NOT target — unstable |
| Latest Beta | — | 2026.5.28-beta.2 | — | Do NOT target — unstable |
| AlphaClaw UI | Active | — | — | Accessible at sslip.io address |
| Primary model | google/gemini-3-flash-preview | — | — | As configured |
| OpenRouter fallback | openrouter/anthropic/claude-3.5-haiku | — | Dead endpoint | Remove |
| MEMORY.md | Missing | Required | **Day 70** | CRITICAL |
| HEARTBEAT.md | Empty | Required | **Day 70** | HIGH |
| iMessage | Paused | — | — | Fix in 2026.5.27 — awaiting upgrade |
| Email | Active | — | — | Gmail via API key |

---

## New Findings — 2026-05-31 Evening (JOSH-77 through JOSH-80)

### JOSH-77 — New Alpha 2026.5.29-alpha.1 Detected (INFO)

**Priority:** INFO
**Status:** New — Monitor Only

OpenClaw shipped `2026.5.29-alpha.1` today. This is an alpha channel build — not eligible for upgrade targeting. It appears to include pre-stabilization work on subagent workspace separation and hook context isolation, continuing the work begun in the 2026.5.28-beta series.

The upgrade target for Josh's production instance remains `2026.5.27` (stable). Once the 2026.5.28 series completes its stabilization window (estimated mid-June based on typical 7–10 day window from beta.2 on 2026-05-29), that will become the new target.

**Action:** Monitor. No upgrade action. If no hotfix by ~2026-06-08, re-evaluate whether 2026.5.28-stable or 2026.5.27 becomes the target for Josh's upgrade.

---

### JOSH-78 — OpenClaw 2026.5.28 Security and Runtime Improvements Confirmed (INFO → Strategic)

**Priority:** INFO / Strategic context for post-upgrade roadmap
**Status:** New — changelog reviewed

The 2026.5.28-beta series changelog confirms the following improvements are coming in the next stable release after 2026.5.27:

**Security hardening:**
- Group prompt text is no longer injected into the system prompt — prevents prompt injection attacks from group chat participants
- No-auth Tailscale exposure is now rejected — improves gateway security posture
- Node/device-role approvals now require admin authority — prevents privilege escalation

**Runtime reliability:**
- Session locks now release on timeout abort — prevents Heather getting stuck in deadlocked sessions
- Hook context is now prompt-local — HEARTBEAT.md hooks will no longer bleed context across sessions
- Stale restart continuations are avoided — cleaner agent restart behavior

**Performance:**
- Gateway startup no longer repeats expensive plugin/channel/session/filesystem scans on each boot
- User-facing replies are separated from slower follow-up processing — faster perceived response time
- Gateway caches churn less under load

**Relevance to Heather:** JOSH-67 (security group prompt isolation) is directly addressed by the first security item above. The prompt-local hook context improvement is directly relevant to Heather's HEARTBEAT.md functionality. These confirm that upgrading to 2026.5.27 first, then to 2026.5.28-stable (mid-June), is the optimal two-step path.

**Action:** No immediate action. Incorporate into upgrade roadmap planning.

---

### JOSH-79 — AI Memory Temporal Algorithm (+29.6 pts) — Validates MEMORY.md Urgency (HIGH)

**Priority:** HIGH — External validation
**Status:** New external context

A new token-efficient memory algorithm was published in April 2026 (mem0 research), benchmarking:
- **+29.6 points** on temporal queries (tracking facts that change over time)
- **+23.1 points** on multi-hop reasoning vs. prior memory architectures

This is the algorithm underlying OpenClaw's Active Memory Plugin (active-memory, released in 2026.4.12 — available post-upgrade).

This finding directly quantifies the cost of JOSH-75 (70 days of email activity with zero persistent memory). Every personal AI assistant deployed in 2026 now ships persistent memory by default. Heather is operating without any memory architecture because:
1. MEMORY.md was never created (JOSH-30)
2. The Active Memory Plugin requires 2026.5.x (JOSH-39)
3. The upgrade from 2026.3.22 has not been executed

The gap is now measurable: Heather is missing an estimated +29.6 points of temporal context retention on queries like "did Josh mention this vendor before?" or "what did Josh say about the Oben HiFi launch timeline?" — questions that require temporal lookup across past interactions.

**Action (GitHub-only, immediate):** Create MEMORY.md stub now. It requires no upgrade — the file exists as a reference for Heather to read at session start. The Active Memory Plugin will automatically locate and index it post-upgrade. Template in today's soul-improvements doc.

---

### JOSH-80 — AlphaClaw Self-Healing Watchdog Confirmed — GitHub-Only Fixes Are Low-Risk (INFO)

**Priority:** INFO
**Status:** New — fleet context

Research confirms AlphaClaw's harness includes a self-healing watchdog that detects crashes, runs automated repair routines, and restarts the agent environment automatically. It also supports Git-backed rollback — any misconfigured openclaw.json or workspace file can be reverted without VPS SSH access, simply by reverting the GitHub commit.

This is relevant for Josh's deployment for one key reason: multiple GitHub-only fixes have been queued for 70 days (JOSH-34/50/55/63/74/75) without being applied, presumably due to uncertainty about risk. The AlphaClaw watchdog eliminates that concern:

- If a GitHub file edit causes an unexpected issue, the watchdog restarts Heather within seconds
- Git-backed rollback means any bad edit can be undone by reverting the commit
- The only real risk is the openclaw.json dead fallback removal (JOSH-50), and that only improves reliability

**Action:** No immediate action, but this validates that all queued GitHub-only fixes are safe to apply today. The watchdog provides a safety net.

---

## Persistent Findings — All Unresolved Items (Day 70)

| ID | Summary | Priority | Days Open | Action Type | Status |
|---|---|---|---|---|---|
| JOSH-30/75 | MEMORY.md never created — 70 days email activity unremembered | CRITICAL | 70 | GitHub-only | Unresolved |
| JOSH-31/69 | HEARTBEAT.md empty — no proactive monitoring | HIGH | 70 | GitHub-only | Unresolved |
| JOSH-34/70 | Emoji contradiction: AGENTS.md vs USER.md STRICT rule | MEDIUM | 70 | GitHub-only | Unresolved |
| JOSH-37 | SOUL.md never personalized for Heather/Josh context | MEDIUM | 70 | GitHub-only | Unresolved |
| JOSH-39/66 | Upgrade to 2026.5.27 (iMessage, Active Memory Plugin) | HIGH | 70 | VPS-required | Unresolved |
| JOSH-42 | ClawHub skills security advisory | MEDIUM | — | VPS-required | Unresolved |
| JOSH-50 | Dead OpenRouter fallback in openclaw.json | MEDIUM | — | GitHub-only | Unresolved |
| JOSH-55 | TOOLS.md completely empty — no tool inventory | MEDIUM | — | GitHub-only | Unresolved |
| JOSH-63 | BOOTSTRAP.md never deleted | MEDIUM | 70 | GitHub-only | Unresolved |
| JOSH-67 | Security group prompt isolation (post-upgrade) | HIGH | — | VPS (post-upgrade) | Blocked on upgrade |
| JOSH-68 | Discord voice/wake improvements (post-upgrade) | INFO | — | VPS (post-upgrade) | Blocked on upgrade |
| JOSH-71 | Beta 2026.5.28-beta.1/.2 detected | INFO | 1 | Monitor | Tracking |
| JOSH-72 | Active Memory Plugin available post-upgrade | HIGH | 1 | GitHub-only (prep) | Blocked on upgrade |
| JOSH-73 | iMessage paused — confirmed inbox-state.json | MEDIUM | 1 | VPS (via upgrade) | Confirmed |
| JOSH-74 | Google API key mode vs gog/OAuth clarification needed | INFO | 1 | GitHub-only | Unresolved |
| JOSH-77 | Alpha 2026.5.29-alpha.1 detected | INFO | 0 | Monitor | New |
| JOSH-78 | 2026.5.28 security/runtime improvements confirmed | INFO | 0 | Strategic note | New |
| JOSH-79 | AI memory temporal algorithm (+29.6 pts) — memory urgency | HIGH | 0 | GitHub-only (MEMORY.md) | New |
| JOSH-80 | AlphaClaw self-healing watchdog — GitHub fixes are low-risk | INFO | 0 | Strategic note | New |

---

## Immediate Action List

### Tier 1 — GitHub-Only (No VPS Required) — All Low-Risk

These can be applied today via GitHub file edits. The AlphaClaw watchdog provides auto-restart safety net.

1. **[CRITICAL] Create MEMORY.md** — `workspace/MEMORY.md`. Template in today's soul-improvements doc. Directly addresses JOSH-30/75/79.
2. **[HIGH] Populate HEARTBEAT.md** — Replace empty file with proactive monitoring checklist. Template in soul-improvements doc. Addresses JOSH-31/69.
3. **[MEDIUM] Fix AGENTS.md emoji contradiction** — Add Josh-specific override block at TOP of `workspace/AGENTS.md` (before all other content) explicitly disabling emoji reactions per USER.md STRICT rule. Exact text in soul-improvements doc. Addresses JOSH-34/70.
4. **[MEDIUM] Personalize SOUL.md** — Add Heather-specific personality block for luxury brand founder context. Additions in soul-improvements doc. Addresses JOSH-37.
5. **[MEDIUM] Populate TOOLS.md** — Document actual tool integrations. Addresses JOSH-55.
6. **[MEDIUM] Remove dead fallback from openclaw.json** — Delete `openrouter/anthropic/claude-3.5-haiku` from the fallbacks array. Addresses JOSH-50.
7. **[MEDIUM] Delete BOOTSTRAP.md** — `workspace/BOOTSTRAP.md` should have been removed at go-live 70 days ago. Addresses JOSH-63.
8. **[INFO] Clarify hooks/bootstrap/TOOLS.md** — Add note that Google is connected via API key mode, not gog/OAuth. Addresses JOSH-74.

### Tier 2 — VPS-Required

1. **[HIGH] Upgrade OpenClaw 2026.3.22 → 2026.5.27** — Resolves JOSH-39/66, enables iMessage (JOSH-73), Active Memory Plugin (JOSH-72), group prompt isolation (JOSH-67), Discord improvements (JOSH-68). Do NOT upgrade to beta or alpha.
2. **[HIGH — post-upgrade] Apply Active Memory Plugin config** — Add `active-memory` plugin entry to openclaw.json. Config in soul-improvements doc.
3. **[HIGH — post-upgrade] Enable group prompt isolation** — Resolves JOSH-67.
4. **[HIGH — post-upgrade] Verify iMessage resumes** — Confirm `imessage_monitoring_paused` clears post-upgrade.
5. **[MEDIUM] Review ClawHub skills security advisory** — Resolves JOSH-42.

---

## Platform Research Notes

### OpenClaw Version Tracking (as of 2026-05-31)

| Version | Channel | Released | Status | Recommendation |
|---|---|---|---|---|
| 2026.5.29-alpha.1 | Alpha | 2026-05-31 | Unstable | Do not target |
| 2026.5.28-beta.2 | Beta | 2026-05-29 | Unstable | Do not target |
| 2026.5.28-beta.1 | Beta | 2026-05-29 | Unstable | Do not target |
| 2026.5.27 | Stable | ~2026-05-01 | **Upgrade target** | Apply when VPS accessible |
| 2026.3.22 | — | 2026-03-22 | Josh current | 70 days behind stable |

### AI Memory Architecture — 2026 State of the Art

The personal AI assistant market has converged on persistent memory as a baseline requirement. The new April 2026 token-efficient algorithm delivers the biggest gains on exactly the types of queries Heather needs to answer for Josh:

- **Temporal queries** (+29.6 pts): "Did Josh mention this before?" / "What was the status of X project last week?"
- **Multi-hop reasoning** (+23.1 pts): "Josh mentioned A and B separately — connect them for context on C"

OpenClaw's Active Memory Plugin implements this algorithm. It is available in 2026.4.12+. Josh's instance is on 2026.3.22 and cannot access it until upgraded.

The practical upside is that MEMORY.md can be created now (GitHub-only) and will be automatically indexed by the plugin the moment the upgrade completes.

### AlphaClaw Harness — Fleet Reliability Features

AlphaClaw (the harness running Josh's deployment) includes:
- **Self-healing watchdog:** Detects crashes, repairs state, auto-restarts within seconds
- **Git-backed rollback:** Any configuration change can be reverted by reverting the GitHub commit
- **In-place updates:** Both AlphaClaw and OpenClaw can be updated with in-app release notes and one-click apply (requires VPS dashboard access)
- **Observability:** Web dashboard at sslip.io address provides full agent state visibility

These features make the GitHub-only fixes safe to apply without fear of permanent breakage.

---

*Scan completed: 2026-05-31 evening. Next scan: 2026-06-01 morning.*
