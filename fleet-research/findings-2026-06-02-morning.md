# Fleet Research: Findings — 2026-06-02 Morning Scan

## Scan Metadata

| Field | Value |
|---|---|
| Scan Date | 2026-06-02 (morning) |
| Scan Type | Incremental |
| Instance | Heather Schwartz (Josh Meyers) |
| Repo | lylle-rgb/josh\_repo |
| Previous Scan | 2026-06-01 evening |
| Scanner | AlphaClaw Fleet Research |
| AlphaClaw UI | https://5.78.142.81.sslip.io |

---

## Platform Status

| Item | Current | Latest Stable | Gap | Notes |
|---|---|---|---|---|
| OpenClaw version | 2026.3.22 | 2026.5.27 | **72 days** | Upgrade HIGH priority |
| Latest Beta | — | 2026.5.31-beta.3 | — | Do NOT target — unstable |
| Primary model | google/gemini-3-flash-preview | — | New: gemini-3.1-flash-lite-preview | See JOSH-85 |
| MEMORY.md | Missing | Required | **Day 72** | CRITICAL |
| HEARTBEAT.md | Empty | Required | **Day 72** | HIGH |
| TOOLS.md | Empty template | Required | **Day 72** | MEDIUM |
| iMessage | Paused | — | — | Fix in 2026.5.27 — awaiting upgrade |

---

## New Findings — 2026-06-02 Morning (JOSH-85 through JOSH-87)

### JOSH-85 — Gemini 3.1 Flash-Lite Model Available — Speed Upgrade Prep (MEDIUM)

**Priority:** MEDIUM  
**Status:** New  
**Action type:** GitHub-only (prep step, zero risk)

Google's `gemini-3.1-flash-lite-preview` is now available via the Google AI provider and OpenRouter. Key metrics vs Josh's current `google/gemini-3-flash-preview`:

| Model | Speed | Cost vs Pro | Notes |
|---|---|---|---|
| google/gemini-3-flash-preview (current) | Baseline | ~1/4 cost | Josh's current primary |
| google/gemini-3.1-flash-lite-preview (new) | **363 tok/s — 45% faster** | **1/8 cost** | New; faster; cheaper |

For Heather's conversational workflows (email triage, quick calendar lookups, iMessage replies), faster token generation directly reduces response latency. The Flash-Lite model approaches full Flash performance while being materially cheaper and faster.

**Recommended action (GitHub-only):** Add `"google/gemini-3.1-flash-lite-preview": {}` to the `agents.defaults.models` block in `openclaw.json`. This makes the model available without changing the active primary. Primary can be switched separately after testing.

**Exact config change for `openclaw.json`:**
```json
"models": {
  "google/gemini-2.5-flash": {},
  "google/gemini-3-flash-preview": {},
  "google/gemini-3.1-flash-lite-preview": {}
}
```

**Risk:** LOW. Adding to the models block does not change the active primary model. Zero behavior change until Josh explicitly switches.

---

### JOSH-86 — v2026.5 Speed Benchmarks Confirmed — Upgrade Impact Quantified (INFO)

**Priority:** INFO (reinforcing JOSH-39)  
**Status:** New

Official v2026.5 performance benchmarks have been published, quantifying the delta from Josh's current 2026.3.22:

| Metric | 2026.3.x (Josh current) | 2026.5.x (upgrade target) | Improvement |
|---|---|---|---|
| Cold turn latency | 9.8s | 3.4s | **2.9× faster** |
| Warm turn latency | 7.5s | 3.0s | **2.5× faster** |
| Peak agent RSS | Baseline | −7% | Lower memory usage |
| Gateway startup | Eager-loading (slow) | Lazy-loading (fast) | v2026.5.4 improvement |

For Heather's interactive use case, every response turn carries the cold/warm latency penalty. At 2026.3.22, cold-start responses take ~9.8s before generation begins; post-upgrade that drops to ~3.4s. Across a multi-message email triage or calendar conversation this compounds meaningfully.

Additionally, **v2026.5.5 contained 50+ bug fixes** — the largest single-release fix count in the v2026.5 series.

**Action:** No new action required. Reinforces JOSH-39 upgrade urgency.

---

### JOSH-87 — File Transfer Plugin Available Post-Upgrade (INFO)

**Priority:** INFO  
**Status:** New

v2026.5.3 shipped a native **file transfer plugin** with four new agent tools:

| Tool | Function |
|---|---|
| `file_fetch` | Download/fetch a file by URL or path |
| `dir_list` | List directory contents |
| `dir_fetch` | Fetch an entire directory tree |
| `file_write` | Write content to a file |

For Heather's use case, potential applications:
- Fetch and summarize PDF attachments from email without shell workarounds
- Organize and list documents in Josh's workspace directory
- Fetch files shared by Josh for annotation or review
- Write structured summaries back to workspace files

Available post-upgrade to 2026.5.27. No action needed now — flag for post-upgrade exploration.

---

## Persistent Findings — All Unresolved Items (Day 72)

