# Fleet Research — Findings: 2026-05-30 Evening Scan

---

## Scan Metadata

| Field | Value |
|---|---|
| Scan date | 2026-05-30 (evening) |
| Scan type | Daily evening / cumulative review |
| Instance | Heather Schwartz (Discord personal assistant) |
| Customer | Josh Meyers |
| Repo | lylle-rgb/josh_repo |
| Previous scan | 2026-05-29 evening |
| Analyst | Fleet research bot |

---

## Platform Status

| Item | Current | Latest Stable | Gap | Notes |
|---|---|---|---|---|
| OpenClaw version | 2026.3.22 | 2026.5.27 | **69 days behind** | URGENT — upgrade target confirmed |
| Beta track | — | 2026.5.28-beta.1, beta.2 (May 29) | — | NOT stable; do NOT upgrade to beta |
| AlphaClaw UI | https://5.78.142.81.sslip.io | — | — | Active |
| Primary model | google/gemini-3-flash-preview | — | — | OK |
| OpenRouter fallback | openrouter/anthropic/claude-3.5-haiku | — | — | DEAD — remove |
| iMessage | paused (confirmed inbox-state.json) | — | — | Fix in 2026.5.27 |
| Email | Active — recent timestamps confirmed | — | — | OK |
| Memory layer | None | Active Memory Plugin (avail. post-upgrade) | — | CRITICAL gap |

---

## New Findings — 2026-05-30 Evening (JOSH-71 through JOSH-76)

### JOSH-71 — Beta Releases 2026.5.28-beta.1 and beta.2 Available (INFO / WATCH)

**Severity:** INFO  
**Requires VPS:** No  
**Discovery:** OpenClaw released beta.1 and beta.2 on 2026-05-29.

**What changed in beta:**
- Subagent cwd/workspace separation
- Session locks now release on timeout (previously hung)
- Hook context scoped prompt-local (not session-global)

**Disposition:** These betas are NOT stable and should NOT be deployed to Heather's instance. The upgrade target remains **2026.5.27 stable**. Continue monitoring; if beta graduates to stable before Josh upgrades, update the upgrade target accordingly.

---

### JOSH-72 — Active Memory Plugin Available Post-Upgrade (HIGH — Actionable on Upgrade)

**Severity:** HIGH  
**Requires VPS:** Yes (post-upgrade)  
**Discovery:** Active Memory Plugin introduced in OpenClaw 2026.4.12. Will become available to Heather upon upgrade from 2026.3.22 → 2026.5.27.

**What it does:**  
A dedicated memory sub-agent runs BEFORE each reply, pre-fetching relevant context from MEMORY.md and daily notes files. This directly addresses JOSH-30 (MEMORY.md never created) and JOSH-31/69 (HEARTBEAT.md empty).

**Recommended config (to be added to openclaw.json `plugins.entries` post-upgrade):**
```json
{
  "plugin": "active-memory",
  "enabled": true,
  "scope": "main",
  "channels": ["dm"],
  "queryMode": "recent",
  "timeoutMs": 15000,
  "maxSummaryChars": 220
}
```

**Blocking dependency:** Requires MEMORY.md to exist and be populated first. See JOSH-30 and soul-improvements doc.

---

### JOSH-73 — inbox-state.json Confirms iMessage Monitoring Paused (HIGH — Confirmed)

**Severity:** HIGH  
**Requires VPS:** Yes (post-upgrade)  
**Discovery:** Direct read of `workspace/memory/inbox-state.json` confirms `imessage_monitoring_paused=true`.

**Status:**
- Email: being checked, recent timestamps present, one Gmail thread drafted — **WORKING**
- iMessage: explicitly paused — **BROKEN**

**Fix:** iMessage monitoring fix ships in OpenClaw 2026.5.27. No workaround available on current 2026.3.22. Upgrade is the fix.

**Note:** Josh's primary channel is Discord; iMessage is a monitored secondary inbox. Impact is moderate day-to-day, but iMessage is part of the personal assistant brief.

---

### JOSH-74 — Google Integration: API Key Mode vs gog/OAuth Clarification (INFO)

