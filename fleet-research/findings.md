# Fleet Research Findings — Josh / Heather Schwartz

> Morning scans: 2026-04-21, 2026-05-01, 2026-05-02, 2026-05-03, 2026-05-04 | Evening scans: 2026-04-22, 2026-04-23, 2026-05-01, 2026-05-02 | Agent: AlphaClaw Fleet Research

---

## MORNING SCAN — 2026-05-04

### Implementation Status — Day 14, 44 Days Stale

`openclaw.json` `meta.lastTouchedVersion` remains `2026.3.22`. No changes since April 21. Josh's instance is now **44 days behind** current stable (2026.5.2). This is the 14th consecutive daily scan with zero implementation.

**Execution note:** Two-step path to meaningful change: (1) add `tools.exec.security: "full"` to openclaw.json — 2 minutes; (2) run `alphaclawctl update` to 2026.5.2 — 10 minutes. Everything else in this list becomes easier after those two steps.

---

### E24: Update Target Advances to 2026.5.2 — New Current Stable

**Finding:** OpenClaw `v2026.5.2` is now the current stable release, advancing beyond the `2026.4.29` target cited since the May 1 morning scan. Josh's instance at `2026.3.22` is now 44 days stale against the latest stable.

**New in 2026.5.x relevant to Heather:**
- **Gateway and agent hot path improvements:** Startup, session listing, task maintenance, prompt prep, and plugin loading are all leaner. Session initialization will be noticeably faster post-update.
- **Plugin management reliability:** External plugin install, update, doctor repair, and dependency reporting are more robust — directly improves stability of the pending `memory-lancedb` install (M2/E18).
- **Gateway now fails-closed on invalid config:** Gateway startup no longer auto-restores invalid config; `openclaw doctor --fix` is the repair path. Post-update, run `openclaw doctor` before starting Heather to verify config validity.
- **Cron store permission hardening:** Cron store and run-log directories hardened to 0700; files to 0600 — applies to pending `workspace/cron.json` (M4).
- **ClawHub 429 annotation:** ClawHub now annotates rate-limit errors with reset windows, reducing confusion if plugin installs hit rate limits.

**Update command (unchanged):**
```bash
alphaclawctl update
```

**Action:** Update target changes from `2026.4.29` → `2026.5.2`. Add `tools.exec.security: "full"` (E20) first, then run `alphaclawctl update`, then run `openclaw doctor`.

**Risk:** Low. No breaking changes confirmed for Discord+Google setups in 2026.5.x.

---

### E25: Active Memory timeoutMs Cold-Start Behavior Changed in 2026.5.2

**Finding:** OpenClaw `v2026.5.2` removes an implicit 30,000ms cold-start grace period for the Active Memory plugin. Before this fix, the configured `timeoutMs` was silently extended by 30s during the first session after gateway start. The pending E18 config specifies `timeoutMs: 15000` — this previously ran as a ~45s cold-start budget without any signal to the user.

**What changes after upgrading:**
- Configured `timeoutMs` is now the hard budget on cold-start by default (no implicit grace).
- New optional `setupGraceTimeoutMs` key explicitly configures cold-start grace when needed.

**Updated E18 active-memory config — add `setupGraceTimeoutMs`:**
```json
"active-memory": {
  "enabled": true,
  "config": {
    "enabled": true,
    "agents": ["main"],
    "allowedChatTypes": ["direct"],
    "modelFallback": "google/gemini-3-flash",
    "queryMode": "recent",
    "promptStyle": "balanced",
    "timeoutMs": 15000,
    "setupGraceTimeoutMs": 30000,
    "maxSummaryChars": 220,
    "persistTranscripts": false,
    "logging": true
  }
}
```

Adding `setupGraceTimeoutMs: 30000` restores the prior cold-start behavior explicitly. After 2–3 weeks of stable memory operation, it can be removed to use the tighter 15s budget.

**Risk:** None today — Heather isn't using Active Memory yet. Apply this config when implementing M2/E18 post-upgrade.

---

### Updated Priority Table (Morning 2026-05-04)

| ID | Item | Impact | Risk | Effort | Status |
|----|------|--------|------|--------|--------|
| M2/E4/E13/E18/E25 | Install memory-lancedb + active-memory (use E25 updated config with setupGraceTimeoutMs) | **Critical** | Low | 15 min | ⏳ Pending (Day 14) |
| M1/E8/E12/E15/E24 | Update OpenClaw to **2026.5.2** stable | **Critical** — 44 days stale | Low | 10 min | ⏳ Pending (Day 14) |
| E20 | Add `tools.exec.security: "full"` before update | **High** — prevents silent exec failures at update time | None | 5 min | ⏳ Pending |
| E2 | Fix emoji contradiction in SOUL.md | **High** — active behavioral bug | None | 5 min | ⏳ Pending (Day 14) |
| E1 | Create MEMORY.md with seed data | **High** — broken session continuity | None | 15 min | ⏳ Pending (Day 14) |
| M4/E21 | Add cron.json morning briefing **(risk now Low)** | **High** — reactive → proactive | Low | 30 min | ⏳ Pending (Day 14) |
| E6/E9/E14 | Populate HEARTBEAT.md + follow-up commitments | **High** | None | 10 min | ⏳ Pending (Day 14) |
| E17 | Activate post-session skill distillation loop | **High** — prevents accumulated knowledge loss | None | 5 min | ⏳ Pending |
| E10 | Investigate + document iMessage pause | Medium | None | 10 min | ⏳ Pending |
| M3 | Enable Discord streaming | Medium — UX quality | Very Low | 5 min | ⏳ Pending (Day 14) |
| E22 | Configure voice persona post-update (ElevenLabs v3) | Low — additive capability | None | 15 min | ⬜ Post-update |
| E11 | Fix duplicate key in inbox-state.json | Low | None | 2 min | ⏳ Pending |
| M5 | Upgrade fallback model (claude-3.5-haiku → claude-haiku-4-5) | Low | Low | 5 min | ⏳ Pending (Day 14) |
| E7/E23 | Monitor gemini-3-flash-preview deprecation (GA path documented) | Low | None | 0 | ⬜ Watch |
| M6 | Fill in TOOLS.md | Low → Medium over time | None | Ongoing | ⏳ Pending (Day 14) |
| E16 | API key rotation cadence + diagnostics hygiene | Low today | None | 30 min | ⬜ Upcoming |
| E19 | Evaluate memory-lancedb-pro after baseline | Low — future upgrade | None | 0 | ⬜ Future |

---

## MORNING SCAN — 2026-05-03

### Implementation Status — Day 13, 43 Days Stale

`openclaw.json` `meta.lastTouchedVersion` remains `2026.3.22`. Morning check — no changes since yesterday's scans. Josh's instance is now **43 days behind** current stable (2026.4.29). This is the 13th consecutive daily scan with zero implementation across all findings.

