# Cross-Customer Fleet Analysis

> Original scan: 2026-04-21 | Updated: 2026-05-05 (morning) | Agent: AlphaClaw Fleet Research
> Fleet: Josh (Heather Schwartz) • Noah (Market Catalyst Agent / Career Research)

---

## UPDATE — MORNING SCAN 2026-05-05

### Fleet Status — Day 15, Zero Implementation Across Both Instances

| Customer | Version | Latest Stable | Days Stale | Scan Days | Implemented |
|----------|---------|--------------|-----------|----------|-------------|
| Josh (Heather) | 2026.3.22 | **2026.5.3** | **44 days** | Day 15 | 0 |
| Noah (Claw) | 2026.4.15 | **2026.5.3** | **20 days** | Day 15 | 0 |

**New stable release:** OpenClaw `v2026.5.3` released today (2026-05-05). Both update targets revised upward from the 2026-05-04 reference of 2026.5.2.

---

### Security Escalation: Updating Is Now a Security Requirement

138+ OpenClaw CVEs disclosed in 2026. Key facts from this morning's research:

- **CVE-2026-32922** (CVSS 9.9, auth bypass race condition): Fixed in 2026.3.11. Both customers are past this specific CVE (Josh: 2026.3.22, Noah: 2026.4.15).
- **CVE-2026-25253** (Remote Code Execution, CVSS 8.8): Patched in 2026.4.x builds. Josh on 2026.3.22 is potentially exposed.
- **April 2026 patch batch**: 13 CVEs patched, 2 at Critical severity. Both customers may be missing some of these.
- **Gateway posture**: Both instances use `bind: loopback` + `trustedProxies: ["127.0.0.1"]` — not internet-exposed. Significant risk mitigation.
- **Fleet threshold shift**: Previous urgency was features. New urgency is security.

**Fleet security exposure comparison:**

| Customer | Version | RCE CVE-25253 | April Batch CVEs | Loopback Bind |
|----------|---------|--------------|-----------------|---------------|
| Josh | 2026.3.22 | Potentially exposed | Missing | ✓ Protected |
| Noah | 2026.4.15 | Likely patched | Possibly missing | ✓ Protected |

---

### New in 2026.5.3 — Fleet-Wide Impact

**1. Bundled File-Transfer Plugin**
- Tools: `file_fetch`, `dir_list`, `dir_fetch`, `file_write` for binary file ops on paired nodes.
- Config: `plugins.entries.file-transfer.config.nodes` — per-node path policy, operator approval required, symlink traversal off by default, 16 MB ceiling per round-trip.
- **Josh relevance:** Medium — personal assistant could access files on Josh's machine for email attachments, documents.
- **Noah relevance:** Low — gog-cli handles Google Drive. Not a priority.

**2. Cron Persistence Fix**
- `jobs-state.json` correctly persists repaired startup state on gateway restart.
- Both customers are blocked on cron.json, but once added, this fix prevents phantom health-check loops after restarts.

**3. New `/side` Command**
- Quiet aside to agent mid-conversation without breaking the thread.
- Useful for both customers for in-context background instructions.

**4. Plugin Install/Update Hardening**
- Externalized plugins now behave like first-class package installs.
- **Noah direct impact**: memory-core half-configured issue may be auto-detectable and auto-repairable post-update.

**5. Reset Interrupt Fix**
- `/new` and `/reset` treated as interrupt runs — steer/followup modes can't block fresh sessions.
- Relevant for Noah: long research tasks will no longer block a fresh session reset.

---

### threadBindings.spawnSessions — Config Migration Required Post-Update

OpenClaw 2026.5.2 replaced split subagent/ACP thread-spawn toggles with unified `channels.discord.threadBindings.spawnSessions` (default: `true`).

**Post-update action for both customers:** `openclaw doctor --fix` — auto-migrates legacy keys.

**Fleet impact:**

| Customer | threadBindings Relevance | Notes |
|----------|------------------------|-------|
| Josh | Low-Medium | Future multi-agent use cases; no immediate change needed |
| Noah | **High** | Prerequisite for parallel 5-company research architecture (E22/E15) — each company gets its own Discord thread + sub-agent |

---

### Gemini 3 Flash — Josh Model Validated