**Severity:** INFO  
**Requires VPS:** No  
**Discovery:** `hooks/bootstrap/TOOLS.md` states "No Google accounts are currently configured." This is accurate for gog/OAuth pathway but is potentially misleading — Josh has Google services connected via API key mode.

**Disposition:** Not a bug. Heather's Google integration (Gmail, Calendar, Contacts) runs via native API key mode, not through the gog-cli OAuth flow. The TOOLS.md statement is technically accurate in context. However, if Josh ever reads that file he may think Google is broken. Recommend adding a clarifying comment to TOOLS.md.

**No immediate action required.** Low priority annotation.

---

### JOSH-75 — Zero Long-Term Memory: 69 Days of Activity, No Retention (CRITICAL — Escalating)

**Severity:** CRITICAL  
**Requires VPS:** No (MEMORY.md creation is GitHub-only)  
**Discovery:** `workspace/memory/` exists with 2 files (`inbox-state.json`, `onboarding-google.md`) but contains NO `MEMORY.md` and NO daily `YYYY-MM-DD.md` files. 69 days of email, calendar, and contact activity with zero durable memory retention.

**Impact:**
- Heather has no cross-session context about Josh's business relationships, preferences, or ongoing projects
- Every session starts cold — no continuity
- Active Memory Plugin (JOSH-72) cannot function without MEMORY.md

**Action:** Create MEMORY.md immediately. Template provided in soul-improvements doc. This is a GitHub-only action — no VPS access needed.

**Escalation status:** Day 69. This finding first appeared at instance launch. Every additional day compounds the gap.

---

### JOSH-76 — SEC "Zero-Tolerance" AI Market Monitoring — Validates Proactive Stance (CONTEXT)

**Severity:** INFO / CONTEXT  
**Requires VPS:** No  
**Discovery:** SEC publicly announced AI-driven zero-tolerance market monitoring as of this cycle.

**Relevance to Josh:** As a founder/CEO (Bliss Lifestyle) and partner (Oben HiFi), Josh operates in a space where regulatory awareness matters. This validates the fleet-wide push for proactive monitoring posture (HEARTBEAT.md population — JOSH-31/69). A personal assistant that surfaces relevant regulatory context before Josh asks is more valuable than one that only responds reactively.

**Action:** Reinforces priority of populating HEARTBEAT.md with proactive monitoring tasks. No direct code change required.

---

## Persistent Findings — Unresolved

| ID | Summary | Severity | VPS Required | Status | Days Open |
|---|---|---|---|---|---|
| JOSH-30 | MEMORY.md never created | CRITICAL | No | Open | 69+ |
| JOSH-31/69 | HEARTBEAT.md empty — no proactive monitoring | HIGH | No | Open | 69 |
| JOSH-34/70 | Emoji reaction contradiction: AGENTS.md vs USER.md | MEDIUM | No | Open | — |
| JOSH-37 | SOUL.md never personalized (stock template) | MEDIUM | No | Open | 69 |
| JOSH-39/66 | Upgrade to 2026.5.27 (69 days behind stable) | HIGH | Yes | Open | 69 |
| JOSH-42 | ClawHub skills security advisory | MEDIUM | No | Open | — |
| JOSH-50 | Dead OpenRouter fallback in openclaw.json | MEDIUM | No (JSON edit) | Open | — |
| JOSH-55 | TOOLS.md empty — no actual tool data | MEDIUM | No | Open | 69 |
| JOSH-63 | BOOTSTRAP.md never deleted | MEDIUM | No | Open | 69 |
| JOSH-67 | Security group prompt isolation (post-upgrade) | HIGH | Yes | Blocked on upgrade | — |
| JOSH-68 | Discord voice/wake improvements (post-upgrade) | INFO | Yes | Blocked on upgrade | — |
| JOSH-71 | Beta 2026.5.28-beta.1/2 — watch only | INFO | — | Monitoring | New |
| JOSH-72 | Active Memory Plugin — enable post-upgrade | HIGH | Yes | Blocked on upgrade | New |
| JOSH-73 | iMessage paused (confirmed) | HIGH | Yes | Blocked on upgrade | New |
| JOSH-74 | TOOLS.md gog/OAuth vs API key clarification | INFO | No | Low priority | New |
| JOSH-75 | Zero long-term memory — 69 days, no MEMORY.md | CRITICAL | No | Open — escalating | New |
| JOSH-76 | SEC AI monitoring context | INFO | — | Context only | New |