**Pattern note:** 13 days, 15+ findings, 0 changes applied. The bottleneck is not awareness — it's execution. The implementation steps are documented, specific, and low-risk. Highest-leverage unblock: one session with Heather, run `alphaclawctl update` first, then work the list.

---

### E21: Task Brain — cron.json Morning Briefing Risk Downgraded

**Finding:** OpenClaw's Task Brain control panel (introduced in 2026.3.31 beta, confirmed stable in current production builds) unifies cron jobs, subagents, ACP, and background CLI processes onto a SQLite-backed ledger. A key behavioral improvement: isolated cron runs now **automatically clean up orphaned browser tabs and processes** when the run completes — whether it succeeds or fails.

**Why this matters for M4:** The pending cron.json morning briefing (M4, first raised April 21) was rated **Medium risk** due to concern that email or calendar automation mid-cron could leave orphaned browser processes. Task Brain's cleanup resolves that concern.

**M4 risk downgrade:** Medium → **Low**. The recommended `workspace/cron.json` from M4 (morning briefing at 8am PST weekdays + Friday week-wrap at 4pm) can be added without additional caution. Risk is now equivalent to enabling Discord streaming (M3 — Very Low).

**Action:** No new config needed. When implementing M4, proceed without the prior hesitation. Draft `cron.json` is in the April 21 morning scan section below.

**Risk:** None.

---

### E22: Voice Overhaul (2026.4.25) — Per-Agent Voice Persona Unlocks Post-Update

**Finding:** OpenClaw 2026.4.25 shipped a full TTS overhaul: per-agent and per-account voice personas, chat-scoped auto-TTS controls, and new provider support including **ElevenLabs v3**, Azure Speech, Inworld, and others. AGENTS.md already instructs Heather to use `sag` (ElevenLabs TTS) for voice storytelling and "storytime" moments when available.

**What this unlocks post-update:** After updating to 2026.5.2 (E24), Heather can have a configured voice persona — a specific ElevenLabs v3 voice consistent across all voice outputs. This requires no new skill installation if ElevenLabs is already connected via `sag`. The `sag` skill would continue working as before; the persona config adds identity consistency.

**Practical value for Josh:** Morning briefings read aloud in a consistent voice, calendar summaries delivered as audio, "storytime" moments Heather already knows to do — all now stylistically consistent rather than defaulting to whatever TTS voice is available that session.

**Action:** After updating OpenClaw, check 2026.4.25 docs for the voice persona config key (expected: `agents.defaults.voice.persona` or similar). No action before the update.

**Risk:** None today. Purely additive capability that unlocks post-update.

---

### E23: gemini-3-flash GA Path Confirmed — E7 Watch Updated

**Finding:** Confirmed from research: `google/gemini-3-flash` (GA) provides a 1M token context window, 66K output tokens, built-in web search, and URL context support — matching or exceeding `gemini-3-flash-preview`'s current capabilities. No EOL has been announced for the preview model as of today.

**Update to E7 watch:** The GA fallback path is now clearly identified. When the preview model is retired, the config change is a one-line update:
```json
"agents": {
  "defaults": {
    "model": {
      "primary": "google/gemini-3-flash"
    }
  }
}
```
This is a drop-in replacement — no other config changes needed.

**E7 status:** Active watch, no action today. GA fallback path now documented so the transition is a 5-minute change when needed.

**Risk:** Low. Preview model still active.

---

### Updated Priority Table (Morning 2026-05-03)

| ID | Item | Impact | Risk | Effort | Status |
|----|------|--------|------|--------|--------|
| M2/E4/E13/E18 | Install memory-lancedb + active-memory (two-step, corrected config) | **Critical** | Low | 15 min | ⏳ Pending (Day 13) |
| M1/E8/E12/E15 | Update OpenClaw to 2026.4.29 stable | **Critical** — 43 days stale | Low | 10 min | ⏳ Pending (Day 13) |
| E20 | Add `tools.exec.security: "full"` before update | **High** — prevents silent exec failures at update time | None | 5 min | ⏳ Pending |
| E2 | Fix emoji contradiction in SOUL.md | **High** — active behavioral bug | None | 5 min | ⏳ Pending (Day 13) |
| E1 | Create MEMORY.md with seed data | **High** — broken session continuity | None | 15 min | ⏳ Pending (Day 13) |
| M4/E21 | Add cron.json morning briefing **(risk now Low)** | **High** — reactive → proactive | **Low** ↓ | 30 min | ⏳ Pending (Day 13) |
| E6/E9/E14 | Populate HEARTBEAT.md + follow-up commitments | **High** | None | 10 min | ⏳ Pending (Day 13) |
| E17 | Activate post-session skill distillation loop | **High** — prevents accumulated knowledge loss | None | 5 min | ⏳ Pending |
| E10 | Investigate + document iMessage pause | Medium | None | 10 min | ⏳ Pending |
| M3 | Enable Discord streaming | Medium — UX quality | Very Low | 5 min | ⏳ Pending (Day 13) |
| E22 | Configure voice persona post-update (ElevenLabs v3) | Low — additive capability | None | 15 min | ⬜ Post-update |
| E11 | Fix duplicate key in inbox-state.json | Low | None | 2 min | ⏳ Pending |
| M5 | Upgrade fallback model (claude-3.5-haiku → claude-haiku-4-5) | Low | Low | 5 min | ⏳ Pending (Day 13) |
| E7/E23 | Monitor gemini-3-flash-preview deprecation (GA path now documented) | Low | None | 0 | ⬜ Watch |
| M6 | Fill in TOOLS.md | Low → Medium over time | None | Ongoing | ⏳ Pending (Day 13) |
| E16 | API key rotation cadence + diagnostics hygiene | Low today | None | 30 min | ⬜ Upcoming |
| E19 | Evaluate memory-lancedb-pro after baseline | Low — future upgrade | None | 0 | ⬜ Future |

---

## MORNING SCAN — 2026-05-02

### Implementation Status — Day 12, 42 Days Stale

`openclaw.json` `meta.lastTouchedVersion` remains `2026.3.22`. Morning check — no changes since last evening's scan. Josh's instance is now 42 days behind the current stable release.

---

### E18: Active Memory Plugin — Two-Step Install, Full Config Syntax Now Available

**Finding:** The Active Memory plugin (tracked since E3, April 22) is confirmed stable in OpenClaw 2026.4.10+. The install path requires **two separate components**, which were previously documented as one step:

1. **Storage layer:** `@openclaw/plugin-memory-lancedb` — the vector database that stores memories on disk
2. **Recall agent:** The `active-memory` plugin entry — a sub-agent that intelligently queries memory before each reply

The prior E4/M2 documentation only covered step 1. Step 2 is required to get the proactive, pre-reply memory surfacing behavior described in E13 (people-aware wiki with provenance views).

