# Fleet Research: Findings — 2026-05-31 Morning Scan

## Scan Metadata

| Field | Value |
|---|---|
| Scan Date | 2026-05-31 (morning) |
| Scan Type | Morning Incremental |
| Instance | Heather Schwartz (Josh Meyers) |
| Repo | lylle-rgb/josh\_repo |
| Previous Scan | 2026-05-31 evening |
| Scanner | AlphaClaw Fleet Research |
| AlphaClaw UI | https://5.78.142.81.sslip.io |

---

## Platform Status

| Item | Current | Latest Stable | Gap | Notes |
|---|---|---|---|---|
| OpenClaw version | 2026.3.22 | **2026.5.28** | **71 days** | Upgrade target UPDATED |
| Latest Beta | — | 2026.5.30-beta.1 | — | Released today — do NOT target |
| Primary model | google/gemini-3-flash-preview | — | — | As configured |
| OpenRouter fallback | openrouter/anthropic/claude-3.5-haiku | — | Dead endpoint | Still needs removal |
| MEMORY.md | Missing | Required | **Day 71** | CRITICAL |
| HEARTBEAT.md | Empty | Required | **Day 71** | HIGH |
| iMessage | Paused | — | — | Fix confirmed in 2026.5.28 |

---

## New Findings — 2026-05-31 Morning (JOSH-81 through JOSH-85)

### JOSH-81 — OpenClaw 2026.5.28 STABLE Released — Upgrade Target Updated (HIGH)

**Priority:** HIGH — Upgrade target change
**Status:** New

OpenClaw `2026.5.28` was promoted to the stable channel on May 30, 2026. This supersedes the previous upgrade target of `2026.5.27`.

**What 2026.5.28 adds over 2026.5.27 (the prior target):**

- **iMessage delivery confirmed steadier** — 2026.5.28 changelog confirms improved delivery across iMessage, Telegram, WhatsApp, and Discord. iMessage stability was partially fixed in 2026.5.27 but 2026.5.28 strengthens it further.
- **Session lock timeout fix** — sessions can no longer deadlock indefinitely; lock releases on abort. This prevents Heather getting permanently stuck mid-task.
- **Gateway startup performance** — expensive plugin/channel/session scans no longer repeat on every restart. Heather's boot time will improve.
- **User-facing reply separation** — faster perceived response time as user-facing content is separated from slower follow-up processing.
- **Subagent workspace/cwd separation** — prevents one subagent task from contaminating another.
- **Hook context is now prompt-local** — HEARTBEAT.md hook context will no longer bleed across sessions.
- **Group prompt text injection removed** — prevents prompt injection attacks from Discord group chat participants.
- **No-auth Tailscale exposure rejected** — gateway security improvement.

**Updated upgrade target:** `2026.5.28` (was `2026.5.27`)

**Action:** When VPS access available, upgrade to 2026.5.28 (not 2026.5.27). The two-step path (3.22 → 5.27 → 5.28) is no longer necessary — go directly to 5.28.

---

### JOSH-82 — OpenClaw 2026.5.30-beta.1 Released Today — Monitor Only (INFO)

**Priority:** INFO
**Status:** New — Monitor

`2026.5.30-beta.1` released today (2026-05-31). This is a fresh beta — do not target for Josh's upgrade. Continuing to track the beta cycle as a signal for what arrives in the next stable release (~mid-June).

Early indicators from the beta: subagent coordination improvements and continued channel delivery hardening.

**Action:** Monitor. No upgrade action.

---

### JOSH-83 — Discord In-Progress Commentary Drafts — Post-Upgrade Visibility Improvement (INFO)

**Priority:** INFO — Post-upgrade benefit
**Status:** New