| ID | Summary | Priority | Days Open | Action Type | Status |
|---|---|---|---|---|---|
| JOSH-30/75/79 | MEMORY.md never created — 72 days of activity unremembered | CRITICAL | 72 | GitHub-only | Unresolved |
| JOSH-31/69 | HEARTBEAT.md empty — no proactive monitoring | HIGH | 72 | GitHub-only | Unresolved |
| JOSH-34/70 | Emoji contradiction: AGENTS.md vs USER.md STRICT rule | MEDIUM | 72 | GitHub-only | Unresolved |
| JOSH-37 | SOUL.md never personalized for Heather/Josh context | MEDIUM | 72 | GitHub-only | Unresolved |
| JOSH-39/66 | Upgrade to 2026.5.27 (iMessage, Active Memory Plugin) | HIGH | 72 | VPS-required | Unresolved |
| JOSH-42 | ClawHub skills security advisory | MEDIUM | — | VPS-required | Unresolved |
| JOSH-50 | Dead OpenRouter fallback in openclaw.json | MEDIUM | — | GitHub-only | Unresolved |
| JOSH-55 | TOOLS.md completely empty | MEDIUM | — | GitHub-only | Unresolved |
| JOSH-63 | BOOTSTRAP.md never deleted | MEDIUM | 72 | GitHub-only | Unresolved |
| JOSH-67 | Security group prompt isolation (post-upgrade) | HIGH | — | VPS (post-upgrade) | Blocked on upgrade |
| JOSH-72 | Active Memory Plugin available post-upgrade | HIGH | — | GitHub-only (prep) | Blocked on upgrade |
| JOSH-73 | iMessage paused — awaiting upgrade | MEDIUM | — | VPS (via upgrade) | Confirmed |
| JOSH-79 | AI memory temporal algorithm (+29.6 pts) | HIGH | 2 | GitHub-only (MEMORY.md) | Unresolved |
| JOSH-81 | 2026.5.31-beta.3: iMessage hardened + tool-call recovery | INFO | 1 | Monitor | Tracking |
| JOSH-82 | Agent recovery from interrupted tool calls | INFO | 1 | Post-upgrade validation | Tracking |
| JOSH-83 | Workboard orchestration primitives | INFO | 1 | Future planning | Tracking |
| JOSH-84 | Official Tokenjuice plugin | INFO | 1 | Post-upgrade cleanup | Tracking |
| JOSH-85 | Gemini 3.1 Flash-Lite model available (363 tok/s, 45% faster) | MEDIUM | 0 | GitHub-only (models block) | New |
| JOSH-86 | v2026.5 speed benchmarks: 2.9× cold, 2.5× warm | INFO | 0 | Reinforces upgrade | New |
| JOSH-87 | File transfer plugin available post-upgrade | INFO | 0 | Post-upgrade exploration | New |

---

## Immediate Action List

### Tier 1 — GitHub-Only (No VPS Required)

1. **[CRITICAL] Create MEMORY.md** — `workspace/MEMORY.md`. Day 72. Templates in soul-improvements docs. Addresses JOSH-30/75/79. Unlocks +29.6 pts temporal memory.
2. **[HIGH] Populate HEARTBEAT.md** — Replace empty file with proactive monitoring checklist. Addresses JOSH-31/69.
3. **[MEDIUM] Fix emoji contradiction in AGENTS.md** — Add Josh-specific override block explicitly disabling emoji reactions per USER.md STRICT rule. Addresses JOSH-34/70.
4. **[MEDIUM] Personalize SOUL.md** — Add Heather-specific context for luxury brand founder. Addresses JOSH-37.
5. **[MEDIUM] Populate TOOLS.md** — Document actual tool integrations (Google/Gmail, calendar, iMessage status). Addresses JOSH-55.
6. **[MEDIUM] Remove dead OpenRouter fallback** — Delete `openrouter/anthropic/claude-3.5-haiku` from openclaw.json fallbacks array. Addresses JOSH-50.
7. **[MEDIUM] Delete BOOTSTRAP.md** — `workspace/BOOTSTRAP.md`. Addresses JOSH-63.
8. **[MEDIUM] Add Gemini 3.1 Flash-Lite to models block** — See JOSH-85 config snippet above. Zero risk, prep step only.

### Tier 2 — VPS-Required

1. **[HIGH] Upgrade OpenClaw 2026.3.22 → 2026.5.27** — Resolves JOSH-39/66; enables iMessage (JOSH-73), Active Memory Plugin (JOSH-72), group prompt isolation (JOSH-67). Do NOT upgrade to beta.
2. **[HIGH — post-upgrade] Apply Active Memory Plugin config** — Addresses JOSH-72.
3. **[HIGH — post-upgrade] Verify iMessage resumes** — Addresses JOSH-73.
4. **[MEDIUM] Review ClawHub skills security advisory** — Addresses JOSH-42.

---

## Research Notes

### Upgrade Target Reconciliation

The cross-customer analysis (2026-05-31 morning) listed the upgrade target as `2026.5.28`, stating it was promoted to stable on 2026-05-30. However, the 2026-06-01 evening individual findings show `2026.5.28-beta.2` released 2026-05-29 as still beta. The correct current stable target is **2026.5.27** as confirmed by the most recent individual scan. The cross-customer analysis will be updated accordingly.

### Gemini 3.1 Flash-Lite — Decision Framework

Josh's current primary `google/gemini-3-flash-preview` scored 78% on SWE-bench Verified (outperforming Pro at 76.2% when benchmarked). The newer `gemini-3.1-flash-lite-preview` has not yet been benchmarked on the same suite, but token throughput and cost metrics are materially better. Recommended approach:

1. **Now:** Add `gemini-3.1-flash-lite-preview` to models block (GitHub-only, zero risk) — JOSH-85
2. **Post-upgrade:** Test Flash-Lite as primary for low-stakes conversational tasks (quick calendar checks, brief summaries)
3. **Keep:** `gemini-3-flash-preview` as the confirmed fallback for complex multi-step tasks

This hedges the upgrade: if Flash-Lite underperforms on complex tasks, Heather reverts to the proven primary at zero cost.

---

*Scan completed: 2026-06-02 morning. Next scan: 2026-06-02 evening.*
