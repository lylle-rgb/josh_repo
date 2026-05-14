# Fleet Research — Josh / Heather Schwartz — Evening Scan

**Scan Date:** 2026-05-14 (Evening)  
**Agent:** AlphaClaw Apex Fleet Research Agent  
**Instance:** Josh — Discord bot Heather Schwartz (personal assistant: iMessage, email, calendar, contacts)  
**Previous findings:** `findings-2026-05-14-morning.md` (Day 27 Morning). All prior findings remain unresolved.

---

## Platform News (New Since May 14 Morning Scan)

### v2026.5.7 Remains Current Stable — No New Release Today

No new stable release today. Beta series is at v2026.5.12-beta.3. v2026.5.10 stable is expected before end of this week. Josh's version gap remains **13 releases** (2026.3.22 → 2026.5.7).

---

### v2026.5.7 Deep Changelog Review — Gemini 3 Tool-Call Fixes Apply to Josh Directly

Evening review of the full v2026.5.7 changelog surfaces items not highlighted this morning that directly affect Josh's instance (running `google/gemini-3-flash-preview` as primary):

**Gemini 3 thought-signature replay fix:** v2026.5.7 specifically fixes "preserve Gemini 3 tool-call thought-signature replay with fallback signatures." Josh's primary model is `google/gemini-3-flash-preview`. Tool-call reliability (calendar reads, email drafts, iMessage operations) improves after this update. The morning scan noted this was patched; evening review confirms it is specifically Gemini 3 — not a generic model fix.

**Channels CLI changes:** `openclaw channels list` is now channel-only by default; `--all` is required for bundled/catalog channels. This changes how Josh views and manages Discord channel configuration post-update.

**Snake_case tool-call transcript sanitization:** v2026.5.7 repairs snake_case sanitization of tool-call transcripts. Heather's tools (email, calendar, contacts) generate transcripts with snake_case keys. Pre-5.7, these could be malformed in session logs. Post-update, session transcript quality improves — relevant for memory file accuracy.

**Doctor/Codex OAuth preservation:** 2026.5.5 had a regression in OpenAI/Codex OAuth routes that 2026.5.7 fixes. Not directly relevant (Josh uses Google/OpenRouter), but confirms 2026.5.7 is a stability milestone worth taking.

---

### ElevenLabs v3 TTS — Available After Update