**Complete corrected config:**
```json
"plugins": {
  "allow": ["discord", "usage-tracker", "memory-lancedb", "active-memory"],
  "entries": {
    "memory-lancedb": {
      "enabled": true,
      "config": {
        "autoRecall": true,
        "autoCapture": true,
        "storagePath": "/data/.openclaw/memory"
      }
    },
    "active-memory": {
      "enabled": true,
      "config": {
        "enabled": true,
        "agents": ["main"],
        "allowedChatTypes": ["direct"],
        "modelFallback": "google/gemini-3-flash",
        "queryMode": "recent",
        "promptStyle": "balanced",
        "timeoutMs": 15000,
        "maxSummaryChars": 220,
        "persistTranscripts": false,
        "logging": true
      }
    }
  }
}
```

**Note (updated by E25):** After upgrading to 2026.5.2, add `"setupGraceTimeoutMs": 30000` to the active-memory config block to preserve the cold-start grace period that was previously implicit.

**Deprecation note:** Before 2026.4.10, lowercase `memory.md` was treated as a secondary fallback. This behavior is deprecated post-2026.4.10. When creating MEMORY.md (E1), the standard uppercase filename is already correct — no change needed.

**Runtime rule:** Plugin enabled + agent targeted (`"main"`) + `allowedChatTypes` matches session type + eligible persistent session = active memory runs on every reply turn.

**Risk:** Low. Same additive change as before, now correctly specified as two plugin entries.

---

### E19: memory-lancedb-pro — Enhanced Alternative with Hybrid Retrieval (Evaluate After Baseline)

**Finding:** `memory-lancedb-pro` (GitHub: CortexReach/memory-lancedb-pro) is a production-grade enhanced memory plugin for OpenClaw with meaningfully better retrieval for complex personal assistant use cases:

- **Hybrid Retrieval (Vector + BM25):** Combines semantic vector search with keyword matching. For Heather, "find everything Josh mentioned about Sarah from Oben HiFi" works even when the semantic vector doesn't surface the match — keyword search catches it.
- **Cross-Encoder Rerank:** Re-ranks retrieval results for higher precision. Matters when Josh's contact graph grows to dozens of recurring people.
- **Multi-Scope Isolation:** Separate memory scopes per context. Potential use: isolate professional contacts from personal ones, or iMessage history from email threads.
- **Management CLI:** Direct memory inspection and pruning without going through the agent (`openclaw config validate`, log grep for `memory-lancedb-pro`).

**Compatibility:** Requires OpenClaw 2026.3.22+. Josh's instance at 2026.3.22 qualifies.

**Install method (after baseline memory-lancedb is stable):**
```bash
curl -fsSL https://raw.githubusercontent.com/CortexReach/toolbox/main/memory-lancedb-pro-setup/setup-memory.sh -o setup-memory.sh
bash setup-memory.sh
```

**Recommendation:** Install standard `memory-lancedb` first (E13/M2 — use the correct two-step config from E18). Run it for 2–3 weeks. If recall precision is poor for contacts or specific conversation context, evaluate switching to `memory-lancedb-pro`. Don't pre-optimize before establishing a baseline.

**Risk:** Low — future upgrade path. No action today.

---

### E20: No Exec Security Settings — Risk at Update Time (Post-2026.4.1)

**Finding:** Josh's `openclaw.json` has no `tools.exec` configuration block at all. OpenClaw 2026.4.1 changed exec security defaults to strict allowlist mode. AlphaClaw's #49 fix seeds permissive defaults on boot — but only for instances that ran AlphaClaw after that fix was merged. Since Josh's instance is at 2026.3.22 (pre-dating 2026.4.1 by weeks), the exec seeding behavior may not have run in its current form.

