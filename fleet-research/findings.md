# Fleet Research — Josh / Heather Schwartz — Morning Scan

**Scan Date:** 2026-05-05 (Morning)  
**Agent:** AlphaClaw Apex Fleet Research Agent  
**Instance:** Josh — Discord bot Heather Schwartz (personal assistant: iMessage, email, calendar, contacts)

---

## Platform News (New This Morning)

### OpenClaw 2026.5.3 — Released Today
- **Bundled file-transfer plugin**: `file_fetch`, `dir_list`, `dir_fetch`, `file_write` tools for binary file ops on paired nodes. Default-deny per-node path policy, symlink traversal refused by default, 16 MB ceiling per round-trip. Future use: Heather accessing files on Josh's machine via paired node once configured.
- **New `/side` command**: Quiet context-aware aside to agent without breaking the main thread. Good for mid-session "remember to follow up on X" instructions.
- **Cron persistence fix**: `jobs-state.json` now correctly persists repaired startup state — a valid future `nextRunAtMs` with missing `updatedAtMs` no longer triggers repeated health-check repairs after Gateway restart. Critical once Josh adds cron jobs.
- **Plugin install/update hardening**: Externalized plugins now behave like first-class package installs. Direct benefit for the pending memory-lancedb + active-memory setup.
- **Reset interrupt fix**: `/new` and `/reset` now treated as interrupt runs — steer/followup modes can't delay fresh sessions behind existing work.

### Security: Update Now Has a Security Dimension
- 138+ OpenClaw CVEs tracked in 2026. April 2026 patch batch: 13 patched (2 Critical).
- **CVE-2026-32922** (CVSS 9.9, auth bypass via race condition): Fixed in 2026.3.11. Josh is on 2026.3.22 — safe from this specific CVE.
- **CVE-2026-25253** (RCE, CVSS 8.8): Patched in 2026.4.x builds. Josh on 2026.3.22 is potentially exposed.
- **CVE-2026-35639/35641**: Remotely exploitable with no credentials required. Patch status for 2026.3.22 unclear.
- Mitigating factor: Josh's gateway uses `bind: loopback` + `trustedProxies: ["127.0.0.1"]` — not internet-exposed.
- **New threshold**: Updating is now a security requirement, not just a feature upgrade.

### threadBindings.spawnSessions — New Unified Config
- Replaces the legacy split subagent/ACP thread-spawn toggles (introduced in 2026.5.2).
- `channels.discord.threadBindings.spawnSessions` (defaults `true`).
- After update: run `openclaw doctor --fix` to auto-migrate any legacy keys.
- Future value: Discord thread-per-subagent workspaces for coordinated multi-agent tasks.