AGENTS.md (Josh's workspace) explicitly mentions ElevenLabs TTS (`sag`) for voice storytelling in Discord: *"If you have `sag` (ElevenLabs TTS), use voice for stories, movie summaries, and 'storytime' moments!"*

OpenClaw v2026.5.4 added ElevenLabs v3 coverage alongside Azure Speech, Xiaomi, Inworld, Volcengine, Gradium. After updating to 2026.5.7, `/tts latest`, per-agent voice overrides, and personas become available. No TOOLS.md entry exists for a preferred voice — that gap is documented below (Finding 43).

---

### Auto-Reply Authorization Hooks — Security Improvement for Email Dispatch

v2026.5.7 introduces: *"Auto-reply gates inline skill tool dispatch through before-tool-call authorization hooks."*

For Heather, this is relevant to email or iMessage send actions triggered via auto-reply or heartbeat-initiated tasks. When Heather runs a cron or heartbeat that might queue an email, the before-tool-call hook now intercepts the tool dispatch — a security gate before external actions fire. This enforces SOUL.md's own principle: "Be careful with external actions... be bold with internal ones."

**Status:** Not yet relevant (no active cron/heartbeat). Becomes important once heartbeat email checks are wired up post-update.

---

### Community Intelligence — Hermes Agent Multi-Level Memory Architecture

Research on AI Discord bot memory improvements surfaces a relevant architecture pattern from Hermes Agent (Nous Research):

**Multi-level memory:**
- Short-term: Recent conversation turns (session context)
- Episodic: Specific events logged to daily memory files
- Procedural: Distilled skill documents for recurring task patterns ("how Heather successfully resolved a calendar conflict Josh raised")

**Learning loop:** Review completed tasks → distill successful procedures → reusable skill documents. Applied to Heather: after email drafting patterns are established, the successful approach gets written down, not just remembered.

**Relevance to Heather:** Current architecture (daily files + MEMORY.md) maps to episodic memory only. No procedural memory exists — no documentation of "how Heather handles [specific Josh task type]." As memory accumulates, the next evolution is procedural documentation in AGENTS.md or dedicated skill files. This requires memory bootstrap to happen first (Finding 39, morning).

**Risk level:** LOW (future direction). Blocked by the still-empty memory directory.

---

## Codebase Analysis — Evening Deep Dive

### workspace/memory — Confirmed State

The memory directory contains exactly two files:
- `inbox-state.json` (243 bytes) — confirmed malformed/stale, too small for a populated state file
- `onboarding-google.md` (1,191 bytes) — Google onboarding notes from initial setup

**Confirmed: zero daily memory logs, no MEMORY.md, no heartbeat-state.json.**

AGENTS.md specifies `memory/heartbeat-state.json` for tracking check timestamps (`email`, `calendar`, `weather`). This file does not exist. Heather has no heartbeat state tracking — every heartbeat starts without knowing when email, calendar, or weather was last checked.

---

### TOOLS.md — Zero Operational Content After 27 Days (Confirmed)

TOOLS.md (860 bytes) is confirmed pure boilerplate after 27 days of active tool use. No Google account notes, no email tool preferences, no iMessage setup notes despite a known monitoring failure, no TTS voice preference despite AGENTS.md explicitly saying to document this. AGENTS.md instructs: *"Keep local notes... in TOOLS.md."* This instruction has never been followed.

---

### AGENTS.md — Identical to Noah's (No Instance Customization)

Josh's AGENTS.md has SHA `3faead9716a2c168df79c2fba558bd04cd8c76d0` — identical to Noah's AGENTS.md. Both repos share the same unmodified template after 27 days. All Josh-specific preferences live only in USER.md, which has security restrictions on when it's loaded. Critical preferences (no-emoji rule) are not in the always-loaded AGENTS.md or SOUL.md.

---

### HEARTBEAT.md — 168 Bytes, Effectively Empty

Heather's HEARTBEAT.md is 168 bytes. The AGENTS.md template for heartbeat-state.json JSON alone is longer than that. The file exists but contains no actionable checklist — confirmed across all prior scans and now by file size.

---

## New Findings — Josh Instance (Day 27 Evening)

### Finding 41. Gemini 3 Tool-Call Fixes Are Version-Gated — Josh Misses Them
**Risk: MEDIUM | Days pending: NEW**

v2026.5.7 specifically patches Gemini 3 tool-call thought-signature replay. Josh's primary model is `google/gemini-3-flash-preview`. Every tool call Heather makes (email read, calendar check, contact lookup, iMessage status) runs on Gemini 3. The reliability improvement is model-specific and version-gated — only available after updating from 2026.3.22 to 2026.5.7.

**Combined impact:** Gemini 3 tool-call fix + snake_case transcript sanitization + startup lazy-loading = meaningfully more reliable Heather post-update. No config changes required — this is pure update benefit. The 13-release version gap translates directly to 13 rounds of stability improvements Josh is not receiving.

**Risk level:** MEDIUM (continued tool-call unreliability on Gemini 3 until update).

---

### Finding 42. ElevenLabs v3 TTS Available Post-Update — No Voice Preference Set
**Risk: LOW | Days pending: NEW**

ElevenLabs v3 TTS support is available in 2026.5.4+. AGENTS.md explicitly guides Heather to use TTS for storytelling and summaries in Discord. However:
1. No TTS voice preference is documented in TOOLS.md
2. AGENTS.md says to note voice preference in TOOLS.md — never done
3. Post-update, `/tts latest` and per-agent voice overrides are available

**Recommended TOOLS.md addition (post-update):**
```
### TTS
- Preferred voice: [TBD — ask Josh to choose from ElevenLabs v3 list]
- Use for: Discord storytime, movie summaries, long-form reads
```
**Risk level:** LOW — additive capability, no downside.

---

### Finding 43. TOOLS.md Has No Operational Notes — Heather Re-Discovers Setup Every Session
**Risk: MEDIUM | Days pending: 27 (evening confirmed)**

TOOLS.md is a pure boilerplate template after 27 days of active tool use. There are no notes about: which Google account is connected, email tool preferences, iMessage setup state (what went wrong around April 26?), contact management patterns Josh prefers, or TTS voice preference.

Every session, Heather starts from zero on tool context. TOOLS.md is designed to solve exactly this problem.

**Zero-config fix (Discord message):**
```
Update TOOLS.md with everything you know about your tools: which Google account is 
connected, how your email is set up, what happened with iMessage setup, any preferences 
Josh has mentioned about how to handle his tools. This is your cheat sheet — write it now.
```
**Risk level:** MEDIUM — each empty session costs time re-establishing tool context.

---

### Finding 44. Auto-Reply Authorization Hooks — Email Security Gate Available Post-Update
**Risk: LOW (opportunity) | Days pending: NEW**

v2026.5.7 gates inline skill tool dispatch through before-tool-call authorization hooks. For Heather's email and iMessage tools, this enables a security layer: any automated email send (from cron, heartbeat, or auto-reply) can be intercepted before dispatch. Currently, no active cron/heartbeat is dispatching emails, so immediate impact is LOW. Once heartbeat checks are active (post-update priority), this gate prevents accidental sends.

**Risk level:** LOW — positive capability unlocked post-update.

---

## Persistent High-Priority Items — Day 27 Evening Summary

**Version gap: 2026.3.22 → 2026.5.7 = 13 releases, 84 days.**  
**27 consecutive days with zero implementations.**  
**workspace/memory: 2 files, no daily logs, no MEMORY.md, no heartbeat-state.json.**  
**TOOLS.md: confirmed empty template — 27 days.**  
**HEARTBEAT.md: 168 bytes — effectively empty.**

| Finding | Severity | Days Pending | Status |
|---|---|---|---|
| OpenClaw 13 releases outdated | HIGH | 27 | ⬜ Pending |
| memory-core plugin missing entirely | HIGH | 3 | ⬜ Pending |
| workspace/memory empty — no daily logs | HIGH | 27 | ⬜ Pending |
| iMessage monitoring dark (~April 26) | HIGH | 19 | ⬜ Pending |
| HEARTBEAT.md effectively empty (168 bytes) | HIGH | 27 | ⬜ Pending |
| Pre-compaction flush not configured | MEDIUM | 3 | ⬜ Pending |
| MEMORY.md never created | MEDIUM | 27 | ⬜ Pending |
| SOUL.md never evolved | MEDIUM | 27 | ⬜ Pending |
| No-emoji rule not in SOUL.md | MEDIUM | 27 | ⬜ Pending |
| TOOLS.md empty template (27 days confirmed) | MEDIUM | 27 | ⬜ Pending |
| inbox-state.json malformed + stale (243 bytes) | MEDIUM | 27 | ⬜ Pending |
| AGENTS.md not customized (matches Noah's) | MEDIUM | 27 (confirmed) | ⬜ Pending |
| Retired claude-3.5-haiku fallback | LOW | 10 | ⬜ Pending |
| Discord streaming off | LOW | 27 | ⬜ Pending |
| IDENTITY.md avatar blank | LOW | 3 | ⬜ Pending |
| Gemini 3 tool-call fixes — version-gated | MEDIUM | NEW | ⬜ Pending |
| ElevenLabs v3 TTS — no voice preference set | LOW | NEW | ⬜ Post-update |
| Auto-reply authorization hooks | LOW | NEW | ⬜ Post-update |
| Config loop bootstrap — do today | INFO | 1 day | ⬜ Zero-config action |
| threadBindings — multi-agent Discord | MEDIUM | 3 | ⬜ Post-update |
| Retry-aware cron | MEDIUM | 3 | ⬜ Post-heartbeat |
| workspace/reports/ missing | LOW | 1 day | ⬜ Pending |
| memory-lancedb-pro upgrade path | LOW | 1 day | ⬜ Post-memory-core |
| Hermes multi-level / procedural memory | LOW | NEW | ⬜ Post-memory-bootstrap |
| v2026.5.10 stable — monitor | OPPORTUNITY | monitoring | ⬜ Expected this week |
| /context map — token visibility | OPPORTUNITY | 3 | ⬜ Post-5.10 |
| A2A 20-turn sub-agent delegation | OPPORTUNITY | 3 | ⬜ Post-5.10 |
| Per-agent tool overrides (Discord) | OPPORTUNITY | 3 | ⬜ Post-5.10 |

**Updated implementation order:**
1. **TODAY — Zero config:** Send memory bootstrap message to Heather in Discord (Finding 39, morning)
2. **TODAY — Zero config:** Send TOOLS.md populate message (Finding 43)
3. Update OpenClaw to 2026.5.7 — unlocks Gemini 3 fixes, ElevenLabs v3, auth hooks
4. Add memory-core to `plugins.allow` + `plugins.entries` + compaction config
5. Populate HEARTBEAT.md with email/calendar/iMessage check schedule
6. Tell Heather to create `workspace/reports/` directory
7. Fix stale claude-3.5-haiku fallback → `openrouter/anthropic/claude-haiku-4-5`
8. Enable threadBindings, add cron retry config
9. Monitor for 2026.5.10 stable → evaluate memory-lancedb-pro

---
*Generated by AlphaClaw Apex Fleet Research Agent — Evening Scan — 2026-05-14*
