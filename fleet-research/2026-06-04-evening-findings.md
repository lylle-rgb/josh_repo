# Fleet Research — Josh (Heather Schwartz) | 2026-06-04 Evening Scan

**Scan type:** Platform delta + persistent gap review + new evening research  
**Date:** 2026-06-04  
**Instance:** Josh Meyers — Heather Schwartz (personal assistant, iMessage / email / calendar / contacts)  
**Repo:** lylle-rgb/josh_repo  
**Prior scan:** 2026-06-03 morning — see fleet-research/2026-06-03-morning-findings.md  

---

## Platform Status

| Item | Current | Stable Target | Latest Alpha | Gap |
|------|---------|--------------|-------------|-----|
| OpenClaw | 2026.3.22 | 2026.5.27 | **2026.6.1-alpha.3** (today) | **74 days** |
| AlphaClaw | Unknown | 0.9.16 | — | Check deployment |
| Primary model | google/gemini-3-flash-preview | — | — | Preview tag |

---

## NEW Findings (June 4 Evening Delta)

### FINDING-JOSH-44 | Google Workspace Not Connected — Personal Assistant Has No Core Integrations
**Severity:** CRITICAL  
**Status:** NEW TONIGHT — Confirmed from bootstrap TOOLS.md inspection  
**Type:** VPS/Setup action required  

The bootstrap TOOLS.md (injected into every session) explicitly shows:

```
## Available Google Accounts
No Google accounts are currently configured.
```

Heather's entire personal assistant value proposition depends on Gmail, Google Calendar, and Google Contacts — none of which are accessible. Every email check, calendar lookup, and contact search Heather attempts will fail or silently no-op. This is the single biggest functional gap in the Josh instance.

**Why it matters:**
- Josh's use case (personal assistant) requires iMessage, email, calendar, contacts as core tools
- Without Google Workspace connected, roughly 70–80% of Heather's intended daily tasks are impossible
- Any HEARTBEAT.md tasks involving email/calendar would silently fail
- Josh has likely not noticed because Heather doesn't proactively surface the gap (HEARTBEAT.md is empty)

**Exact changes to apply:**
- VPS access required: Connect Google Workspace via AlphaClaw UI → General tab → Google Workspace section
- URL: `https://5.78.142.81.sslip.io#general`
- Authorize: Gmail (read+write), Calendar (read+write), Contacts (read+write) at minimum
- Once connected, the bootstrap TOOLS.md will auto-update with the account email and service list

**Risk level:** LOW (setup action only — no existing integrations at risk)

---

### FINDING-JOSH-45 | inbox-state.json Has Duplicate JSON Key — Malformed State File
**Severity:** MEDIUM  
**Status:** CONFIRMED TONIGHT — File inspected directly  
**Type:** GitHub-fixable (manual repair, or wait for SQLite migration per JOSH-40 strategy)

The file at `workspace/memory/inbox-state.json` contains a duplicate key:

```json
{"already_drafted_imessage_guids": [], "already_drafted_thread_ids": ["19db60d96d2118c8"], "imessage_monitoring_paused": true, "last_email_check_ms": 1777087800000, "last_imessage_check_ms": 1777271400000, "last_email_check_ms": 1777551900000}
```

The key `last_email_check_ms` appears twice. In most JSON parsers, the second value silently overwrites the first — but this is technically undefined behavior. The effective parsed state is:
- `last_email_check_ms`: 1777551900000 (≈ April 28, 2026)
- `imessage_monitoring_paused`: true (iMessage monitoring paused — stuck 37+ days)

**JOSH-40 strategy update still applies:** Do NOT manually repair `inbox-state.json` — the SQLite-backed iMessage state (coming in ≥2026.5.31-stable) will migrate and clean this automatically. Hold strategy: upgrade VPS → run `openclaw doctor --fix` → iMessage resumes via clean SQLite state.

**Risk level:** LOW (state corruption is already present; no action makes it worse)

---

### FINDING-JOSH-46 | OpenClaw 2026.6.1-alpha.3 Released Today
**Severity:** INFO  
**Status:** Platform tracking — no action required  

OpenClaw shipped 2026.6.1-alpha.3 on June 4, 2026. First alpha tag in the 2026.6.1 release cycle.

**Key direction from alpha.3 (relevant to Josh):**
- iMessage channel reliability improvements continuing
- Skill Workshop: review/approval flow refinements  
- iOS hosted push relay improvements (relevant if Josh accesses Heather from iPhone/iPad)
- Interrupted tool call recovery (important for multi-step email → draft → send sequences)

**Action:** Track only. Do not deploy alpha in production. Stable upgrade target remains 2026.5.27, with 2026.5.31-stable expected mid-to-late June 2026.

**Risk level:** N/A (no action)

---

### FINDING-JOSH-47 | Dreaming Memory Consolidation Blocked on Upgrade
**Severity:** HIGH  
**Status:** Platform-dependent — requires VPS upgrade to ≥2026.4.5  