**Risk at update time:** When Josh updates to 2026.5.2, exec commands could silently fail with "allowlist miss" errors if:
1. The AlphaClaw boot seeding (#49) did not run with the correct exec model
2. `exec-approvals.json` doesn't exist or has mismatched defaults

**Preventive action — add to `openclaw.json` before or during the update:**
```json
"tools": {
  "profile": "full",
  "exec": {
    "security": "full"
  }
}
```

**Also verify `exec-approvals.json` exists after updating:**
```json
{
  "defaults": {
    "security": "full",
    "ask": "off",
    "askFallback": "full"
  }
}
```

**Risk:** Medium at update time if not addressed. Low effort to preemptively add the exec settings before running `alphaclawctl update`.

---

### Updated Priority Table (Morning 2026-05-02)

| ID | Item | Impact | Risk | Effort | Status |
|----|------|--------|------|--------|--------|
| M2/E4/E13/E18 | Install memory-lancedb + active-memory (two-step, corrected config) | **Critical** — step-change capability | Low | 15 min | ⏳ Pending (Day 12) |
| M1/E8/E12 | Update OpenClaw to 2026.4.29 | **Critical** — prerequisite for memory wiki, 42 days stale | Low | 10 min | ⏳ Pending (Day 12) |
| E20 | Add `tools.exec.security: "full"` before update | **High** — prevents silent exec failures at update time | None | 5 min | ⏳ Pending (new) |
| E2 | Fix emoji contradiction in SOUL.md | **High** — active behavioral bug | None | 5 min | ⏳ Pending (Day 12) |
| E1 | Create MEMORY.md with seed data | **High** — broken session continuity | None | 15 min | ⏳ Pending (Day 12) |
| M4 | Add cron.json morning briefing | **High** — reactive → proactive | Medium | 30 min | ⏳ Pending (Day 12) |
| E6/E9/E14 | Populate HEARTBEAT.md + follow-up commitments | **High** — guardrails on active unconfigured behavior | None | 10 min | ⏳ Pending (Day 12) |
| E17 | Activate post-session skill distillation loop | **High** — prevents loss of 12 days accumulated knowledge | None | 5 min | ⏳ Pending |
| E10 | Investigate + document iMessage pause | Medium | None | 10 min | ⏳ Pending |
| M3 | Enable Discord streaming | Medium — UX quality | Very Low | 5 min | ⏳ Pending (Day 12) |
| E11 | Fix duplicate key in inbox-state.json | Low | None | 2 min | ⏳ Pending |
| M5 | Upgrade fallback model (claude-3.5-haiku → claude-haiku-4-5) | Low | Low | 5 min | ⏳ Pending (Day 12) |
| E7 | Monitor gemini-3-flash-preview deprecation | Low | None | 0 | ⬜ Watch |
| M6 | Fill in TOOLS.md | Low → Medium over time | None | Ongoing | ⏳ Pending (Day 12) |
| E16 | API key rotation cadence + diagnostics hygiene | Low today | None | 30 min | ⬜ Upcoming |
| E19 | Evaluate memory-lancedb-pro after baseline | Low — future upgrade | None | 0 | ⬜ Future |
| E15 | Track OpenClaw 2026.4.29-beta.2 features → stable | Informational | None | 0 | ⬜ Watch |

---

## EVENING SCAN — 2026-05-02

### Implementation Status — Day 11, All Findings Still Pending

`openclaw.json` `meta.lastTouchedVersion` remains `2026.3.22`. No configuration changes since the first scan on April 21. Josh's instance is now **41 days stale** — the longest gap on the fleet.

| ID | Finding | First Raised | Status |
|----|---------|-------------|--------|
| M2/E4/E13 | Install memory-lancedb (people-aware wiki) | 2026-04-21 | ⏳ Pending |
| M1/E8/E12 | Update OpenClaw to 2026.4.29 | 2026-04-21 | ⏳ Pending |
| E2 | Fix emoji contradiction in SOUL.md | 2026-04-22 | ⏳ Pending |
| E1 | Create MEMORY.md with seed data | 2026-04-22 | ⏳ Pending |
| M4 | Add cron.json morning briefing | 2026-04-21 | ⏳ Pending |
| E6/E9/E14 | Populate HEARTBEAT.md + follow-up commitments | 2026-04-23 | ⏳ Pending |
| E10 | Investigate + document iMessage pause | 2026-05-01 | ⏳ Pending |
| M3 | Enable Discord streaming | 2026-04-21 | ⏳ Pending |
| E11 | Fix duplicate key in inbox-state.json | 2026-05-01 | ⏳ Pending |
| M5 | Upgrade fallback model | 2026-04-21 | ⏳ Pending |
| E7 | Monitor gemini-3-flash-preview deprecation | 2026-04-23 | ⬜ Watch |
| M6 | Fill in TOOLS.md | 2026-04-21 | ⏳ Pending |

---

### E15: OpenClaw 2026.4.29-beta.2 Released — Subagent Routing, NVIDIA Provider, Memory Diagnostics

**Finding:** OpenClaw `2026.4.29-beta.2` is now available. Josh's instance (2026.3.22) is 41 days stale and still targeting 2026.4.29 stable as the update goal. The beta introduces additional features worth tracking:

**New since 2026.4.29 stable:**
- **Spawned subagent routing metadata** — when Heather spawns sub-agents (e.g., drafting an email while simultaneously checking calendar), session routing metadata makes these traceable and debuggable. Foundational infrastructure for future multi-task parallel workflows.
- **NVIDIA provider with image/audio/video/embedding** — adds multimedia AI capabilities. For Josh (luxury lifestyle brand at Bliss), this opens future paths for image analysis and richer content workflows without custom skill installation.
- **Partial recall on timeout + bounded REM preview diagnostics** — memory system now handles context timeouts gracefully with partial recall rather than a full reset. Makes it easier to diagnose memory gaps. Directly relevant once memory-lancedb is installed (E13/M2).
- **Safer Codex/OpenAI-compatible replay and streaming** — improves streaming stability; relevant once streaming is enabled (M3).
- **Active-run steering confirmed as default** — users can redirect in-progress tasks without canceling. Heather's long email drafting or calendar scheduling tasks can now be steered mid-run.

**Update target:** 2026.3.22 → `2026.4.29` stable. Do not run beta.2 in production.
**Risk:** Low. No breaking changes for Discord+Google setups.

---

### E16: Community Standard — Diagnostics Hygiene and Auth Boundary Cleanliness

**Finding:** The OpenClaw community in May 2026 has converged on operational expectations that will affect fleet reliability going forward:

- **Sanitized diagnostics exports**: The community now expects to export a diagnostic bundle when something breaks — without leaking credentials or personal data. Heather's `inbox-state.json` duplicate key bug (E11) is exactly the type of issue that surfaces cleanly in diagnostics bundles. Fixing it proactively is the right move.
- **Auth boundary cleanliness**: Fewer copied credentials, no stale identities leaking between conversations. Josh's `auth.profiles` uses API keys for both Google and OpenRouter — these should be rotated on a regular cadence, not left indefinitely since first setup.
- **Fail-fast behavior over silent failures**: The iMessage monitoring pause (E10) with no documented reason is a textbook example of the silent-failure pattern the community is moving away from. Every significant state change should have a logged reason.

**Action:** No config changes required today. Apply these hygiene practices as part of the pending implementation batch:
- Fix E11 (duplicate JSON key) when next in-session
- Document E10 (iMessage pause reason) in daily memory log
- Add an API key rotation note to TOOLS.md (see Rec #4 in soul-improvements.md)

**Risk:** Low today. Increases over time as credentials age.

---

### E17: Hermes Procedural Memory — Heather's Skill Distillation Loop Is Inactive

**Finding:** Hermes Agent (Nous Research, February 2026) uses a learning loop that reviews completed tasks and distills successful procedures into reusable skill documents — the same pattern described in Heather's AGENTS.md ("When you learn a lesson → update AGENTS.md, TOOLS.md, or the relevant skill"). Hermes formalizes this with an explicit post-task review step built into the heartbeat cycle.

**The gap:** Heather has been operating 11+ days and has:
- Successfully run proactive email and iMessage checks (inbox-state.json confirms activity as recently as April 30–May 1)
- Completed Google Workspace onboarding (memory/onboarding-google.md)
- Learned Josh's explicit preferences (no emoji reactions, direct communication style)

None of these workflows have been distilled into TOOLS.md, AGENTS.md, or task-specific notes. Heather is executing the proactive behavior described in AGENTS.md but not running the post-session distillation cycle. The accumulated operational knowledge lives in session context only and will not survive a full restart.

**Recommended action:** When implementing HEARTBEAT.md (Rec #5 in soul-improvements.md), add an explicit post-task distillation step. See Rec #8 in soul-improvements.md.
**Risk:** None. Prevents accumulated knowledge loss on the next full restart.

---

### Priority Table (Updated Evening 2026-05-02)

| ID | Item | Impact | Risk | Effort | Status |
|----|------|--------|------|--------|--------|
| M2/E4/E13 | Install memory-lancedb (people-aware wiki in 2026.4.29) | **Critical** — step-change capability | Low | 15 min | ⏳ Pending (Day 11) |
| M1/E8/E12 | Update OpenClaw to 2026.4.29 stable | **Critical** — prerequisite for memory wiki, 41 days stale | Low | 10 min | ⏳ Pending (Day 11) |
| E2 | Fix emoji contradiction in SOUL.md | **High** — active behavioral bug | None | 5 min | ⏳ Pending (Day 11) |
| E1 | Create MEMORY.md with seed data | **High** — broken session continuity | None | 15 min | ⏳ Pending (Day 11) |
| M4 | Add cron.json morning briefing | **High** — reactive → proactive | Medium | 30 min | ⏳ Pending (Day 11) |
| E6/E9/E14 | Populate HEARTBEAT.md + configure follow-up commitments | **High** — guardrails on active unconfigured behavior | None | 10 min | ⏳ Pending (Day 11) |
| E17 | Activate post-session skill distillation loop (Rec #8) | **High** — prevents loss of 11 days of accumulated knowledge | None | 5 min | ⏳ Pending |
| E10 | Investigate + document iMessage pause | Medium — undocumented gap in core integration | None | 10 min | ⏳ Pending |
| M3 | Enable Discord streaming | Medium — UX quality | Very Low | 5 min | ⏳ Pending (Day 11) |
| E11 | Fix duplicate key in inbox-state.json | Low — malformed file | None | 2 min | ⏳ Pending |
| M5 | Upgrade fallback model (claude-3.5-haiku → claude-haiku-4-5) | Low — fallback path only | Low | 5 min | ⏳ Pending (Day 11) |
| E7 | Monitor gemini-3-flash-preview deprecation | Low | None | 0 | ⬜ Watch |
| M6 | Fill in TOOLS.md | Low → Medium over time | None | Ongoing | ⏳ Pending (Day 11) |
| E16 | API key rotation cadence + diagnostics hygiene | Low today | None | 30 min | ⬜ Upcoming |
| E15 | Track OpenClaw 2026.4.29-beta.2 features → stable | Informational | None | 0 | ⬜ Watch |

---

## MORNING SCAN — 2026-05-01

### E12: OpenClaw 2026.4.29 Released — Josh Now 38 Days and 37+ Patch Versions Behind

**Finding:** OpenClaw released `2026.4.29` on April 30, 2026. Josh's instance remains at `2026.3.22`. The previous update target from E8 (`2026.4.27`) is itself 2 patch versions stale.

**New in 2026.4.28–2026.4.29 relevant to Heather:**
- **Memory people-aware wiki with provenance views** — memory now organizes around people and entities, tracking who said what and when. For Heather, iMessage contacts, email correspondents, and calendar attendees become structured contact records with full history. This is the most impactful feature release for a personal assistant use case in the entire 2026.4.x series.
- **Per-conversation Active Memory filters** — different conversation types surface different memories. Work emails recall professional context; casual messages recall personal preferences.
- **Opt-in follow-up commitments for heartbeat-delivered reminders** — agents can register explicit commitments ("check back on this email thread Friday") that persist across session restarts and fire via heartbeat. Directly addresses E9's finding that Heather is running proactive checks without documented guardrails.
- **Active-run steering by default** — users can redirect in-progress tasks without canceling and restarting. Relevant for long email drafting or calendar scheduling runs.
- **Visible-reply enforcement** — prevents silent message drops on Discord. Improves delivery reliability.
- **Tool security policy update** — configured tool sections can no longer implicitly widen restrictive profiles. Low direct impact for `tools.profile: "full"`, but overall platform stability improvement.

**Action:**
```bash
alphaclawctl update
```
**Risk:** Low. No breaking config changes for Discord+Google setups in this version range.

---

### E13: Memory People-Aware Wiki — Step-Change Capability for Personal Assistant

**Finding:** OpenClaw 2026.4.29 upgrades the memory architecture to a "people-aware wiki with provenance views." This moves from flat LanceDB text retrieval to a structured, contact-centric knowledge base.

**Why this matters for Heather specifically:**
- Every iMessage contact, email correspondent, and calendar attendee gets a persistent record with interaction history
- Provenance tracking means Heather can recall "Josh mentioned Sarah from Oben HiFi last Tuesday in the context of the board meeting" — not just that Sarah exists as a contact
- Per-conversation Active Memory filters allow work emails to recall professional context and personal messages to recall personal preferences, without cross-contamination
- Once enabled, every future session compounds — the memory wiki grows richer with each conversation

This is the single most impactful reason to install memory-lancedb and update OpenClaw. The capability gap between Heather-with-memory-wiki and Heather-without is the gap between a personal assistant that knows Josh and one that meets him anew every session.

**Priority escalation:** M2/E4 (install memory-lancedb) upgrades from **High** to **Critical**. The prerequisite is updating OpenClaw to 2026.4.29 (E12).
**Risk:** None — additive, non-breaking change.

---

### E14: Opt-In Follow-Up Commitments — Formalizes Heather's Unconfigured Proactive Behavior

**Finding:** OpenClaw 2026.4.29 ships opt-in inferred follow-up commitments for heartbeat-delivered reminders. Agents can register explicit commitments with a date, context, and action that survive session restarts and fire via the heartbeat system.

**Why this matters for E9:** Heather is already running proactive email and iMessage checks, but with no HEARTBEAT.md policy and no way to persist commitment context across sessions. With follow-up commitments:
- When Josh asks Heather to follow up on something Friday, that commitment is registered and persists
- The heartbeat fires the reminder at the right time even after a restart or compaction
- Quiet hours, cadence, and alert conditions can be set as persistent policy rather than ad-hoc session behavior

**Action:** After updating OpenClaw to 2026.4.29 and populating HEARTBEAT.md (E6/E9), check 2026.4.29 release docs for the commitments opt-in flag (likely `commitments.enabled: true` in heartbeat config or agent defaults).
**Risk:** None until configured. Strictly opt-in — zero behavior change before enabling.

---

### Priority Table (Updated Morning 2026-05-01)

| ID | Item | Impact | Risk | Effort | Status |
|----|------|--------|------|--------|--------|
| M2/E4/E13 | Install memory-lancedb (people-aware wiki in 2026.4.29) | **Critical** — step-change capability | Low | 15 min | ⏳ Pending |
| M1/E8/E12 | Update OpenClaw to 2026.4.29 | **Critical** — prerequisite for memory wiki, 38 days stale | Low | 10 min | ⏳ Pending |
| E2 | Fix emoji contradiction in SOUL.md | **High** — active behavioral bug | None | 5 min | ⏳ Pending |
| E1 | Create MEMORY.md with seed data | **High** — broken session continuity | None | 15 min | ⏳ Pending |
| M4 | Add cron.json morning briefing | **High** — reactive → proactive | Medium | 30 min | ⏳ Pending |
| E6/E9/E14 | Populate HEARTBEAT.md + configure follow-up commitments | **High** — guardrails on active unconfigured behavior | None | 10 min | ⏳ Pending |
| E10 | Investigate + document iMessage pause | Medium — undocumented gap in core integration | None | 10 min | ⏳ Pending |
| M3 | Enable Discord streaming | Medium — UX quality | Very Low | 5 min | ⏳ Pending |
| E11 | Fix duplicate key in inbox-state.json | Low — malformed file | None | 2 min | ⏳ Pending |
| M5 | Upgrade fallback model | Low — fallback path only | Low | 5 min | ⏳ Pending |
| E7 | Monitor gemini-3-flash-preview deprecation | Low | None | 0 | ⬜ Watch |
| M6 | Fill in TOOLS.md | Low → Medium over time | None | Ongoing | ⏳ Pending |

---

## EVENING SCAN — 2026-05-01

### Implementation Status — All Findings Still Pending (Day 10)

`openclaw.json` `meta.lastTouchedVersion` remains `2026.3.22`. Confirmed zero configuration changes since the first scan on April 21. All 10 prior findings remain unimplemented across 10 days.

| ID | Finding | First Raised | Status |
|----|---------|-------------|--------|
| E2 | Fix emoji contradiction in SOUL.md | 2026-04-22 | ⏳ Pending |
| E1 | Create MEMORY.md with seed data | 2026-04-22 | ⏳ Pending |
| M2/E4 | Install memory-lancedb + storage path | 2026-04-21 | ⏳ Pending |
| M4 | Add cron.json morning briefing | 2026-04-21 | ⏳ Pending |
| E6 | Populate HEARTBEAT.md | 2026-04-23 | ⏳ Pending |
| M3 | Enable Discord streaming | 2026-04-21 | ⏳ Pending |
| M1/E5 | Update OpenClaw | 2026-04-21 | ⏳ Pending |
| M5 | Upgrade fallback model | 2026-04-21 | ⏳ Pending |
| E7 | Monitor gemini-3-flash-preview deprecation | 2026-04-23 | ⬜ Watch |
| M6 | Fill in TOOLS.md | 2026-04-21 | ⏳ Pending |

---

### E8: OpenClaw 2026.4.27 Released — Josh Now 40 Days and 27+ Patch Versions Behind

**Finding:** OpenClaw released `2026.4.27` on April 27, 2026. Josh's instance is at `2026.3.22` — 40 days and at minimum 27 patch versions behind the current stable release. This is the longest gap on the fleet.

**New in 2026.4.22–2026.4.27 relevant to Heather:**
- **Codex Computer Use** — AI-driven desktop control with fail-closed MCP checks. Potential path to richer Gmail/Google Calendar automation beyond the current API approach.
- **DeepInfra bundled provider** — image/audio understanding, TTS, text-to-video. New provider option beyond Google/OpenRouter, less relevant for Heather's current use case but available.
- **Manifest-first plugin/model catalogs** — reduces Gateway boot time; provider config (aliases, suppressions) easier to audit. Helps stability.
- **Reliability fixes** — Gateway startup prewarm, session/history defaults, update sync. Directly improves Heather's session reliability.
- **AlphaClaw Docker EBUSY self-update fix** — if Josh is on Railway/Docker, `alphaclawctl update` previously failed with an EBUSY error. This is now fixed. Self-updates via AlphaClaw now work correctly.

**Action:** Update via AlphaClaw managed update:
```bash
alphaclawctl update
```
**Risk:** Low. No breaking config changes for Discord+Google setups in this version range.

---

### E9: Heather IS Running Proactive Checks — But Without Configuration Guardrails

**Finding:** `workspace/memory/inbox-state.json` reveals Heather is actively performing proactive email and iMessage checks despite `HEARTBEAT.md` being empty:
- **Email:** Last checked ~April 30 / May 1, 2026 (today or yesterday)
- **iMessage:** Last checked ~April 27–28, 2026

Heather has self-organized a proactive check routine using `inbox-state.json` as state tracking, exactly as AGENTS.md describes — but she is doing this without any `HEARTBEAT.md` instructions defining *what* to check, *when* to alert Josh, or *what quiet hours* to respect. She is operating on instinct, not on documented policy.

**Why this matters:** The proactive behavior is alive and working — this is good. But without a HEARTBEAT.md config:
- There is no documented policy to debug if she becomes noisy or too quiet
- There are no explicit quiet hours (23:00–08:00 PST) to prevent late-night pings
- There is no rotation policy across email / calendar / memory maintenance
- Future-Heather in a fresh session won't know what cadence was in effect

**Action:** Populating HEARTBEAT.md (soul-improvements.md Rec #5) is now more urgent than previously rated. Heather is running unconfigured. The config content is already drafted in `soul-improvements.md` — this is a copy-paste operation.
**Risk:** None.

---

### E10: iMessage Monitoring Paused — Undocumented

**Finding:** `workspace/memory/inbox-state.json` contains `"imessage_monitoring_paused": true`. No workspace file explains why iMessage monitoring was paused, when it happened, or under what conditions it should be resumed.

**Why this matters:** iMessage is one of Josh's three core integrations (email, calendar, iMessage). If monitoring is silently paused with no documented reason:
- Heather in a fresh session will not know if this was intentional or a bug
- A permission error, expired token, or deliberate user request are three very different causes — each requiring a different response
- Josh may be expecting iMessage awareness that is currently inactive

**Action:** In the next live session:
1. Ask Heather why iMessage monitoring is paused (or review memory for context)
2. Check with Josh if this was intentional
3. Document the reason in `memory/YYYY-MM-DD.md` and optionally in `TOOLS.md`
4. Resume if appropriate, or leave paused with a documented reason

**Risk:** None to investigate. Medium impact if it stays undocumented.

---

### E11: inbox-state.json — Duplicate Key Bug (Malformed JSON)

**Finding:** `workspace/memory/inbox-state.json` contains a duplicate `last_email_check_ms` key:

```json
{
  "last_email_check_ms": 1777087800000,
  "already_drafted_thread_ids": [...],
  "imessage_monitoring_paused": true,
  "last_imessage_check_ms": 1777271400000,
  "last_email_check_ms": 1777551900000
}
```

Most JSON parsers silently use the last occurrence (behavior is correct in practice), but this is a malformed file. Strict JSON parsers will error on it. The duplicate suggests Heather updated the file by appending a new key rather than updating the existing one in-place — a JSON write pattern issue.

**Action:** Have Heather rewrite `inbox-state.json` cleanly in the next session:
```json
{
  "already_drafted_imessage_guids": [],
  "already_drafted_thread_ids": ["19db60d96d2118c8"],
  "imessage_monitoring_paused": true,
  "last_email_check_ms": 1777551900000,
  "last_imessage_check_ms": 1777271400000
}
```
See soul-improvements.md Rec #6 for a new AGENTS.md rule about atomic JSON file updates.
**Risk:** Low — current behavior is correct due to last-key-wins parsing. Fix is trivial.

---

### Priority Summary (Updated Evening 2026-05-01)

| ID | Item | Impact | Risk | Effort | Status |
|----|------|--------|------|--------|--------|
| E2 | Fix emoji contradiction in SOUL.md | **High** — active behavioral bug | None | 5 min | ⏳ Pending |
| E1 | Create MEMORY.md with seed data | **High** — broken continuity | None | 15 min | ⏳ Pending |
| M2/E4 | Install memory-lancedb (with storage path) | **High** — core persistence | Low | 15 min | ⏳ Pending |
| M4 | Add cron.json morning briefing | **High** — reactive → proactive | Medium | 30 min | ⏳ Pending |
| E6/E9 | Populate HEARTBEAT.md (now urgent — Heather running unconfigured) | **High** — enable guardrails on active behavior | None | 10 min | ⏳ Pending |
| E10 | Investigate + document iMessage pause | Medium — undocumented gap in core integration | None | 10 min | ⏳ Pending |
| M3 | Enable Discord streaming | Medium — UX quality | Very Low | 5 min | ⏳ Pending |
| M1/E8 | Update OpenClaw to 2026.4.27 | Medium — stability, 40 days stale | Low | 10 min | ⏳ Pending |
| E11 | Fix duplicate key in inbox-state.json | Low — malformed file | None | 2 min | ⏳ Pending |
| M5 | Upgrade fallback model | Low — fallback path only | Low | 5 min | ⏳ Pending |
| E7 | Monitor gemini-3-flash-preview deprecation | Low | None | 0 | ⬜ Watch |
| M6 | Fill in TOOLS.md | Low → Medium over time | None | Ongoing | ⏳ Pending |

---

## EVENING SCAN — 2026-04-23

### Implementation Status — All Findings Still Pending

No changes have been applied to Josh's instance since the first scan. `openclaw.json` `meta.lastTouchedVersion` remains `2026.3.22`, confirming zero updates.

| ID | Finding | First Raised | Status |
|----|---------|-------------|--------|
| E1 | Create MEMORY.md with seed data | 2026-04-22 | ⏳ Pending |
| E2 | Fix emoji reaction contradiction in SOUL.md | 2026-04-22 | ⏳ Pending |
| E3 | Active Memory Plugin (watch for stable) | 2026-04-22 | ⬜ Future |
| E4 | Memory plugin storage path config | 2026-04-22 | ⏳ Pending |
| M1 | Update OpenClaw | 2026-04-21 | ⏳ Pending |
| M2 | Install memory-lancedb | 2026-04-21 | ⏳ Pending |
| M3 | Enable Discord streaming | 2026-04-21 | ⏳ Pending |
| M4 | Add cron.json morning briefing | 2026-04-21 | ⏳ Pending |
| M5 | Upgrade fallback model | 2026-04-21 | ⏳ Pending |
| M6 | Fill in TOOLS.md | 2026-04-21 | ⏳ Pending |

---

### E5: OpenClaw 2026.4.21 Released — Update Target Bumped

**Finding:** OpenClaw released `2026.4.21` on April 21, 2026. Josh's instance is still at `2026.3.22`. The update target from the previous morning scan (`2026.4.14`) is now itself 7 patch versions stale. Heather is now running 29+ days behind the latest stable.

**New in 2026.4.21 relevant to Heather:**
- Instant `ax<N>` accessibility tree validation during browser automation — immediate rejection of invalid element references with a clear error instead of a silent timeout hang. More reliable browser-based email and calendar actions.
- Browser automation speed improvements overall.
- Plugin dependency recovery — plugins with missing peer deps are now recoverable instead of silently broken on restart.
- Slack thread routing fix (not relevant to Discord, but reflects overall platform stability work on channel delivery).
- npm install warning cleanup — cleaner plugin installs when adding memory-lancedb or other plugins.

**Action:** Update upgrade target from `2026.4.14` to `2026.4.21`.
```bash
alphaclawctl update
```
**Risk:** Low. No breaking changes in this range for Discord setups.

---

### E6: HEARTBEAT.md Still Empty — Proactive System Has Been Dormant for 3 Days

**Finding:** `workspace/HEARTBEAT.md` still contains only the stock skip-comment. No heartbeat tasks have been configured since the first scan on April 21. Heather has been running as a purely reactive Discord bot despite AGENTS.md describing a full proactive email/calendar/memory heartbeat cadence.

**Why it matters:** Heather's highest value for Josh (founder/CEO with email, calendar, and iMessage access) comes from proactive behavior — surfacing an important email before he asks, flagging a calendar clash, noticing something worth mentioning. The infrastructure for all of this is built and described in AGENTS.md. It's just not active because HEARTBEAT.md is empty.

**Action:** Apply Recommendation #5 from `soul-improvements.md`. The full HEARTBEAT.md content is already drafted there — it is a copy-paste operation.
**Risk:** None. Zero cost when empty; very low cost once active (1-2 API calls per heartbeat interval).

---

### E7: Preview Model Risk — gemini-3-flash-preview Has No GA Fallback

**Finding:** Josh's primary model is `google/gemini-3-flash-preview`. Preview models operate outside standard deprecation notice cycles and can be retired with short lead time. The configured fallback (`openrouter/google/gemini-2.5-flash`) covers a Gemini outage but is a different model tier than the preview. There is no pinned GA equivalent of `gemini-3-flash` in the fallback chain.

**Action:** No immediate change needed. Monitor Google's preview model lifecycle announcements. If `gemini-3-flash-preview` is deprecated or enters EOL notice, update `agents.defaults.model.primary` to `google/gemini-3-flash` (GA).
**Risk:** Low today. Medium if preview is deprecated without a config update.

---

## EVENING SCAN — 2026-04-22

### Morning Findings Status Check

None of the morning findings have been applied yet. All 6 items remain pending as of the evening scan.

| # | Finding | Status |
|---|---------|--------|
| 1 | Update OpenClaw 2026.3.22 → 2026.4.14 | ⏳ Pending |
| 2 | Install memory-lancedb | ⏳ Pending |
| 3 | Enable Discord streaming | ⏳ Pending |
| 4 | Add cron.json morning briefing | ⏳ Pending |
| 5 | Upgrade fallback model (claude-3.5-haiku → claude-haiku-4-5) | ⏳ Pending |
| 6 | Fill in TOOLS.md | ⏳ Pending |

---

### Evening Finding E1: MEMORY.md Missing — Continuity Broken

**Finding:** No `workspace/MEMORY.md` file exists. The `memory/` directory contains only `memory/onboarding-google.md` from initial setup. No daily session logs (`memory/YYYY-MM-DD.md`) exist either.

**Why it matters:** AGENTS.md explicitly instructs Heather to read `MEMORY.md` at session startup for long-term context — but the file doesn't exist. Every session starts completely cold regardless of what's been learned in prior sessions. The memory directory is present but effectively unused.

**Action:** In the next live session, prompt Heather to:
1. Read `memory/onboarding-google.md`
2. Create `workspace/MEMORY.md` with distilled facts from onboarding (Josh's name, timezone, businesses, no emoji reactions preference)
3. Create today's daily log `memory/2026-04-22.md`

**Risk:** None. High impact.

---

### Evening Finding E2: SOUL.md ↔ USER.md Contradiction — Emoji Reactions (Active Bug)

**Finding:** A direct behavioral contradiction exists between two files loaded at bootstrap:

- **USER.md:** `"STRICT: DO NOT SEND EMOJI REACTIONS TO MESSAGES."`
- **AGENTS.md:** Has a full section "React Like a Human!" explicitly instructing Heather to use emoji reactions on Discord (👍, ❤️, 😂, etc.)

**Why it matters:** Both files are loaded at every session bootstrap. These instructions directly conflict. Heather's behavior on emoji reactions is currently unpredictable — she may react to Josh's messages despite his explicit request not to. This is an active UX bug.

**Action:** Add an explicit override to SOUL.md. See `soul-improvements.md` for the exact recommended text.
**Risk:** Medium — active behavioral conflict affecting user experience today.

---

### Evening Finding E3: Active Memory Plugin Available (2026.4.15 Beta)

**Finding:** OpenClaw 2026.4.15 beta introduces a dedicated **Active Memory Plugin** — a memory sub-agent with configurable context modes (`message`, `recent`, `full`). Unlike basic LanceDB (embedding retrieval), the sub-agent actively decides what past context is relevant to surface during each conversation.

**Why it matters for Heather:** Personal assistant is the highest-value use case for this. The sub-agent proactively surfaces things like "Josh mentioned the Oben HiFi board meeting is this week" during relevant calendar conversations — without requiring explicit retrieval triggers.

**Action:** Install `memory-lancedb` first (morning finding #2, still pending). Watch for 2026.4.15 stable release to evaluate Active Memory Plugin as an upgrade.
**Risk:** None — future upgrade path.

---

### Evening Finding E4: Plugin Storage Path — Memory May Not Survive Restarts

**Finding:** A recent AlphaClaw managed fix now correctly exports `OPENCLAW_STATE_DIR` through startup so plugins write durable artifacts to `/data/.openclaw` instead of ephemeral `/tmp`. Older deployments may not have received this fix properly.

**Why it matters:** If memory-lancedb is installed without explicit path configuration, it may write indexes to `/tmp` — wiped on every container restart. Memory would appear to work but silently reset after every host restart or Railway redeploy.

**Action:** When installing memory-lancedb, explicitly configure the storage path:
```json
"memory-lancedb": {
  "config": {
    "autoRecall": true,
    "autoCapture": true,
    "storagePath": "/data/.openclaw/memory"
  }
}
```
**Risk:** Low — only relevant once memory-lancedb is installed.

---

## MORNING SCAN — 2026-04-21

## 1. OpenClaw Version — UPDATE NEEDED

**Finding:** Running `2026.3.22`. Latest stable is `2026.4.14`; beta `2026.4.15` is also available.

**Why it matters for Heather:** Version 2026.4.x includes a critical cron delivery regression fix (Discord cron jobs broke with "Unknown Channel" errors on some 2026.4.x builds and were resolved), the new Model Auth Status card so you can see OAuth health at a glance, and Gemini TTS support (useful for future voice reply features).

**Action:**
```bash
npm install -g openclaw@latest
# or via alphaclaw wizard:
alphaclawctl update
```
**Risk:** Low. No breaking config changes in 2026.3 → 2026.4 for Discord setups.

---

## 2. Memory Plugin — NOT CONFIGURED (workspace/memory/ dir exists but unused)

**Finding:** `workspace/memory/` directory is present but no `memory-lancedb` plugin is installed or enabled in `openclaw.json`. All of Heather's learned context about Josh (preferences, ongoing projects, contacts) evaporates between restarts.

**Why it matters:** Josh's use case (personal assistant, iMessage, email, calendar) is the highest-value memory use case on the fleet. Heather needs to remember things like email preferences, calendar habits, recurring contacts, and project context across sessions.

New in `2026.4.15` beta: `memory-lancedb` now supports **cloud/remote object storage** — meaning memory survives host restarts and migrations.

**Action (install memory plugin):**
```bash
npm install @openclaw/plugin-memory-lancedb
```

Add to `openclaw.json`:
```json
"plugins": {
  "allow": ["discord", "usage-tracker", "memory-lancedb"],
  "entries": {
    "memory-lancedb": {
      "enabled": true,
      "config": {
        "autoRecall": true,
        "autoCapture": true,
        "storagePath": "/data/.openclaw/memory"
      }
    }
  }
}
```
**Risk:** Low. Additive change, no existing config disrupted.

---

## 3. Discord Streaming — DISABLED

**Finding:** `channels.discord.streaming: "off"` — responses are sent as single blocks.

**Why it matters:** With streaming enabled, Josh sees Heather "typing" in real-time, making the interaction feel much more natural and responsive. This is especially noticeable for longer responses (calendar summaries, email drafts).

**Action:** In `openclaw.json`:
```json
"channels": {
  "discord": {
    "streaming": "partial"
  }
}
```
**Risk:** Very low. Can revert instantly.

---

## 4. No Cron Automation — OPPORTUNITY

**Finding:** No `workspace/cron.json` exists. Heather has no proactive scheduled actions.

**Why it matters:** A personal assistant should push information to Josh proactively — not just respond reactively. Josh is a founder/CEO in LA (PST), so timed briefings add significant value.

**Suggested starter `workspace/cron.json`:**
```json
{
  "jobs": [
    {
      "name": "morning_briefing",
      "schedule": "0 8 * * 1-5",
      "task": "Send Josh a morning briefing: today's calendar events, any unread priority emails (flag subject lines), and 1-2 things worth knowing. Keep it tight. DM to Josh on Discord."
    },
    {
      "name": "friday_week_wrap",
      "schedule": "0 16 * * 5",
      "task": "Send Josh a short end-of-week summary: what was accomplished, anything still open, what's on next week. DM to Josh on Discord."
    }
  ]
}
```
**Risk:** Medium. Test the first cron manually with a `/cron run morning_briefing` before enabling.

---

## 5. Fallback Model Upgrade

**Finding:** OpenRouter fallback is `openrouter/anthropic/claude-3.5-haiku` — this model is retired/old. The current equivalent is `claude-haiku-4-5-20251001`.

**Action:** In `openclaw.json` `agents.defaults.model.fallbacks`:
```json
"fallbacks": [
  "openrouter/google/gemini-2.5-flash",
  "openrouter/anthropic/claude-haiku-4-5-20251001"
]
```
**Risk:** Low. Fallbacks only activate when primary fails.

---

## 6. TOOLS.md — Blank Template

**Finding:** `workspace/TOOLS.md` contains only the boilerplate example. No actual tool notes for Heather's setup.

**Why it matters:** TOOLS.md is injected at bootstrap. A blank file wastes context tokens and provides no actual guidance. Heather should have notes on Josh's iMessage contacts aliases, email account names, calendar IDs, etc.

**Action:** Heather (or Josh) should populate TOOLS.md during a session with specifics like:
- Preferred email account labels
- Calendar names/IDs
- Common contacts and their relationship to Josh
- Any SSH or home automation endpoints

**Risk:** None.