### Gemini Model Landscape — Josh's Primary Validated
- **Gemini 3 Flash Preview** (Josh's current primary): Community confirms 78% SWE-bench Verified — outperforms Gemini 3 Pro at the same benchmark. 1M context window, 66K output limit. Model choice is sound.
- **Gemini 3.1 Flash Lite** now available on OpenRouter — faster, lower cost. Could be added as a budget supplemental fallback for heartbeat sub-tasks, not as a replacement.

---

## Implementation Status — Day 15, Zero Changes

Fifteen consecutive daily scans. No config changes have been implemented on Josh's instance.

| Action | Status | Est. Time |
|--------|--------|-----------|
| Add `tools.exec.security: "full"` to openclaw.json | ⬜ Pending | 2 min |
| `openclaw config validate` | ⬜ Pending | 1 min |
| `alphaclawctl update` → 2026.5.3 | ⬜ Pending | 5 min |
| `openclaw doctor --fix` (migrate threadBindings) | ⬜ Pending | 1 min |
| Install memory-lancedb + enable active-memory | ⬜ Pending | 20 min |
| Create MEMORY.md (seed content) | ⬜ Pending | 15 min |
| Populate HEARTBEAT.md | ⬜ Pending | 15 min |
| Enable Discord streaming | ⬜ Pending | 2 min |
| Investigate iMessage monitoring pause | ⬜ Pending | 10 min |

**Total: ~71 minutes to address all critical pending items.**

**Lowest-friction first step:** Add `tools.exec.security: "full"` to openclaw.json (2 min), run `openclaw config validate`, then `alphaclawctl update`. Security improvement + full feature catch-up in under 10 minutes.

---

*Full findings detail continues below (Evening Scan).*

---

# Fleet Research — Josh / Heather Schwartz — Evening Scan

**Scan Date:** 2026-05-05 (Evening)  
**Agent:** AlphaClaw Apex Fleet Research Agent  
**Instance:** Josh — Discord bot Heather Schwartz (personal assistant: iMessage, email, calendar, contacts)  
**Previous findings:** See git history for morning scan.

---

## Platform News (Since Morning Scan)

### OpenClaw 2026.5.3 (Released Today)
- **Plugin reliability hardened**: npm package support fixed, smarter fallback on partial install failures, auto-repair logic detects and fixes broken plugin states without manual intervention.
- **New `/side` command**: Like `/btw` — sends a quiet, context-aware aside to the agent without breaking the main conversation thread. Useful for background instructions.
- **WhatsApp newsletter/channel outbound fixed**: Relevant if Josh ever adds WhatsApp.
- Source: [buildfastwithai.com](https://www.buildfastwithai.com/blogs/openclaw-2026-5-2-release)

### OpenClaw 2026.5.2 (May 3, 2026)
- **`/goal` command via Codex**: Describe a high-level multi-step objective; OpenClaw executes it autonomously across multiple steps without human hand-holding at each step.
- **Grok 4.3 bundled**: xAI's Grok 4.3 is now in the model catalog. Josh uses OpenRouter fallbacks which could include Grok.
- **Gemini search grounding improvements**: Freshness/date filters now passed through — more accurate results for time-sensitive queries.
- **DuckDuckGo added** to keyless search path.

### OpenClaw 2026.4.12 (Earlier this month)
- **Active Memory plugin**: A dedicated memory agent now runs before each session, proactively maintaining memory state. This is a significant upgrade over purely file-based memory.
- Source: [@iamlukethedev on X](https://x.com/iamlukethedev/status/2043675662188199962)

### AlphaClaw Community / Infrastructure
- **Durable plugin state directory fix**: `OPENCLAW_STATE_DIR` now exports `/data/.openclaw` so plugins write durable artifacts instead of ephemeral temp paths. Prevents state loss on restart.
- **Docker self-update fix**: EBUSY rename error resolved using temp-dir + cp install pattern.
- Source: [github.com/chrysb/alphaclaw](https://github.com/chrysb/alphaclaw)

---

## Critical Findings — Josh Instance

### 1. OpenClaw Version Severely Outdated
**Risk: HIGH**

`openclaw.json` shows `lastTouchedVersion: 2026.3.22`. Current stable release is **2026.5.3**. That is approximately **6 weeks and 8+ releases behind**.

**Why it matters:** Missing the Active Memory plugin (4.12), `/goal` autonomous execution (5.2), `/side` command (5.3), plugin reliability fixes (5.2/5.3), Gemini search improvements, and Grok 4.3 availability. The gap is large enough that there may also be bug fixes and security patches being missed.

**Fix:** In AlphaClaw UI (`https://5.78.142.81.sslip.io#general`), apply the pending OpenClaw update. Review in-app changelog before applying.

---

### 2. iMessage Monitoring Is Paused
**Risk: HIGH**

`workspace/memory/inbox-state.json` contains:
```json
"imessage_monitoring_paused": true
```

iMessage is a core stated function of this instance (personal assistant). If monitoring is paused, Heather is not watching incoming messages, cannot surface urgent iMessages to Josh, and any iMessage-triggered workflows are silently dead.

**Fix:** Resume iMessage monitoring. Investigate why it was paused (crash? API issue? intentional?). If intentional, document the reason in HEARTBEAT.md or USER.md.

---

### 3. inbox-state.json Has Duplicate Key (Malformed JSON)
**Risk: MEDIUM**

`workspace/memory/inbox-state.json` has `last_email_check_ms` defined twice:
```json
{
  "last_email_check_ms": 1777087800000,
  ...
  "last_email_check_ms": 1777551900000
}
```

JSON parsers are inconsistent with duplicate keys — some take the first value, some take the last. This could cause email check state to be read incorrectly, leading to missed emails or redundant re-processing.

**Fix:** Deduplicate the key, keeping `1777551900000` (the most recent timestamp).

---

### 4. HEARTBEAT.md Is Empty — No Proactive Monitoring
**Risk: HIGH**

`workspace/HEARTBEAT.md` contains only comments — no active tasks. This means Heather is completely reactive. She is not:
- Checking Josh's email inbox periodically
- Monitoring upcoming calendar events
- Surfacing urgent iMessages
- Doing any background maintenance

AGENTS.md explicitly describes how to use heartbeats productively and even provides a `heartbeat-state.json` tracking pattern — but none of it is active.

**Fix:** Populate HEARTBEAT.md with a practical checklist. See soul-improvements.md for a recommended starter template.

---

### 5. TOOLS.md Is a Bare Template
**Risk: MEDIUM**

`workspace/TOOLS.md` contains only the example scaffolding — no actual entries for Josh's environment. For a personal assistant with access to iMessage, email, calendar, and contacts, there should be documented specifics:
- Preferred TTS voice (if using voice features)
- Any SSH or device aliases
- Platform formatting preferences (Josh has a STRICT no-emoji-reactions rule — this is in USER.md but not TOOLS.md where it would also be appropriate)

**Fix:** Populate TOOLS.md with Josh-specific environment details as they accumulate.

---

### 6. No Long-Term MEMORY.md Exists
**Risk: MEDIUM**

AGENTS.md defines `MEMORY.md` as the "curated long-term memory" that should be read in main sessions. This file does not exist in the workspace root. The memory directory contains only `inbox-state.json` and `onboarding-google.md` — there are no daily `YYYY-MM-DD.md` session logs visible either.

This means Heather has no curated long-term memory. Every main session starts cold with only what's in USER.md and IDENTITY.md.

**Fix:** Create `workspace/MEMORY.md` with an initial entry covering what's known about Josh, key preferences, and any decisions made to date. Heather should maintain this going forward.

---

### 7. Google Workspace Not Reflected in Runtime TOOLS.md
**Risk: LOW-MEDIUM**

`workspace/hooks/bootstrap/TOOLS.md` (the file injected at every session) states:
> "No Google accounts are currently configured."

However, `workspace/memory/onboarding-google.md` confirms Josh successfully completed Google Workspace onboarding on 2026-03-21 with Gmail, Calendar, Drive, Sheets, Docs, Tasks, Contacts APIs enabled.

This creates a contradiction: the file Heather reads every session says no Google is configured, but the memory suggests it was set up. This could cause Heather to incorrectly tell Josh that Google Workspace isn't available.

**Fix:** After confirming the Google connection is live in AlphaClaw UI, the bootstrap TOOLS.md should be regenerated (usually done automatically by AlphaClaw on reconnect). Check `https://5.78.142.81.sslip.io#general` to verify the Google account shows as connected.

---

### 8. Discord Streaming Is Disabled
**Risk: LOW**

`openclaw.json` has `"streaming": "off"` in the Discord channel config. With streaming off, Josh sees no typing indicator and the entire response appears at once after processing. For a personal assistant, streaming provides a much more natural conversational feel.

**Fix:** Consider setting `"streaming": "on"` — this can be done via AlphaClaw UI or the openclaw.json directly.

---

### 9. No Compaction / Memory Flush Configuration
**Risk: LOW-MEDIUM**

Unlike Noah's instance, Josh's `openclaw.json` has no `compaction` config. For long personal assistant sessions (email triage, scheduling, research), context can grow large and expensive. Without memory flush, context will hit limits abruptly.

**Fix:** Add compaction config:
```json
"compaction": {
  "reserveTokensFloor": 20000,
  "memoryFlush": {
    "enabled": true,
    "softThresholdTokens": 3000
  }
}
```

---

### 10. Active Memory Plugin Not Installed (New in 2026.4.12)
**Risk: MEDIUM**

The Active Memory plugin (released 2026.4.12) runs a dedicated memory agent before each session to proactively maintain memory state. This is a significant improvement over pure file-based memory. Josh's instance is not running it.

**Fix:** Add `memory-core` to `plugins.allow` and `plugins.entries` in openclaw.json, then restart the gateway.

---

## Opportunity Findings

### A. `/goal` Command for Autonomous Multi-Step Tasks
With 2026.5.2, Heather can now execute high-level tasks autonomously. Josh could say "draft and send my weekly update email" and Heather could plan and execute the entire flow (read inbox context → draft → confirm → send) without step-by-step prompting.

### B. `/side` Command for Quiet Background Instructions
The new `/side` command lets Josh give Heather quiet instructions without breaking the main thread. Good for things like "remember to check on my Bliss brand meeting tomorrow" mid-conversation.

### C. Heartbeat-Triggered Morning Briefing
With a populated HEARTBEAT.md, Heather could deliver a morning briefing (email summary + calendar overview) aligned with Josh's LA timezone (08:00 PDT). This matches patterns described in the community "MoltBot/OpenClaw personal assistant" space.

---

## Summary Table

| Finding | Severity | Effort | Action |
|---|---|---|---|
| OpenClaw 6+ weeks outdated | HIGH | Low | Update via AlphaClaw UI |
| iMessage monitoring paused | HIGH | Low | Resume + investigate root cause |
| Duplicate key in inbox-state.json | MEDIUM | Low | Fix JSON file |
| HEARTBEAT.md empty | HIGH | Low | Populate with monitoring tasks |
| TOOLS.md template only | MEDIUM | Low | Add Josh-specific entries |
| MEMORY.md missing | MEDIUM | Low | Create with initial content |
| Google account not in bootstrap TOOLS | MEDIUM | Low | Verify connection in UI |
| Streaming off | LOW | Low | Toggle in openclaw.json |
| No compaction config | LOW | Low | Add JSON block |
| Active Memory plugin not installed | MEDIUM | Low | Add to plugins config |

---
*Generated by AlphaClaw Apex Fleet Research Agent — Morning + Evening Scan — 2026-05-05*