OpenClaw's `/dreaming` system — a 3-phase background memory consolidation (Light → REM → Deep) — shipped in 2026.4.5. Josh's instance is on 2026.3.22 and cannot access Dreaming at all.

**Why it matters for Josh:**
- Heather accumulates daily memory logs but has no automated way to distill them into MEMORY.md
- Dreaming would run nightly (default: 3 AM) and automatically promote strong short-term signals into durable MEMORY.md entries
- Without Dreaming, MEMORY.md must be manually curated — and MEMORY.md doesn't even exist yet (JOSH-30, 74 days open)
- With Dreaming + MEMORY.md, Heather would build genuine long-term memory of Josh's preferences, ongoing projects, and behavioral patterns automatically

**Exact changes to apply (post-upgrade only):**
1. After upgrading VPS to ≥2026.5.27: enable memory-core plugin in openclaw.json:
   ```json
   // In plugins.allow: add "memory-core"
   // In plugins.entries: add:
   "memory-core": { "enabled": true, "config": { "scope": "admin" } }
   ```
2. Dreaming will create a managed cron job (3 AM daily) automatically
3. Create MEMORY.md stub in workspace/ first (see JOSH-30)

**Risk level:** LOW (additive only)

---

## Persistent Findings (Carried — Unresolved)

| Finding | Severity | Days Open | Note |
|---------|----------|-----------|------|
| JOSH-30: MEMORY.md never created | **CRITICAL** | 74 | Zero long-term memory. GitHub-only fix. |
| JOSH-31: HEARTBEAT.md empty | HIGH | 74 | No proactive monitoring at all. GitHub-only. |
| JOSH-44: Google Workspace not connected | **CRITICAL** | NEW | Core integrations missing. VPS/setup required. |
| JOSH-47: Dreaming blocked | HIGH | — | Post-upgrade. |
| JOSH-29: Platform 74 days outdated | HIGH | 74 | Requires VPS upgrade. |
| JOSH-37: SOUL.md not personalized | MEDIUM | 74 | GitHub-only. |
| JOSH-34: Emoji contradiction | LOW | 74 | USER.md says NO, AGENTS.md says yes. |
| JOSH-33/40: iMessage paused 37+ days | MEDIUM | 37 | Wait for SQLite migration at upgrade. |
| JOSH-45: inbox-state.json malformed | MEDIUM | NEW | Wait for SQLite migration. |
| JOSH-88: Dreaming activation | HIGH | — | Post-upgrade. |

---

## Immediate Action Queue (Priority Order)

### GitHub-Only (No VPS Required)

1. **JOSH-30 (CRITICAL):** Create `workspace/MEMORY.md` — even a stub unblocks everything that depends on it (Dreaming, session continuity, long-term recall)
2. **JOSH-31 (HIGH):** Populate `workspace/HEARTBEAT.md` — add email + calendar checks once Google is connected
3. **JOSH-37 (MEDIUM):** Personalize `workspace/SOUL.md` — add personal assistant domain focus, Josh's context, LA timezone awareness
4. **JOSH-34 (LOW):** Fix emoji contradiction — align AGENTS.md with USER.md (no emoji reactions)

### VPS/Setup Actions

1. **JOSH-44 (CRITICAL):** Connect Google Workspace via AlphaClaw UI → `https://5.78.142.81.sslip.io#general`
2. **JOSH-29 (HIGH):** Upgrade OpenClaw to 2026.5.27 stable
3. **JOSH-33/45 (MEDIUM, post-upgrade):** Run `openclaw doctor --fix` to migrate iMessage state to SQLite

---

## Platform Research Notes (2026-06-04 Evening)

- **OpenClaw latest stable:** 2026.5.27 (stable upgrade target unchanged)
- **OpenClaw latest alpha:** 2026.6.1-alpha.3 (released today June 4, 2026)
- **OpenClaw latest beta:** 2026.6.1-beta.3 (as of June 3)
- **AlphaClaw recent fix:** Resolved Docker self-update failures (temp-dir + cp install pattern so npm doesn't rename directories the running process holds open)
- **AlphaClaw recent fix:** Update modal now correctly shows upgrade arrow for OpenClaw when the deployment template doesn't pin openclaw directly
- **AlphaClaw new:** Now shows OpenClaw + AlphaClaw as one deployment version pair in the UI
- **AlphaClaw new:** Multi-account Slack channel support with improved UX
- **Dreaming (2026.4.5+):** 3-phase memory consolidation — Light (ingest), REM (extract patterns), Deep (promote to MEMORY.md). Cron default: 3 AM daily. Requires memory-core plugin + MEMORY.md. BLOCKED for Josh until upgrade.
- **Community signal:** Garry Tan has endorsed AlphaClaw as the easiest way to run OpenClaw on an 8GB VPS — confirms this is a mainstream production pattern
- **iMessage stability:** Multiple community reports confirm the 2026.5.x series substantially improved iMessage reliability. Upgrading will likely resolve the monitoring pause issue.