Starting in 2026.5.28, Discord shows **in-progress commentary drafts** during active runs. This means when Heather is working on a longer task (e.g., reading Josh's email backlog, drafting a calendar invitation), Josh will see live status updates in the Discord channel rather than silence followed by a wall of text.

For a personal assistant context — where Josh asks questions and expects timely responses — this improves the perceived responsiveness of the assistant even during complex multi-step tasks.

**Relevance:** HIGH for Josh's use case. iMessage and email tasks often take 15–30 seconds. Discord will now show "working..." commentary rather than appearing frozen.

**Action:** No config change needed. Benefit arrives automatically post-upgrade to 2026.5.28.

---

### JOSH-84 — Skill Workshop Proposals — New Guided Skill Creation Path (INFO → Strategic)

**Priority:** INFO / Strategic
**Status:** New

OpenClaw 2026.5.x introduced **Skill Workshop proposals** — a guided workflow for creating new custom skills via a proposal lifecycle (draft PROPOSAL.md → review → approve/reject → rollback metadata). Skills can now be proposed with pre-approval security scanning and hash verification.

For Heather's context, this is relevant for adding Josh-specific capabilities:
- A custom iMessage-to-calendar parsing skill
- A custom email-triage skill with Josh's priority rules
- A Heather-specific memory summary skill

The Skill Workshop makes these safer to add and easier to roll back if a skill behaves unexpectedly.

**Action:** No immediate action. Worth exploring after the upgrade and post-upgrade stabilization window. The Skill Workshop is available in 2026.5.x.

---

### JOSH-85 — Tokenjuice Plugin Now Official — Low Priority for Heather (INFO)

**Priority:** INFO — Low priority
**Status:** New

The `@openclaw/tokenjuice` plugin was officially externalized in 2026.5.x. It compacts noisy exec and bash tool output after execution, reducing token bloat in long conversations.

**Assessment for Josh:** LOW priority. Heather is a personal assistant with conversational workloads (email, calendar, iMessage). She does not run exec-heavy or bash-heavy tool chains where tokenjuice provides significant benefit. This is more useful for agents running heavy code execution or file processing pipelines.

**Action:** No action needed.

---

## Persistent Findings — All Unresolved Items (Day 71)

| ID | Summary | Priority | Days Open | Action Type | Status |
|---|---|---|---|---|---|
| JOSH-30/75/79 | MEMORY.md never created — 71 days activity unremembered | CRITICAL | 71 | GitHub-only | Unresolved |
| JOSH-31/69 | HEARTBEAT.md empty — no proactive monitoring | HIGH | 71 | GitHub-only | Unresolved |
| JOSH-34/70 | Emoji contradiction: AGENTS.md vs USER.md STRICT rule | MEDIUM | 71 | GitHub-only | Unresolved |
| JOSH-37 | SOUL.md never personalized for Heather/Josh context | MEDIUM | 71 | GitHub-only | Unresolved |
| JOSH-39/66/81 | Upgrade to **2026.5.28** (iMessage, Active Memory, session fixes) | HIGH | 71 | VPS-required | Unresolved |
| JOSH-42 | ClawHub skills security advisory | MEDIUM | — | VPS-required | Unresolved |
| JOSH-50 | Dead OpenRouter fallback in openclaw.json | MEDIUM | — | GitHub-only | Unresolved |
| JOSH-55 | TOOLS.md completely empty — no tool inventory | MEDIUM | — | GitHub-only | Unresolved |
| JOSH-63 | BOOTSTRAP.md never deleted | MEDIUM | 71 | GitHub-only | Unresolved |
| JOSH-72 | Active Memory Plugin available post-upgrade | HIGH | 2 | GitHub-only (prep) | Blocked on upgrade |
| JOSH-73 | iMessage paused — confirmed inbox-state.json | MEDIUM | 2 | VPS (via upgrade) | Confirmed |
| JOSH-74 | Google API key mode vs gog/OAuth clarification needed | INFO | 2 | GitHub-only | Unresolved |
| JOSH-77 | Alpha 2026.5.29-alpha.1 detected | INFO | 1 | Monitor | Tracking |
| JOSH-78 | 2026.5.28 security/runtime improvements confirmed (now STABLE) | INFO→HIGH | 0 | Upgrade target updated | Updated |
| JOSH-79 | AI memory temporal algorithm (+29.6 pts) — memory urgency | HIGH | 1 | GitHub-only (MEMORY.md) | Unresolved |
| JOSH-80 | AlphaClaw self-healing watchdog — GitHub fixes are low-risk | INFO | 1 | Strategic note | Noted |
| JOSH-81 | 2026.5.28 STABLE released — upgrade target updated | HIGH | 0 | Strategic note | New |
| JOSH-82 | 2026.5.30-beta.1 released | INFO | 0 | Monitor | New |
| JOSH-83 | Discord in-progress commentary — post-upgrade benefit | INFO | 0 | Auto post-upgrade | New |
| JOSH-84 | Skill Workshop proposals — custom skill path | INFO | 0 | Post-upgrade explore | New |
| JOSH-85 | Tokenjuice plugin — low priority for Heather | INFO | 0 | No action | New |

---

## Immediate Action List (Updated)

### Tier 1 — GitHub-Only (No VPS Required) — All Low-Risk

1. **[CRITICAL] Create MEMORY.md** — `workspace/MEMORY.md`. Template in soul-improvements docs. Addresses JOSH-30/75/79.
2. **[HIGH] Populate HEARTBEAT.md** — Replace empty file with proactive monitoring checklist. Addresses JOSH-31/69.
3. **[MEDIUM] Fix AGENTS.md emoji contradiction** — Add Josh-specific override block at top of `workspace/AGENTS.md` per USER.md STRICT rule. Addresses JOSH-34/70.
4. **[MEDIUM] Personalize SOUL.md** — Add Heather-specific personality block for luxury brand founder context. Addresses JOSH-37.
5. **[MEDIUM] Populate TOOLS.md** — Document actual tool integrations. Addresses JOSH-55.
6. **[MEDIUM] Remove dead fallback from openclaw.json** — Delete `openrouter/anthropic/claude-3.5-haiku` from the fallbacks array. Addresses JOSH-50.
7. **[MEDIUM] Delete BOOTSTRAP.md** — Addresses JOSH-63.
8. **[INFO] Clarify hooks/bootstrap/TOOLS.md** — Note Google API key mode. Addresses JOSH-74.

### Tier 2 — VPS-Required

1. **[HIGH] Upgrade OpenClaw 2026.3.22 → 2026.5.28** — Target is now 2026.5.28 (not 5.27). Resolves JOSH-39/66/81, enables iMessage (JOSH-73), Active Memory Plugin (JOSH-72), group prompt isolation, session lock fix, Discord commentary. **Skip 2026.5.27 — go direct to 2026.5.28.**
2. **[HIGH — post-upgrade] Apply Active Memory Plugin config** — Add `active-memory` plugin entry to openclaw.json.
3. **[HIGH — post-upgrade] Verify iMessage resumes** — Confirm `imessage_monitoring_paused` clears.
4. **[MEDIUM] Review ClawHub skills security advisory** — Addresses JOSH-42.

---

## Platform Research Notes

### OpenClaw Version Tracking (as of 2026-05-31 morning)

| Version | Channel | Released | Status | Recommendation |
|---|---|---|---|---|
| 2026.5.30-beta.1 | Beta | 2026-05-31 | Unstable | Do not target |
| **2026.5.28** | **Stable** | **2026-05-30** | **Current stable** | **Upgrade target** |
| 2026.5.29-alpha.1 | Alpha | 2026-05-31 | Unstable | Do not target |
| 2026.5.27 | Stable (prev) | ~2026-05-01 | Superseded | Skip — go to 2026.5.28 |
| 2026.3.22 | — | 2026-03-22 | Josh current | 71 days behind stable |

### Key Upgrade Improvement (2026.5.28 vs 2026.5.27)

The promotion of 2026.5.28 to stable means Josh's single upgrade now captures everything in the prior target PLUS:
- Session lock timeout release (prevents deadlock)
- Prompt-local hook context (HEARTBEAT.md works correctly)
- Group prompt injection removal (security hardening)
- Gateway startup caching (faster boot)
- User-reply separation (faster perceived response)
- Subagent workspace isolation (prevents cross-contamination)

The extra improvement over 2026.5.27 is substantial and aligns directly with known issues (session behavior, HEARTBEAT.md context bleed).

---

*Scan completed: 2026-05-31 morning. Next scan: 2026-06-01 morning.*