Community benchmarks confirm Gemini 3 Flash Preview (Josh's primary) scores **78% SWE-bench Verified** — above Gemini 3 Pro (76.2%). 1M context window, 66K output limit. Josh's model choice is sound; no change recommended.

**New option:** Gemini 3.1 Flash Lite on OpenRouter — faster, lower cost. Could be added to Josh's fallback chain for budget heartbeat sub-tasks. Not a replacement; a supplement.

Noah's Anthropic Claude Sonnet 4.6 is not affected. Note: `claude-opus-4-6` listed in Noah's models catalog is eligible for upgrade to `claude-opus-4-7`.

---

### Updated Pre-Update Checklist (2026-05-05 Morning)

Before running `alphaclawctl update` on either instance:

1. **Josh (critical):** Add `tools.exec.security: "full"` to `openclaw.json` — not yet configured, creates risk at update time
2. **Both:** Run `openclaw config validate` — gateway fails-closed on invalid config since 2026.5.2
3. **Both:** After update, run `openclaw doctor --fix` to migrate threadBindings legacy config

Active Memory config: no changes from 2026-05-04 morning scan. `setupGraceTimeoutMs: 30000` still required post-upgrade.

---

### Fleet-Wide Action Checklist (Morning 2026-05-05)

**Security first:**
- [ ] Josh: Add `tools.exec.security: "full"` to `openclaw.json`
- [ ] Both: `openclaw config validate`
- [ ] Both: `alphaclawctl update` → 2026.5.3
- [ ] Both: `openclaw doctor --fix` (threadBindings migration)

**Immediate — zero config needed:**
- [ ] Noah: Prompt Claw for updated AE target companies report (13 days overdue)

**Memory (use configs from 2026-05-04, with `setupGraceTimeoutMs: 30000`):**
- [ ] Both: Install `memory-lancedb` + enable `active-memory` plugin (two-step)

**High priority per customer:**
- [ ] Josh: Create MEMORY.md + populate HEARTBEAT.md + enable Discord streaming
- [ ] Josh: Investigate iMessage monitoring pause
- [ ] Noah: Fill USER.md + IDENTITY.md + create MEMORY.md
- [ ] Noah: Fix memory-core plugin entry (add to `plugins.entries`)
- [ ] Noah: Increase contextPruning TTL 5m → 30m
- [ ] Noah: Enable Gmail Watch (`gog gmail watch --enable --account Ngkatz.ai@gmail.com`)

**Upcoming:**
- [ ] Josh: Add Gemini 3.1 Flash Lite to fallback chain (budget heartbeat tasks)
- [ ] Josh: Configure voice persona post-update (ElevenLabs v3)
- [ ] Noah: threadBindings.spawnSessions → parallel 5-company research threads (post-update)
- [ ] Noah: Google Meet earnings call attendance (meet:write perms already granted)
- [ ] Noah: Upgrade `claude-opus-4-6` → `claude-opus-4-7` in models catalog

---

## UPDATE — MORNING SCAN 2026-05-04

### Fleet Status — Day 14, Zero Implementation Across Both Instances

| Customer | Version | Latest Stable | Days Stale | Scan Days | Findings | Implemented |
|----------|---------|--------------|-----------|----------|---------|-------------|
| Josh (Heather) | 2026.3.22 | **2026.5.2** | **44 days** | Day 14 | 25 | 0 |
| Noah (Claw) | 2026.4.15 | **2026.5.2** | ~19 days | Day 14 | 27 | 0 |

**New stable release:** OpenClaw `v2026.5.2` is now the current stable, advancing beyond `2026.4.29`. Update targets for both customers are revised upward. The update command is unchanged: `alphaclawctl update`.

---

### New Fleet-Wide: Active Memory timeoutMs Cold-Start Fix (2026.5.2)

OpenClaw `v2026.5.2` removes the implicit 30,000ms cold-start grace period for the Active Memory plugin. Both customers' pending `active-memory` configs require a one-line addition before deploying post-upgrade:

```json
"setupGraceTimeoutMs": 30000
```

This explicitly opts into the cold-start grace that was previously implicit. Without it, cold-start memory queries run against the configured `timeoutMs` budget rather than the previous silent extended budget.

**Impact per customer:**

| Customer | `timeoutMs` config | Cold-start budget before 2026.5.2 | After 2026.5.2 without fix | After fix |
|----------|-------------------|----------------------------------|---------------------------|----------|
| Josh (Heather) | 15,000ms | ~45,000ms (implicit) | 15,000ms | 45,000ms (explicit) |
| Noah (Claw) | 20,000ms | ~50,000ms (implicit) | 20,000ms | 50,000ms (explicit) |

Noah's `queryMode: "full"` means cold-start memory scans are more expensive than Josh's `"recent"` mode — the `setupGraceTimeoutMs` is especially important for Claw's config.

Both pending configs have been updated:
- Josh: E18/E25 (josh_repo findings.md)
- Noah: E17/E27 (noah--repo findings.md)

---

### New Fleet-Wide: Gateway Fails-Closed on Invalid Config (2026.5.2)

In prior OpenClaw versions, gateway startup would attempt to auto-restore invalid config. In 2026.5.2, invalid config fails closed — gateway won't start until config is valid. `openclaw doctor --fix` is the repair path.

**Pre-update checklist for both customers:**
1. Stage any pending config changes (Josh: add `tools.exec.security: "full"` — E20)
2. Run `openclaw config validate` before triggering update
3. After update, run `openclaw doctor` before starting the gateway

Josh carries more risk here because his config hasn't been touched since 2026.3.22 and lacks the exec security block. Noah's config was last touched at 2026.4.15 and has explicit security settings.

---

### Noah Escalation: Weekly Report Now 6 Days Overdue

The April 29 AE target companies report is **6 days overdue** (was 5 on May 3). Intelligence gap is now 12 days. Zero config changes needed — just a Discord prompt to Claw:

> *"Run an updated AE target companies report. Use the April 22 report as the baseline. Update each company's status, check for new AE openings, flag funding or personnel changes since April 22."*

---

### Fleet-Wide Action Checklist (Morning 2026-05-04)

**Immediate — no config needed:**
- [ ] Noah: Prompt Claw for updated AE target companies report (6 days overdue)

**Critical pre-update config:**
- [ ] Josh: Add `tools.exec.security: "full"` to `openclaw.json` (E20)
- [ ] Josh: Run `openclaw config validate` before updating

**Update both instances to 2026.5.2:**
- [ ] Both: `alphaclawctl update`
- [ ] Both: `openclaw doctor` after update

**Memory install — use 2026.5.2-updated configs with `setupGraceTimeoutMs: 30000`:**
- [ ] Josh: Install `memory-lancedb` + enable `active-memory` (E18/E25 config)
- [ ] Noah: Install `memory-lancedb` + enable `active-memory` (E17/E27 config)

**High priority per customer:**
- [ ] Josh: Fix SOUL.md emoji contradiction (E2) — 5 min
- [ ] Josh: Create MEMORY.md with seed data — 15 min
- [ ] Josh: Populate HEARTBEAT.md + add cron.json morning briefing — 30 min
- [ ] Josh: Investigate iMessage monitoring pause (E10) — 10 min
- [ ] Noah: Fill USER.md (Noah Katz, PermitFlow AE, job search criteria) — 15 min
- [ ] Noah: Fill IDENTITY.md (anchor "CLAW" persona) — 10 min
- [ ] Noah: Create MEMORY.md from April 22 report context — 15 min
- [ ] Noah: Add cron.json for weekly report + follow-up commitments — 30 min
- [ ] Noah: Replace misformatted HEARTBEAT.md — 10 min
- [ ] Noah: Increase contextPruning TTL from 5m to 15m (E7) — 5 min

**Upcoming:**
- [ ] Both: Enable Discord streaming (`"streaming": "partial"`) — 5 min each
- [ ] Both: Evaluate memory-lancedb-pro after 2–3 weeks of standard memory
- [ ] Josh: Configure voice persona post-update (ElevenLabs v3, E22)
- [ ] Noah: Verify exec-approvals.json defaults (E20)

---

## UPDATE — MORNING SCAN 2026-05-03

### Fleet Status — Day 13, Zero Implementation Across Both Instances

| Customer | Version | Latest Stable | Days Stale | Scan Days | Findings | Implemented |
|----------|---------|--------------|-----------|----------|---------|-------------|
| Josh (Heather) | 2026.3.22 | 2026.4.29 | **43 days** | Day 13 | 23 | 0 |
| Noah (Claw) | 2026.4.15 | 2026.4.29 | 17 days | Day 13 | 24 | 0 |

**Pattern alert:** 13 consecutive daily scans across two customers. Zero config changes on either instance. The bottleneck is execution, not documentation — every finding has a specific, low-risk action. Suggested lowest-friction unblock:

- **Noah:** No config needed. Open Discord, prompt Claw for the overdue AE report. 5 minutes.
- **Josh:** One config change + one command: add `tools.exec.security: "full"` to openclaw.json, then run `alphaclawctl update`.

---

### New Fleet-Wide: Task Brain Makes Cron Safer for Both Customers

OpenClaw's Task Brain (2026.3.31+, stable in current builds) unifies cron jobs, subagents, ACP, and background processes onto a SQLite-backed ledger. The specific behavioral change relevant to both customers: **isolated cron runs now automatically clean up orphaned browser tabs and processes on completion**, whether the run succeeds or fails.

**Impact per customer:**

| Customer | Pending Cron Item | Prior Risk | Updated Risk |
|----------|------------------|-----------|-------------|
| Josh (Heather) | Morning briefing + Friday wrap (M4) | Medium — orphaned browser processes | **Low** ↓ |
| Noah (Claw) | Weekly AE report delivery (N1) | Medium — timing/timezone setup | Medium (unchanged — timezone concern, not process safety) |

Both customers can add `workspace/cron.json` with higher confidence than previously documented. For Josh specifically, the risk downgrade makes M4 equivalent in risk to enabling Discord streaming (Very Low).

---

### Josh Escalation: Voice Capability Unlocks Post-Update

OpenClaw 2026.4.25 shipped a full TTS overhaul including per-agent voice personas and ElevenLabs v3 bundled natively. Josh's AGENTS.md already instructs Heather to use `sag` (ElevenLabs TTS) for voice storytelling — this capability is already wired in Heather's workspace instructions. Post-update to 2026.5.2, Heather gains:

- A configured, consistent voice persona across all TTS output
- ElevenLabs v3 quality improvements without additional skill installation
- Chat-scoped auto-TTS controls (voice briefings can be on/off per conversation)

This is a meaningful capability unlock that activates on update with no additional installation. No action before the update; once updated, check 2026.4.25 docs for the `agents.defaults.voice.persona` config.

Noah's use case (research/career assistance) is less voice-oriented — this is primarily a Josh/Heather capability.

---

### Noah Escalation: Report 5 Days Overdue + memoryFlush Losing Data Daily

Two time-sensitive findings on Noah's instance escalated since the May 2 scan:

1. **Weekly AE report (E21):** Was 5 days overdue on May 3 (now 6 days on May 4). Zero config changes needed — just a Discord prompt to Claw.

2. **memoryFlush silent data loss (E19/E24):** The `compaction.memoryFlush` has been active for 13+ days, flushing context to ephemeral `/tmp` on every long research session. Every container restart discards the flushed data. Installing `memory-lancedb` (N3/E10/E17) resolves this automatically.

Josh does not have memoryFlush configured, so this is Noah-specific.

---

### Noah Use Case Note: Parallel Research Now Near-Term

With Task Brain stable and subagent routing metadata confirmed in 2026.4.29+, the parallel company research architecture (E15/E22) moves from "future prototype" to "near-term after basics." The prerequisites are both already pending:
1. Update OpenClaw to 2026.5.2 (N4/E25)
2. Install memory-lancedb (N3)

Once those are done, prototyping parallel 5-company research is the natural next phase — not a speculative future goal.

---

### Fleet-Wide Action Checklist (Morning 2026-05-03)

**Immediate — no config needed:**
- [ ] Noah: Prompt Claw for updated AE target companies report (5 days overdue)

**Do first — 10 minutes each:**
- [ ] Josh: Add `tools.exec.security: "full"` to `openclaw.json` before updating
- [ ] Both: Run `alphaclawctl update` → OpenClaw 2026.4.29

**Memory install — 20 minutes (use divergent configs from 2026-05-02 cross-analysis):**
- [ ] Both: Install `memory-lancedb` + enable `active-memory` plugin (two-step)
  - Josh: `queryMode: "recent"`, `maxSummaryChars: 220`, `timeoutMs: 15000`, `modelFallback: "google/gemini-3-flash"`
  - Noah: `queryMode: "full"`, `maxSummaryChars: 400`, `timeoutMs: 20000`, `modelFallback: "anthropic/claude-haiku-4-5-20251001"`

**High priority batch — 30–60 min total per customer:**
- [ ] Josh: Fix SOUL.md emoji contradiction (E2) — 5 min
- [ ] Josh: Create MEMORY.md with seed data — 15 min
- [ ] Josh: Populate HEARTBEAT.md + add cron.json morning briefing (now Low risk) — 30 min
- [ ] Josh: Investigate iMessage monitoring pause (E10) — 10 min
- [ ] Noah: Fill USER.md (Noah Katz, PermitFlow AE, job search criteria) — 15 min
- [ ] Noah: Fill IDENTITY.md (anchor "CLAW" persona — drift risk rising) — 10 min
- [ ] Noah: Create MEMORY.md from April 22 report context — 15 min
- [ ] Noah: Add cron.json for weekly report + configure follow-up commitments — 30 min
- [ ] Noah: Replace misformatted HEARTBEAT.md — 10 min
- [ ] Noah: Increase contextPruning TTL from 5m to 15m — 5 min

**Upcoming:**
- [ ] Both: Enable Discord streaming (`"streaming": "partial"`) — 5 min each
- [ ] Both: Consider gog-cli cross-fleet (Josh lacks it; Noah's is audited clean)
- [ ] Both: Evaluate memory-lancedb-pro after 2–3 weeks of standard memory
- [ ] Josh: Configure voice persona post-update (E22)
- [ ] Noah: Verify exec-approvals.json defaults (E20)

---

## UPDATE — MORNING SCAN 2026-05-02

### Active Memory Plugin: Two-Step Install Required (Fleet-Wide Correction)

Previous documentation across all findings files described installing `memory-lancedb` as a single step. The Active Memory system actually requires **two separate components**:

1. **Storage layer:** `@openclaw/plugin-memory-lancedb` — vector database for storing memories on disk
2. **Recall agent:** The `active-memory` plugin entry in `openclaw.json` — a dedicated sub-agent that queries memory before each reply turn

Without step 2, memory is stored but never automatically surfaced. The `active-memory` recall agent is what makes memory "intelligent" — running a dedicated query on every reply to pull relevant context. This is confirmed stable in OpenClaw 2026.4.10+.

**Config divergence by use case (both customers need divergent `active-memory` config):**

| Setting | Josh (Heather) | Noah (Claw) | Reason |
|---------|---------------|------------|--------|
| `queryMode` | `"recent"` | `"full"` | Conversation vs. full historical research |
| `maxSummaryChars` | `220` | `400` | Richer company context needed |
| `timeoutMs` | `15000` | `20000` | Complex multi-entity research queries |
| `modelFallback` | `"google/gemini-3-flash"` | `"anthropic/claude-haiku-4-5-20251001"` | Must match customer's auth profile |
| `setupGraceTimeoutMs` | `30000` | `30000` | Required after 2026.5.2 upgrade (new) |

---

### Noah-Specific: memoryFlush Writing to Ephemeral Storage

Noah's `openclaw.json` has `compaction.memoryFlush.enabled: true` with `softThresholdTokens: 4000`. Without `memory-lancedb` installed, this flush writes to `/tmp` on Docker/Railway — wiped on every container restart.

Josh has no compaction settings, so he is not affected by this specific issue. For Noah, this is an additional urgency argument for the memory-lancedb install: the agent is actively flushing context it believes is being saved, but the data is ephemeral. Installing `memory-lancedb` with an explicit `storagePath` resolves this automatically.

---

### Josh-Specific: No Exec Security Settings — Risk at Update Time

Josh's `openclaw.json` has no `tools.exec` block. Noah has `tools.exec.security: "full"` explicitly configured. When Josh updates to OpenClaw 2026.4.1+ (required to reach 2026.5.2), the stricter exec defaults may cause silent exec failures.

**Fleet exec security posture (Morning 2026-05-02):**

| Customer | `tools.exec.security` | `exec-approvals.json` | Risk at Update |
|----------|----------------------|-----------------------|----------------|
| Josh (Heather) | Not set | Unknown | **Medium — add `security: "full"` before updating** |
| Noah (Claw) | `"full"` (explicit) | Needs verification (E20) | Low |

**Fix for Josh — add to `openclaw.json` before running `alphaclawctl update`:**
```json
"tools": {
  "profile": "full",
  "exec": {
    "security": "full"
  }
}
```

---

### memory-lancedb-pro: Available for Both Customers (Future Evaluation)

`memory-lancedb-pro` (CortexReach/memory-lancedb-pro) provides enhanced retrieval over standard `memory-lancedb`. Requires OpenClaw 2026.3.22+ (both customers qualify).

| Benefit | Josh Value | Noah Value |
|---------|-----------|------------|
| Hybrid Retrieval (Vector + BM25) | High — contact name keyword hits | High — company name keyword hits |
| Cross-Encoder Rerank | Medium — grows with contact count | High — 10+ companies × multiple sessions |
| Multi-Scope Isolation | Medium — work/personal separation | **High** — companies/contacts/job-search state as separate scopes |
| Management CLI | Low | Medium — pre-report memory audit |

**Recommendation:** Both customers should establish standard `memory-lancedb` first. Noah is the stronger candidate for eventual `memory-lancedb-pro` upgrade due to multi-scope isolation. Don't pre-optimize.

---

### Version Status (Morning 2026-05-02)

| Customer | Version | Latest Stable | Days Stale | Scan Days |
|----------|---------|--------------|-----------|----------|
| Josh (Heather) | 2026.3.22 | 2026.4.29 | **42 days** | Day 12 |
| Noah (Claw) | 2026.4.15 | 2026.4.29 | 17 days | Day 12 |

No implementations across either customer in 12 days of daily scanning.

---

### Updated Fleet-Wide Action Checklist (Morning 2026-05-02)

**Urgent (do first):**
- [ ] Noah: Trigger AE target companies report — 4 days overdue
- [ ] Josh: Add `tools.exec.security: "full"` before updating
- [ ] Both: Update to OpenClaw 2026.4.29 via `alphaclawctl update`
- [ ] Both: Install `memory-lancedb` + enable `active-memory` plugin (two-step, use divergent configs above)
- [ ] Noah: Verify `exec-approvals.json` defaults after update

**High priority:**
- [ ] Josh: Fix emoji contradiction in SOUL.md (E2)
- [ ] Josh: Create MEMORY.md + fix duplicate key in inbox-state.json
- [ ] Josh: Populate HEARTBEAT.md + add cron.json morning briefing
- [ ] Josh: Investigate iMessage monitoring pause (E10)
- [ ] Noah: Fill USER.md + IDENTITY.md + create MEMORY.md
- [ ] Noah: Add cron.json for weekly report with follow-up commitments enabled
- [ ] Noah: Replace misformatted HEARTBEAT.md
- [ ] Noah: Increase contextPruning TTL from 5m to 15m (E7)

**Future:**
- [ ] Both: Evaluate memory-lancedb-pro after 2–3 weeks of standard memory-lancedb operation
- [ ] Josh: Consider installing gog-cli (audited clean by Noah's install — see cross-analysis below)

---

## UPDATE — MORNING SCAN 2026-05-01

### Version Status Update

Latest stable OpenClaw is now `2026.4.29` (released April 30, 2026). Previous latest referenced in this document (`2026.4.14`) is 15 patch versions stale.

| Customer | Bot Name | OpenClaw Version | Latest Stable | Behind By |
|----------|----------|-----------------|--------------|----------|
| Josh | Heather Schwartz | 2026.3.22 | 2026.4.29 | **38 days, 37+ patch versions — longest gap on fleet** |
| Noah | Market Catalyst Agent | 2026.4.15 | 2026.4.29 | 16 patch versions |

---

### Fleet-Wide New Capability: Memory People-Aware Wiki (2026.4.29)

OpenClaw 2026.4.29 ships a major memory architecture upgrade relevant to every instance on the fleet. Memory is now a "people-aware wiki with provenance views" — organizing stored knowledge around people and entities rather than flat text chunks.

**Impact by use case:**
- **Josh (personal assistant):** Highest fleet impact. Heather would build a contact wiki for every person Josh interacts with via iMessage, email, and calendar. Provenance tracking means context like "Josh mentioned the Oben HiFi board meeting last Tuesday" persists and is retrievable.
- **Noah (career research):** Critical. The agent tracks companies, founders, investors, and hiring managers across research sessions. The people-aware wiki makes every AE target company a persistent, structured record that accumulates context across sessions — directly addresses the finding that the agent resets all research context on every restart.

**Install sequence (same for all):** `memory-lancedb` plugin → update to OpenClaw 2026.5.2 → enable `active-memory` plugin with use-case-appropriate config (including `setupGraceTimeoutMs: 30000` for 2026.5.2).

---

### Noah Use Case Clarification: Career Research, Not Stock Trading

Evening scan 2026-05-01 (Noah's findings.md, E2) confirmed that Noah's agent is performing **career research and job search assistance**, not stock catalyst hunting. The agent produced a detailed 21KB AE target companies report on April 22 — ranking 10 startups in construction tech, PropTech, and field service for Noah Katz's next AE role after PermitFlow.

**Fleet implications:**
- The "Market Catalyst Agent" label in the fleet overview is misleading; the actual use case is job search intelligence
- Noah's `gog-cli` skill (Google Workspace CLI) makes sense for this use case: email hiring managers via Gmail, track interview calendar events, manage research docs in Drive
- The weekly cron recommendation in Noah's findings.md should target career research delivery (weekly AE target update) rather than market close debrief

---

### gog-cli Skill (Noah): Audited Clean ✓ — Google Workspace Access Confirmed

Noah's `skills/gog-cli` skill has been audited. It is a legitimate Google Workspace CLI providing access to Gmail, Calendar, Drive, Sheets, Docs, Tasks, Contacts, and Meet — connected to `Ngkatz.ai@gmail.com`. No security concerns. The skill is well-structured, comprehensive, and appropriate for the career research use case.

**Cross-fleet gap:** Josh also uses Google (google:default auth, Google Calendar, Gmail) but does not have the gog-cli skill installed. Heather currently accesses Google services via the native API integration. Adding gog-cli would give Heather scripted, batch-capable access to Gmail search, calendar management, Drive, and Contacts — significantly expanding what she can automate on behalf of Josh.

**Recommendation:** Consider installing gog-cli on Josh's instance and connecting it to Josh's Google account. The skill has zero security concerns based on Noah's audit and would unlock capabilities like batch email operations, Drive file management, and Sheets integration for Josh's Bliss Lifestyle / Oben HiFi operations.

---

### Opt-In Follow-Up Commitments (2026.4.29) — Fleet-Wide Opportunity

OpenClaw 2026.4.29 ships opt-in follow-up commitments for heartbeat-delivered reminders. Agents can register time-bound commitments that persist across session restarts.

- **Josh:** Directly addresses E9/E14 — Heather is running proactive checks without any documented commitment tracking. This feature formalizes her behavior.
- **Noah:** Directly addresses E3 — the agent set a self-deadline of April 29 for a weekly report that was missed. With persistent commitments, this would have fired a heartbeat reminder.

---

### Updated Fleet-Wide Action Checklist (Morning 2026-05-01)

- [ ] Update all instances to OpenClaw 2026.4.29
- [ ] Install `memory-lancedb` on all instances (people-aware wiki unlocked in 2026.4.29)
- [ ] Create `workspace/MEMORY.md` on all repos (pre-seeded with known context)
- [ ] Fill in `workspace/TOOLS.md` on all repos (environment-specific notes)
- [ ] Josh: Enable Discord streaming + add cron.json + populate HEARTBEAT.md
- [ ] Josh: Investigate iMessage monitoring pause (E10)
- [ ] Josh: Fix emoji contradiction in SOUL.md (E2)
- [ ] Josh: Consider installing gog-cli for expanded Google Workspace automation
- [ ] Noah: Fill USER.md + IDENTITY.md + create MEMORY.md (critical — all blank)
- [ ] Noah: Trigger overdue AE target companies report update
- [ ] Noah: Add cron.json for weekly career research report delivery
- [ ] Noah: Configure HEARTBEAT.md with follow-up commitments (E11)
- [ ] Noah: Increase contextPruning TTL from 5m to 15m (E7)

---

## ORIGINAL ANALYSIS — 2026-04-21

---

## Fleet Version Status

| Customer | Bot Name | OpenClaw Version | Latest Stable | Behind By |
|----------|----------|-----------------|--------------|----------|
| Josh | Heather Schwartz | 2026.3.22 | 2026.4.14 | ~3 weeks |
| Noah | Market Catalyst Agent | 2026.4.9 | 2026.4.14 | ~2 weeks |

**Fleet action:** Both instances need an update. The jump from 2026.3.x to 2026.4.x is the most impactful — it includes cron reliability fixes and the Model Auth Status card.

---

## Workspace File Inventory

| File | Josh | Noah | Notes |
|------|------|------|-------|
| SOUL.md | ✓ Generic default | ✓ Generic default | Same SHA — fleet-shared template |
| IDENTITY.md | ✓ Filled in (Heather) | **✗ BLANK TEMPLATE** | Noah's bot has no identity |
| USER.md | ✓ Filled in (Josh's profile) | **✗ BLANK TEMPLATE** | Noah has zero persistent user context |
| TOOLS.md | ⚠️ Generic template | ⚠️ Generic template | Both unfilled boilerplate |
| AGENTS.md | ✓ Present | ✓ Present | Same SHA — fleet-shared |
| MEMORY.md | ✗ Missing | ✗ Missing | Neither has a MEMORY.md |
| cron.json | **✗ Missing** | **✗ Missing** | Neither has scheduled automation |
| BOOTSTRAP.md | ✓ Present | ✓ Present | Both present |
| hooks/ | ✓ Present | ✓ Present | Both have hook directories |

---

## Model Provider Comparison

| Customer | Primary Model | Fallbacks | Provider |
|----------|-------------|-----------|----------|
| Josh | gemini-3-flash-preview | openrouter/gemini-2.5-flash, openrouter/claude-3.5-haiku | Google + OpenRouter |
| Noah | claude-sonnet-4-6 | claude-opus-4-6 (in models, not fallbacks) | Anthropic only |

**Key risks:**
- **Josh's** claude-3.5-haiku fallback is retired — should upgrade to claude-haiku-4-5-20251001
- **Noah** has claude-opus-4-6 in the models catalog but it's not wired as a fallback in the model config. Upgrade both to 4.7 variants.

---

## Memory Plugin Status

**Both customers: No memory plugin configured.**

This is the single biggest capability gap across the fleet. Every instance wakes up with no persistent memory. Relevant context from past sessions (user preferences, ongoing projects, learned behaviors, business knowledge) is lost on every restart.

Memory impact by use case:
- **Josh (personal assistant):** High impact — should remember contacts, preferences, ongoing projects, communication style
- **Noah (career research):** Critical impact — should remember AE target companies, research assessments, Noah's job search criteria, contact list

**Recommended plugin:** `@openclaw/plugin-memory-lancedb`
Install + enable on all instances. See morning scan 2026-05-02 for the corrected two-step install (storage layer + `active-memory` recall agent). See morning scan 2026-05-04 for the 2026.5.2 `setupGraceTimeoutMs` addition.

---

## Automation (Cron) Comparison

| Customer | Has cron.json | # Jobs | Assessment |
|----------|--------------|--------|------------|
| Josh | No | 0 | **Gap** — personal assistant with no proactive actions |
| Noah | No | 0 | **Critical gap** — research bot with no scheduled reports |

Neither customer has cron automation configured. Both lose significant value by being reactive-only.

---

## Workspace File Gaps: Detailed

### MEMORY.md — Missing Across Both
Neither instance has a `workspace/MEMORY.md` file. Even without the memory plugin installed, a manually maintained MEMORY.md acts as session continuity — a human-readable scratch pad for the most important persistent facts.

**Recommended:** Add a `workspace/MEMORY.md` to both repos, pre-seeded with known context, and have each bot maintain it.

### TOOLS.md — Unfilled Boilerplate Across Both
Both TOOLS.md files contain only the default example content. This file is injected at bootstrap and wastes context window with generic non-information.
- **Josh:** iMessage contacts, email accounts, calendar IDs, home automation endpoints
- **Noah:** gog-cli account details, OAuth review date, Drive folder IDs, research cadence notes

---

## Channel Configuration Comparison

| Customer | Channel | Streaming | Group Policy | DM Policy |
|----------|---------|-----------|-------------|----------|
| Josh | Discord | **off** | open | open |
| Noah | Discord | not set (off) | **allowlist** | pairing |

- Josh's Discord streaming being `off` is the most impactful quick fix — set to `partial`
- Noah's security posture (allowlist + pairing) is appropriate for a research/trading bot

---

## Security Notes

1. **Noah's exec security** is set to `"full"` with `strictInlineEval: false` — the `false` on strictInlineEval allows inline JS eval in tools. Worth noting when auditing future skill installs.

2. **Josh has no exec settings at all** — post-2026.4.1 stricter defaults create a risk at update time. Fix documented in morning scans 2026-05-02 and 2026-05-03.

3. **ClawHub malware warning (2026 Q1):** 2,419 suspicious skills purged; 1,184 distributed wallet/credential-stealing malware. Noah has a `skills/` directory — gog-cli has been audited clean. Josh has no custom skills directory.

4. **Exposed instances:** ~21,639 OpenClaw instances remain publicly accessible on the internet as of March 2026. Both instances use `gateway.bind: loopback` + `trustedProxies: ["127.0.0.1"]` — good practice.

---

## Customer-Specific Improvement Summary

### Josh (Heather Schwartz) — Personal Assistant
**Top priorities:**
1. Add `tools.exec.security: "full"` before updating
2. Update OpenClaw to 2026.5.3
3. Install memory-lancedb + active-memory (two-step, `queryMode: "recent"`, add `setupGraceTimeoutMs: 30000`)
4. Create MEMORY.md + fix SOUL.md emoji bug
5. Add cron.json morning briefing (now Low risk) + populate HEARTBEAT.md
6. Post-update: configure ElevenLabs v3 voice persona (E22)

**Unique gap:** 44 days stale — longest update gap on the fleet.

---

### Noah (Market Catalyst Agent / Career Research) — Job Search Intelligence
**Top priorities:**
1. Trigger overdue AE target companies report immediately (13 days overdue, no config needed)
2. Fill USER.md + IDENTITY.md + create MEMORY.md — most fundamental gap, drift risk rising
3. Install memory-lancedb + active-memory (two-step, `queryMode: "full"`, add `setupGraceTimeoutMs: 30000`)
4. Update OpenClaw to 2026.5.3
5. Add cron.json for weekly report with follow-up commitments

**Unique asset:** `skills/gog-cli` — audited clean, full Google Workspace automation. Consider cross-fleet deployment to Josh.

**Unique risk:** memoryFlush active but writing to ephemeral storage — data being lost silently for 14+ days.

**Near-term architecture:** Parallel subagent research (E22/E15) — viable once update + memory-lancedb are in place. threadBindings.spawnSessions (2026.5.2+) is the config prerequisite.

---

## Fleet-Wide Action Checklist (Original — 2026-04-21)

- [ ] Update all instances to OpenClaw 2026.4.14
- [ ] Install `memory-lancedb` on all instances
- [ ] Create `workspace/MEMORY.md` on all repos (pre-seeded)
- [ ] Fill in `workspace/TOOLS.md` on all repos (environment-specific notes)
- [ ] Josh: Enable Discord streaming + add cron.json
- [ ] Noah: Add cron.json (research schedule) + fill in USER.md + IDENTITY.md
- [ ] Noah: Audit skills/ directory for malicious packages
- [ ] Noah: Review strictInlineEval: false in exec security config
