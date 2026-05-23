# Fleet Research — Josh (Heather Schwartz) | 2026-05-23 Evening Scan

**Scan type:** Full (web research + codebase analysis + platform news)
**Date:** 2026-05-23
**Instance:** Josh Meyers — Heather Schwartz (personal assistant)
**Repo:** lylle-rgb/josh_repo
**Previous scan:** 2026-05-22 evening

---

## Platform Status

| Item | Current | Latest | Gap |
|------|---------|--------|-----|
| OpenClaw | 2026.3.22 | **2026.5.20 stable** | **~2 months behind; alpha 2026.5.21 now in train** |
| AlphaClaw | Unknown | 0.9.16 | Check deployment |
| Primary model | google/gemini-3-flash-preview | — | Active |

---

## New Since Yesterday

### FINDING-JOSH-40 | OpenClaw 2026.5.21-alpha.1 Released Today — Transcript Durability Focus
**Severity:** INFO
**Status:** NEW (released 2026-05-23)

A new alpha train started today. 2026.5.21-alpha.1 focuses on **transcript correctness and agent state durability**:
- Prompt-owned assistant transcript writes
- Locked Pi session event persistence
- Setup/login coverage alignment

This is NOT a stable release — do not upgrade to alpha. But the theme is significant: the OpenClaw team is investing in making agent state (conversation history, session events) more durable before adding more automation features. This aligns directly with the fleet-wide MEMORY.md gap — native durability features are being hardened platform-side.

**Why it matters:** Once 2026.5.21 hits stable, agents upgraded to 2026.5.20+ with memory-core active will benefit from improved session transcript persistence. The platform is converging toward what the fleet needs.

**Exact changes to apply:** None yet. Monitor for 2026.5.21 stable.

**Risk level:** INFO

---

### FINDING-JOSH-41 | hooks/bootstrap Directory — Files Not Visible in Repo
**Severity:** HIGH
**Status:** NEW (identified this scan — needs verification)

Josh's `workspace/hooks/bootstrap/` appears to contain no files based on the GitHub repository listing. The `openclaw.json` bootstrap hook references:
```json
"paths": [
  "hooks/bootstrap/AGENTS.md",
  "hooks/bootstrap/TOOLS.md"
]
```

If these files don't exist on the VPS, the bootstrap hook fires but injects nothing. Heather would start every session without:
- The no-YOLO policy for system changes
- The commit-and-push workflow requirement
- Correct Google auth context (or any Google auth context at all)

This could explain why the MEMORY.md and HEARTBEAT.md have never been updated — if Heather is never told the no-YOLO rule or the commit workflow, she may not be acting on workspace file changes at all.

**What to verify on the VPS:**
```bash
ls /data/.openclaw/workspace/hooks/bootstrap/
```

**If files are missing, recreate them** — see soul-improvements-2026-05-23-evening.md Recommendation 4 for exact content.

**Risk level:** HIGH if confirmed missing — affects every session startup since deployment

---

### FINDING-JOSH-42 | ClawHub Skills Security Advisory
**Severity:** MEDIUM
**Status:** NEW (security note)

In early 2026, ClawHub purged 2,419 suspicious skills after discovery that 1,184 were distributing wallet-stealing malware. One package disguised as a Polymarket bot was downloaded 14,285 times before detection.

Heather's install has `tools.profile: "full"` (core platform tools, no ClawHub skill installs visible in the repo). Current risk is low, but this is relevant context if skills are added in the future.

**Guidance:** Only install skills from verified ClawHub publishers or the official `@openclaw/` namespace. Audit any existing `skills/` directory entries.

**Risk level:** LOW for current install; MEDIUM advisory for future skill additions

---

### FINDING-JOSH-43 | defineToolPlugin SDK — Custom Heather Tools Now Viable
**Severity:** INFO
**Status:** OPPORTUNITY (available since 2026.5.18)

