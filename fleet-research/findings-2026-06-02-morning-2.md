# Fleet Research: Findings — 2026-06-02 Morning Scan (2)

## Scan Metadata

| Field | Value |
|---|---|
| Scan Date | 2026-06-02 (morning — second scan) |
| Scan Type | Incremental |
| Instance | Heather Schwartz (Josh Meyers) |
| Repo | lylle-rgb/josh\_repo |
| Previous Scan | 2026-06-02 morning |
| Scanner | AlphaClaw Fleet Research |
| AlphaClaw UI | https://5.78.142.81.sslip.io |

---

## Platform Status

*(Unchanged from 2026-06-02 morning scan — see previous file)*

| Item | Current | Latest Stable | Gap |
|---|---|---|---|
| OpenClaw version | 2026.3.22 | 2026.5.27 | **72 days** |
| MEMORY.md | Missing | Required | **Day 72 — CRITICAL** |
| iMessage | Paused | — | Awaiting upgrade |

---

## New Findings — 2026-06-02 Morning-2 (JOSH-88 through JOSH-89)

### JOSH-88 — Active Memory Dreaming: Auto-Curation of MEMORY.md Post-Upgrade (HIGH)

**Priority:** HIGH (post-upgrade)  
**Status:** New  
**Action type:** Post-upgrade config (GitHub prep now)

Deeper research on the memory-core plugin reveals a feature called **Dreaming** — a background memory consolidation pass that shipped in OpenClaw **2026.4.5**. Josh is on 2026.3.22 and cannot use it until upgrade to 2026.5.27. This finding maps the full post-upgrade memory setup.

**What it is:**

Dreaming auto-manages MEMORY.md using a three-phase consolidation cycle:

| Phase | Function |
|---|---|
| Light Sleep | Ingest and stage short-term memory candidates from daily files |
| REM Sleep | Reflect and extract patterns across staged entries |
| Deep Sleep | Promote only qualified entries to MEMORY.md; discard noise |

Candidates are scored on six weighted signals before promotion:

| Signal | Weight |
|---|---|
| Relevance | 0.30 |
| Frequency (how often recalled) | 0.24 |
| Query diversity (multiple contexts) | 0.15 |
| Recency | 0.15 |
| Consolidation (de-duplication) | 0.10 |
| Conceptual richness | 0.06 |

After each cycle, OpenClaw writes a **DREAMS.md** transparency log showing exactly what was promoted and what was discarded.

**Why it matters for Heather:**

MEMORY.md for a personal assistant (iMessage, email, calendar, contacts) will accumulate noise over time — resolved email threads, old calendar context, stale preferences. Without curation, MEMORY.md degrades and costs more tokens per session to load. Dreaming auto-promotes what actually matters:

- **Recurring preferences** ("Josh likes calendar events confirmed two days ahead") — high frequency, high relevance → promoted
- **Resolved one-off tasks** ("dentist appointment next Tuesday") — low recency after the date passes → discarded
- **Persistent patterns** ("Josh's iMessage tone: casual, no emoji") — high frequency, high conceptual richness → promoted

This means Josh doesn't need to manually maintain Heather's long-term memory — the agent self-curates.

**Timing and prerequisites:**

1. MEMORY.md must exist — JOSH-30/79 (CRITICAL, Day 72, GitHub-only **today**)
2. Upgrade to 2026.5.27 — JOSH-39 (HIGH, VPS required)
3. Configure active-memory plugin — JOSH-72 (post-upgrade)
4. Enable Dreaming — JOSH-88 (opt-in config, post-upgrade)

**Exact config for post-upgrade reference:**
```json
"plugins": {
  "entries": {
    "memory-core": {
      "enabled": true,
      "dreaming": {
        "enabled": true
      }
    }
  }
}
```

**Dreaming is opt-in** — zero behavior change until explicitly enabled. Nothing in MEMORY.md will change automatically post-upgrade unless the config block above is applied.

**Risk:** LOW — opt-in only. No automatic behavior until Josh/AlphaClaw explicitly activates it post-upgrade.

**Action:** No immediate action. Document for post-upgrade setup checklist. MEMORY.md creation (JOSH-30/79) remains the most urgent prerequisite.

---

### JOSH-89 — 2026.5.28-beta.4: iMessage Reactions/Polling Improvements (INFO/TRACK)

**Priority:** INFO (track for stable promotion)  
**Status:** New

Beta 2026.5.28-beta.4 includes two iMessage-specific fixes relevant to Josh's use case:

| Fix | Description |
|---|---|
| Polling continuity | Continue iMessage polling after denied reactions — previously a denial could stall the iMessage channel |
| Duplicate suppression | Suppress duplicate native exec approvals — prevents Heather from presenting redundant approval requests |

**Why it matters:**

Josh's primary use case is iMessage-heavy — Heather manages messages, email, and calendar over iMessage. These fixes directly improve iMessage channel stability beyond the base iMessage recovery already planned for 2026.5.27 (JOSH-73).

The polling continuity fix in particular matters: if Josh denies a suggested reaction from Heather (e.g., "shouldn't send that"), the channel currently risks stalling. Post-2026.5.28-stable, denial is handled gracefully and polling resumes.

**Timing:**
- 2026.5.28 is currently **beta.4** — do NOT upgrade to beta
- Expected stable promotion: mid-to-late June 2026
- Path: upgrade to 2026.5.27 (JOSH-39) first, then watch for 2026.5.28 stable

**Risk:** N/A — informational only.

**Action:** Track 2026.5.28 for stable promotion. After upgrading to 2026.5.27 and verifying iMessage resumes (JOSH-73), flag 2026.5.28 as the next upgrade target.

---

## Persistent Findings — New Items This Scan

*(Full table in 2026-06-02 morning findings — JOSH-30 through JOSH-87)*

| ID | Summary | Priority | Days Open | Action Type | Status |
|---|---|---|---|---|---|
| JOSH-88 | Active Memory Dreaming — 3-phase auto-curation, available post-upgrade | HIGH | 0 | Post-upgrade config | New |
| JOSH-89 | 2026.5.28-beta.4 iMessage reactions/polling continuity | INFO | 0 | Monitor for stable | New |

---

## Research Notes

### The Full Memory Stack for Heather — Now Mapped

With JOSH-88, the complete post-upgrade memory architecture for Heather is now documented:

| Step | Action | Type | Timing |
|---|---|---|---|
| 1 | Create MEMORY.md stub | GitHub-only | **Today** (Day 72) |
| 2 | Upgrade to 2026.5.27 | VPS | When VPS available |
| 3 | Configure active-memory plugin | Post-upgrade config | After step 2 |
| 4 | Enable Dreaming | Post-upgrade config | After step 3 |

Step 1 is the only blocker that can be resolved today. Every day MEMORY.md doesn't exist is a day of context that Dreaming will never be able to consolidate — the daily memory files from the last 72 days contain potential learnings about Josh's preferences, communication patterns, and recurring tasks that Dreaming could promote. Once Dreaming is running, those historical files can be fed through a manual consolidation pass.

### DREAMS.md — What to Expect Post-Activation

After each Dreaming cycle, OpenClaw writes `DREAMS.md` to the config directory with a structured log of promotions and discards. For an operator like AlphaClaw, this file provides a lightweight audit trail of what Heather is "learning" about Josh — useful for spot-checking that no sensitive personal data is being promoted to shared contexts.

---

*Scan completed: 2026-06-02 morning (second pass). Next scan: 2026-06-02 evening.*