---

## Immediate Action List

### GitHub-Only Actions (No VPS Access Needed)

Priority order:

1. **[CRITICAL] Create MEMORY.md** — Template in soul-improvements doc. Heather has no cross-session memory. 69 days of data lost. GitHub file creation only.
2. **[HIGH] Populate HEARTBEAT.md** — Replace empty file with proactive monitoring task list. See soul-improvements doc for full content.
3. **[MEDIUM] Fix AGENTS.md emoji contradiction** — Add Josh-specific override block at top of AGENTS.md to disable emoji reactions, referencing USER.md rule. Exact text in soul-improvements doc.
4. **[MEDIUM] Personalize SOUL.md** — Add Josh/Heather-specific personality notes. Additions in soul-improvements doc.
5. **[MEDIUM] Remove dead OpenRouter fallback** — Edit openclaw.json to remove `openrouter/anthropic/claude-3.5-haiku` from models list.
6. **[MEDIUM] Delete BOOTSTRAP.md** — This file should have been removed at launch (69 days ago).
7. **[LOW] Annotate TOOLS.md** — Add note clarifying Google is connected via API key mode, not gog/OAuth.

### VPS-Required Actions (Require Server Access)

Priority order:

1. **[HIGH] Upgrade OpenClaw 2026.3.22 → 2026.5.27** — Unlocks iMessage fix, security prompt isolation, Discord voice improvements, and Active Memory Plugin. Do NOT upgrade to 2026.5.28-beta.
2. **[HIGH] Enable Active Memory Plugin post-upgrade** — Add config block to openclaw.json (see soul-improvements doc). Requires MEMORY.md to exist first.
3. **[HIGH] Verify iMessage resumes post-upgrade** — Confirm `imessage_monitoring_paused` clears after 2026.5.27 upgrade.
4. **[HIGH] Configure security group prompt isolation** — Available post-upgrade.

---

## Platform Research Notes

### OpenClaw 2026.5.28-beta.1 / beta.2 (Released 2026-05-29)

Beta changes in this cycle are infrastructure-level (subagent workspace isolation, lock timeouts, hook scope). These are low-risk features but unproven. Standard fleet policy: wait for stable graduation before deployment. Expected stable window: 1–2 weeks if no regressions found.

### Active Memory Plugin — Architecture Note

The plugin inserts a pre-reply pass: before Heather generates any response, a memory sub-agent reads MEMORY.md and any matching daily notes, summarizes relevant context, and injects it as a system-level prefix. The `queryMode: "recent"` setting limits the query to the most recent N entries rather than full-file scan, keeping latency low. The `timeoutMs: 15000` setting ensures the pre-pass fails gracefully if it hangs — Heather will still reply, just without the memory prefix. Recommended scope: `main` agent only, `dm` channels only to avoid polluting group channel responses with personal context.

### iMessage Architecture Note

Josh's iMessage monitoring runs through the macOS bridge component of OpenClaw. The `imessage_monitoring_paused=true` flag in inbox-state.json is set by the bridge when it detects an incompatible macOS entitlement state — a known regression introduced between 2026.3.x and fixed in 2026.5.27. The pause is intentional (prevents duplicate/lost message handling) but has been in effect for the full 69-day deployment lifetime of this instance.

### Google Integration Mode Note

Josh's instance uses native Google API key integration, not the gog-cli OAuth flow. The gog-cli pathway appears in hooks/bootstrap/TOOLS.md because that file is a stock template. The "No Google accounts configured" message refers specifically to gog/OAuth accounts. Josh's actual Google connectivity (Gmail, Calendar, Contacts) is handled by openclaw.json service credentials directly. Both pathways ultimately call the same Google APIs; the difference is auth method and tooling.

---

*Document generated: 2026-05-30 evening — fleet research*