OpenClaw 2026.5.18–2026.5.19 shipped `defineToolPlugin` plus `openclaw plugins build`, `validate`, and `init` — typed simple tool plugins with generated manifest metadata, optional tool declarations, and context factories.

**Heather-specific custom tools worth building:**
- **iMessage bridge health check tool:** Exposes real-time bridge status so Heather knows whether the bridge is operational before attempting iMessage reads/sends. Eliminates the inbox-state.json paused/unpaused ambiguity.
- **Bliss brand context tool:** Preloaded Bliss Lifestyle Official branding, tone, and social guidelines — instant reference for content assistance without re-explaining every session.

Neither requires ClawHub publishing. Both are local plugins.

**Risk level:** LOW / Future opportunity

---

## Persistent Findings (Unresolved from Previous Scans)

*All findings JOSH-30 through JOSH-39 remain unresolved. See [2026-05-22 evening findings](findings-2026-05-22-evening.md) for full detail.*

| Finding | Severity | Status | Day # |
|---------|----------|--------|-------|
| JOSH-30: MEMORY.md never created | CRITICAL | PERSISTENT | 34+ |
| JOSH-31: HEARTBEAT.md empty | HIGH | PERSISTENT | 34+ |
| JOSH-39: Upgrade to OpenClaw 2026.5.20 | HIGH | PENDING | 1 |
| JOSH-41: Bootstrap hook files missing | HIGH | NEW — VERIFY | 0 |
| JOSH-37: SOUL.md never personalized | MEDIUM | PERSISTENT | 34+ |
| JOSH-32: Bootstrap TOOLS.md false Google auth | MEDIUM | PERSISTENT | 34+ |
| JOSH-33: iMessage paused + malformed JSON | MEDIUM | PERSISTENT | 26+ |
| JOSH-34: Emoji contradiction | LOW | PERSISTENT | 2 |
| JOSH-35: streaming.mode progress available | INFO | OPPORTUNITY | — |
| JOSH-36: Mem0 plugin | INFO | OPPORTUNITY | — |
| JOSH-38: Crash notifications | INFO | OPPORTUNITY | — |
| JOSH-40: 2026.5.21-alpha.1 transcript durability | INFO | NEW | 0 |
| JOSH-42: ClawHub skills security | MEDIUM | NEW (advisory) | — |
| JOSH-43: defineToolPlugin custom skills | INFO | OPPORTUNITY | — |

---

## Platform Research Notes (2026-05-23)

- **OpenClaw latest stable:** 2026.5.20 (released 2026-05-21 20:44 UTC) — no new stable release today
- **OpenClaw 2026.5.21-alpha.1:** Released today — transcript correctness (prompt-owned writes, locked Pi session persistence, setup/login alignment). Not for production.
- **AlphaClaw latest:** 0.9.16 (May 15, 2026) — no new release
- **defineToolPlugin SDK (2026.5.18–2026.5.19):** `openclaw plugins build/validate/init` generate manifest metadata, typed tool declarations, context factories. First-class custom plugin development now supported.
- **fs-safe 0.2.7 (in 2026.5.20):** Improved Python-helper-off policy with best-effort Node write fallbacks for private stores, secret writes, run logs, and media attachments on Linux/macOS.
- **memory-core active memory (2026 state):** Redundancy filtering removes near-duplicate daily note snippets; time-decay reduces stale entry relevance. `memory_recall` NO LONGER in default allowlist — explicit `toolsAllow` override required for memory-lancedb slot.
- **OpenClaw 2026.5.21 theme:** "Make agent state durable before layering on more automation" — directly validates the fleet research priority on MEMORY.md and HEARTBEAT.md.
- **ClawHub security (2026 Q1):** 2,419 skills purged; 1,184 distributing wallet-stealing malware. Always verify skill provenance.
- **Discord voice (2026.5.20):** Multi-turn context maintained across voice messages — requires upgrade to access.
