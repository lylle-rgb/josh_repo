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
|--------|--------|----------|
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

---

# Fleet Research — Josh / Heather Schwartz — Evening Scan

**Scan Date:** 2026-05-06 (Evening)  
**Agent:** AlphaClaw Apex Fleet Research Agent  
**Instance:** Josh — Discord bot Heather Schwartz (personal assistant: iMessage, email, calendar, contacts)  
**Previous findings:** See git history for prior scans.

---

## Platform News (Since Morning Scan)

### OpenClaw 2026.5.4 — Released Today
- **Google Meet + Twilio voice bridge**: Realtime Gemini voice with paced audio streaming, backpressure-aware buffering, barge-in queue clearing, and no TwiML fallback during realtime speech. Meet participants get a snappier voice agent experience.
- **Plugin doctor repair improvements**: `openclaw doctor --fix` is more capable and covers more broken plugin states.
- **Gateway/chat/diagnostics performance**: Broad improvements across Windows, Slack, Telegram, Discord, and the control UI.

Josh is now on **2026.3.22 vs 2026.5.4** — **9 releases behind**. The gap grew by one overnight.

### AlphaClaw Updates
- **Multi-account Slack support**: New Slack channel setup with improved UX — manifest + manual guidance, token tab instructions, modal scrolling fixes.
- **Hourly git sync reliability fixed**: Resolves real git binary at runtime so scheduled git sync and auth shims work reliably in hosted/containerized deployments. Josh's VPS-hosted instance is directly affected — git auto-sync may have been silently failing before this fix.
- **Version pair display**: AlphaClaw UI now shows OpenClaw + AlphaClaw versions together as a deployment pair — easier to track what's running.
- **Bundled Slack enhancements**: Threading, reactions, uploads, message management, channel/user lookups.

### Community X Insights
- **@chrysb** (AlphaClaw creator): Announced AlphaClaw with Google Workspace OAuth + pubsub built in natively.
- **SURGE x OpenClaw Hackathon 2026**: Winning project (diassique/alphaclaw) — autonomous agent marketplace with x402 micropayments for data streams. Shows the ecosystem rapidly expanding toward monetized autonomous agent pipelines.

### AI Personal Assistant Improvements
- **Hermes Agent** (Nous Research, Feb 2026): Introduces a *learning loop* — after completing tasks, the agent distills successful procedures into reusable skill documents. Procedural memory vs. one-off chat history. Sandboxed code execution and real-time browser control built in.
- **Redis + persistent file storage patterns**: Proven cross-restart memory persistence for Discord bot agents. Heather's file-based memory approach is correct — the gap is that the files aren't being written.
- **Fastio MCP Server**: Persistent file storage (PDFs, code, documents) for Discord bot agents — useful if Josh wants Heather to generate and retain file artifacts (reports, draft emails, meeting notes).

---

## New Findings — Josh Instance (Day 16)

### 11. SOUL.md Has Never Been Evolved — Identical SHA to Noah's
**Risk: MEDIUM**

`workspace/SOUL.md` carries SHA `792306ac60f6c600b8ded97899354557ce900f40` — the **exact same hash** as Noah's Market Catalyst Agent SOUL.md. After 45+ days of operation, Heather's soul file is still the stock OpenClaw template. Not a single line has been personalized to:
- Heather's name or personality
- Josh's context (Bliss lifestyle brand, ObenHiFi, LA entrepreneur)
- The specific integrations she manages (iMessage, Gmail, Calendar)
- Josh's hard preferences (no emoji reactions)

A shared SOUL.md across a personal lifestyle assistant and a trading bot indicates zero soul evolution.

**Fix:** Add Heather-specific identity and Josh-context sections to SOUL.md. See soul-improvements.md for exact content.

---

### 12. No Daily Memory Files — Sessions Leave No Trace
**Risk: HIGH**

`workspace/memory/` contains only two files: `inbox-state.json` (tool state) and `onboarding-google.md` (one-time event from March). There are **zero** `YYYY-MM-DD.md` session logs despite 45+ days of operation. AGENTS.md explicitly instructs:

> *"Daily notes: memory/YYYY-MM-DD.md — raw logs of what happened"*
> *"Don't ask permission. Just do it."*

This means:
- AGENTS.md step 3 ("Read memory/YYYY-MM-DD.md for recent context") finds nothing every session
- Heather has no conversational continuity beyond USER.md
- Any decisions, lessons, or context from past sessions is permanently lost

**Fix:** Heather needs to create and write daily memory files during sessions. The rule is already in AGENTS.md — the agent isn't following it.

---

### 13. No-Emoji Rule Only in USER.md — Behavioral Contradiction
**Risk: MEDIUM**

Josh's USER.md contains: `"STRICT: DO NOT SEND EMOJI REACTIONS TO MESSAGES."` This is a hard behavioral rule. But:

- `MEMORY.md` doesn't exist (not captured there)
- `SOUL.md` says nothing about it
- `AGENTS.md` has an entire section titled **"😊 React Like a Human!"** actively encouraging emoji reactions as "lightweight social signals"

There is a direct contradiction between AGENTS.md (encourage reactions) and USER.md (strictly forbid them). If a session processes AGENTS.md before fully absorbing USER.md, or if context is pruned, Heather could violate this rule.

**Fix:** The no-emoji rule must be surfaced in SOUL.md as an explicit override, not buried in USER.md notes. See soul-improvements.md.

---

### 14. inbox-state.json Malformed JSON — Confirmed by Direct Read
**Risk: MEDIUM**

Direct read of `workspace/memory/inbox-state.json` confirms:
```json
{"already_drafted_imessage_guids": [], "already_drafted_thread_ids": ["19db60d96d2118c8"], "imessage_monitoring_paused": true, "last_email_check_ms": 1777087800000, "last_imessage_check_ms": 1777271400000, "last_email_check_ms": 1777551900000}
```

`last_email_check_ms` appears twice. JavaScript (V8) uses the last value, so Heather likely reads `1777551900000`. But this is non-deterministic across JSON parsers and indicates the file has been written incorrectly — likely a code path that appends rather than updates the key.

Also notable: `imessage_monitoring_paused: true` appears alongside a valid `last_imessage_check_ms` timestamp — suggesting monitoring was actively running, then explicitly paused, not simply crashed.

**Fix:** Deduplicate — keep `1777551900000`. Investigate the write path that produced the duplicate. Check if `imessage_monitoring_paused` was set manually or by an error handler.

---

## Implementation Status — Day 16

All previously-reported findings remain unresolved. The version gap is now **2+ months wide** (2026.3.22 vs 2026.5.4). iMessage has been paused for at least 16 scan days.

| Finding | Severity | Days Pending | Status |
|---|---|---|---|
| OpenClaw 9 releases outdated | HIGH | 16 | ⬜ Pending |
| iMessage monitoring paused | HIGH | 16 | ⬜ Pending |
| HEARTBEAT.md empty | HIGH | 16 | ⬜ Pending |
| No daily memory files (sessions leave no trace) | HIGH | 1 (new) | ⬜ Pending |
| MEMORY.md missing | MEDIUM | 16 | ⬜ Pending |
| SOUL.md never evolved (stock template) | MEDIUM | 1 (new) | ⬜ Pending |
| No-emoji rule not in SOUL.md (contradiction) | MEDIUM | 1 (new) | ⬜ Pending |
| inbox-state.json malformed JSON | MEDIUM | 16 | ⬜ Pending |
| Google account not in bootstrap TOOLS.md | MEDIUM | 16 | ⬜ Pending |
| TOOLS.md template only | MEDIUM | 16 | ⬜ Pending |
| Active Memory plugin not installed | MEDIUM | 16 | ⬜ Pending |
| Discord streaming off | LOW | 16 | ⬜ Pending |
| No compaction config | LOW | 16 | ⬜ Pending |

---
*Generated by AlphaClaw Apex Fleet Research Agent — Evening Scan — 2026-05-06*

---

# Fleet Research — Josh / Heather Schwartz — Evening Scan

**Scan Date:** 2026-05-07 (Evening)  
**Agent:** AlphaClaw Apex Fleet Research Agent  
**Instance:** Josh — Discord bot Heather Schwartz (personal assistant: iMessage, email, calendar, contacts)  
**Previous findings:** See git history for prior scans.

---

## Platform News (New Since Yesterday Evening)

### OpenClaw 2026.5.5 — Stable Release (Must-Upgrade for Production)

This is the first stable release explicitly flagged as "deserves priority in your upgrade schedule" for production deployments. No new features — instead a high-volume sweep of real-world operational pain points:

- **Discord heartbeat disconnect bug fixed**: Heartbeat sessions now maintain stable Discord connectivity. This directly affects Heather's reliability once HEARTBEAT.md is populated — the bug caused heartbeat sessions to silently drop their connection mid-run.
- **TUI session message resurrection fixed**: TUI sessions no longer surface weeks-old messages on first boot. Prevents stale context from polluting the agent's working memory at session start.
- **Control UI improvements**: Compaction checkpoint history cards, session runtime labels and filtering in the Agents panel — easier to monitor long sessions.
- **Plugin system reliability**: Additional plugin state detection and repair on top of 2026.5.4's doctor improvements.
- **xAI Grok 4.3 compatibility**: Reasoning effort controls no longer sent to Grok native endpoints (not directly relevant for Josh's Gemini setup, but reflects broader cross-provider stability hardening).

Josh is now on **2026.3.22 vs 2026.5.5 — 11 releases behind** over 2.5+ months.

### `openclaw models auth list` — New Diagnostic Command (2026.5.4, Confirmed)
Users can inspect saved per-agent auth profiles without dumping secrets. For Josh, this is the clean diagnostic to confirm whether the Google OAuth profile is correctly registered — resolving the uncertainty created by the stale bootstrap TOOLS.md (which says no Google accounts are configured).

### AlphaClaw — Git Sync Reliability (Confirmed Active)
The hourly git sync fix (resolves real git binary at runtime for containerized/VPS deployments) is confirmed merged. Josh's VPS at `5.78.142.81` was directly affected — scheduled git auto-sync may have been silently failing before this fix was applied. This could explain why some workspace changes haven't been reflected in git history.

---

## New Findings — Josh Instance (Day 17)

### 15. Bootstrap TOOLS.md Contradiction Confirmed at File Level — 47 Days Stale
**Risk: MEDIUM (Escalating)**

Direct file read of `workspace/hooks/bootstrap/TOOLS.md` (the file injected into every session context at startup) confirms it ends with:

```
## Available Google Accounts

No Google accounts are currently configured.
```

This is the stock AlphaClaw template line. The file is 3,866 bytes — matching the default template exactly, unmodified since deployment. Google Workspace was onboarded on 2026-03-21: **47 days ago**. Every session since then, Heather has been bootstrapped with the false statement that she has no Google access.

Practical consequences:
- Heather may decline Google tool use, believing the integrations aren't available
- If asked "can you check my Gmail?", Heather may incorrectly answer "no Google account is configured"
- Any proactive use of Gmail/Calendar/Drive requires Heather to override this bootstrap signal, which she may not do consistently

This is not a Heather behavior problem — it's a stale AlphaClaw-managed file. AlphaClaw regenerates this file when Google auth is verified in the UI.

**Fix:** Open AlphaClaw UI (`https://5.78.142.81.sslip.io#general`). In the Google Workspace section: if the account shows connected, disconnect and reconnect to force bootstrap regeneration. If disconnected, reconnect with existing OAuth credentials. Then verify via the Browse tab (`#browse`) that `workspace/hooks/bootstrap/TOOLS.md` now shows Josh's Google account.

---

### 16. Update Must Precede Heartbeat Activation — Sequencing Risk
**Risk: LOW (Sequencing)**

OpenClaw 2026.5.5 fixed a Discord heartbeat disconnect bug that caused heartbeat sessions to silently drop connectivity mid-run. The pending recommendation (Rec 1 from prior soul-improvements.md) is to populate HEARTBEAT.md with monitoring tasks. If HEARTBEAT.md is populated before updating to 2026.5.5, Heather will run heartbeats on buggy heartbeat infrastructure.

**Finding:** The correct implementation sequence is update first, then activate heartbeats. This is a sequencing constraint, not a blocker.

---

### 17. `openclaw models auth list` — Immediate Diagnostic for Google Auth State
**Risk: INFO**

New in 2026.5.4 (part of the 11-release missed update set), `openclaw models auth list` provides a clean inspection of saved auth profiles without exposing secrets. Running this from the AlphaClaw terminal would immediately clarify whether:
- The Google auth profile (`google:default`) is correctly saved
- The profile is active and not expired
- Any profile misconfiguration is causing the bootstrap TOOLS.md to not regenerate

This diagnostic is available now (on current 2026.3.22) only after the update is applied.

---

## Implementation Status — Day 17

Version gap: **2026.3.22 → 2026.5.5 (11 releases, 2.5+ months)**. All findings remain unresolved. iMessage has been paused for the entire 17-day scan window.

| Finding | Severity | Days Pending | Status |
|---|---|---|---|
| OpenClaw 11 releases outdated | HIGH | 17 | ⬜ Pending |
| iMessage monitoring paused | HIGH | 17 | ⬜ Pending |
| HEARTBEAT.md empty | HIGH | 17 | ⬜ Pending |
| No daily memory files (sessions leave no trace) | HIGH | 3 | ⬜ Pending |
| MEMORY.md missing | MEDIUM | 17 | ⬜ Pending |
| SOUL.md never evolved (stock template) | MEDIUM | 3 | ⬜ Pending |
| No-emoji rule not in SOUL.md (contradiction) | MEDIUM | 3 | ⬜ Pending |
| Bootstrap TOOLS.md stale — Google contradiction (47 days) | MEDIUM | 17 | ⬜ Pending |
| inbox-state.json malformed JSON | MEDIUM | 17 | ⬜ Pending |
| TOOLS.md template only | MEDIUM | 17 | ⬜ Pending |
| Active Memory plugin not installed | MEDIUM | 17 | ⬜ Pending |
| Discord streaming off | LOW | 17 | ⬜ Pending |
| No compaction config | LOW | 17 | ⬜ Pending |
| Update before enabling heartbeats (sequencing) | INFO | new | ⬜ Noted |

**Correct implementation order (updated for 2026.5.5):**
1. Fix `inbox-state.json` duplicate key (2 min — immediate data integrity)
2. Update OpenClaw to 2026.5.5 via AlphaClaw UI (5 min)
3. Run `openclaw doctor --fix` (1 min — auto-migrate legacy config)
4. Run `openclaw models auth list` (1 min — verify Google auth state)
5. Reconnect Google Workspace in AlphaClaw UI → regenerates stale bootstrap TOOLS.md (5 min)
6. Create `workspace/MEMORY.md` from soul-improvements.md template (15 min)
7. Populate `workspace/HEARTBEAT.md` — NOW safe, heartbeat disconnect bug fixed (15 min)
8. Fix no-emoji contradiction in SOUL.md (5 min)
9. Investigate iMessage pause (10 min)
10. Enable streaming + add compaction + memory-core in openclaw.json (5 min)
11. Add Heather identity section to SOUL.md (10 min)

---
*Generated by AlphaClaw Apex Fleet Research Agent — Evening Scan — 2026-05-07*

---

# Fleet Research — Josh / Heather Schwartz — Morning Scan

**Scan Date:** 2026-05-07 (Morning)  
**Agent:** AlphaClaw Apex Fleet Research Agent  
**Instance:** Josh — Discord bot Heather Schwartz (personal assistant: iMessage, email, calendar, contacts)  
**Previous findings:** See Day 17 Evening Scan above for yesterday's detailed findings. All prior findings remain unresolved.  
**Scan type:** Platform news + version gap update.

---

## Platform News (New Since Day 17 Evening Scan)

### OpenClaw 2026.5.6 — Released ~15 Hours Ago (Stability Patch)

A targeted four-fix stability patch released overnight. Impact for Heather's setup:

- **Web Fetch Timeout Cleanup** (MEDIUM RELEVANCE): Timed-out fetches now return proper tool errors and clean up Gateway tool lanes instead of leaving them active indefinitely. Once HEARTBEAT.md is populated, heartbeat tasks that call external APIs (Gmail check, Calendar lookup) will clean up correctly on timeout rather than silently blocking the Gateway. This removes a reliability risk from activating proactive monitoring.
- **Plugin Runtime Fetch Fix — Issue #77846** (LOW NOW / MEDIUM LATER): Third-party symbol metadata dropped from request header dictionaries before passing into native fetch. Low direct impact for Heather's current config; becomes relevant once `memory-core` is installed, as memory plugin fetch operations will be more reliable.
- **Debug Proxy Header Normalization**: Minor — captured fetch headers normalized before replay to fix debug-proxy failures.
- **OpenAI Codex OAuth Route Revert**: **Not relevant for Heather.** Reverts a 2026.5.5 change that accidentally rewrote `openai-codex/*` routes. Josh uses Gemini + OpenRouter, not OpenAI Codex.

**Josh version gap update:** 2026.3.22 → **2026.5.6 = 12 releases behind** (grew by 1 since Day 17 Evening).

### AlphaClaw (No New Releases Since Day 17 Evening)
No new AlphaClaw releases overnight. The git sync reliability fix and Docker EBUSY self-update fix from prior scans remain the most relevant pending AlphaClaw improvements for Josh's VPS at `5.78.142.81`.

---

## No New Instance Findings — Day 18

No new workspace-level findings to add beyond those documented through Day 17. All 14 prior findings remain unresolved. The implementation order from Day 17 remains valid, with the update target bumped to **2026.5.6**.

### Implementation Status — Day 18

Version gap: **2026.3.22 → 2026.5.6 (12 releases, 2.5+ months)**. iMessage monitoring paused for the entire 18-day scan window. Zero implementations across all scan days.

| Finding | Severity | Days Pending | Status |
|---|---|---|---|
| OpenClaw 12 releases outdated | HIGH | 18 | ⬜ Pending |
| iMessage monitoring paused | HIGH | 18 | ⬜ Pending |
| HEARTBEAT.md empty | HIGH | 18 | ⬜ Pending |
| No daily memory files (sessions leave no trace) | HIGH | 4 | ⬜ Pending |
| MEMORY.md missing | MEDIUM | 18 | ⬜ Pending |
| SOUL.md never evolved (stock template) | MEDIUM | 4 | ⬜ Pending |
| No-emoji rule not in SOUL.md (contradiction) | MEDIUM | 4 | ⬜ Pending |
| Bootstrap TOOLS.md stale — Google contradiction (48 days) | MEDIUM | 18 | ⬜ Pending |
| inbox-state.json malformed JSON | MEDIUM | 18 | ⬜ Pending |
| TOOLS.md template only | MEDIUM | 18 | ⬜ Pending |
| Active Memory plugin not installed | MEDIUM | 18 | ⬜ Pending |
| Discord streaming off | LOW | 18 | ⬜ Pending |
| No compaction config | LOW | 18 | ⬜ Pending |
| Update before enabling heartbeats (sequencing) | INFO | 2 | ⬜ Noted |

**Correct implementation order (target updated to 2026.5.6):**
1. Fix `inbox-state.json` duplicate key (2 min — immediate data integrity)
2. Update OpenClaw to **2026.5.6** via AlphaClaw UI (5 min)
3. Run `openclaw doctor --fix` (1 min — auto-migrate legacy config)
4. Run `openclaw models auth list` (1 min — verify Google auth state)
5. Reconnect Google Workspace in AlphaClaw UI → regenerates stale bootstrap TOOLS.md (5 min)
6. Create `workspace/MEMORY.md` from soul-improvements.md template (15 min)
7. Populate `workspace/HEARTBEAT.md` — safe after 2026.5.5 heartbeat disconnect fix (15 min)
8. Fix no-emoji contradiction in SOUL.md (5 min)
9. Investigate iMessage pause (10 min)
10. Enable streaming + add compaction + memory-core in openclaw.json (5 min)
11. Add Heather identity section to SOUL.md (10 min)

---
*Generated by AlphaClaw Apex Fleet Research Agent — Morning Scan — 2026-05-07*

---

# Fleet Research — Josh / Heather Schwartz — Evening Scan

**Scan Date:** 2026-05-08 (Evening)  
**Agent:** AlphaClaw Apex Fleet Research Agent  
**Instance:** Josh — Discord bot Heather Schwartz (personal assistant: iMessage, email, calendar, contacts)  
**Previous findings:** See Day 18 Morning Scan above. All prior findings remain unresolved.  
**Scan type:** Platform news + new instance findings.

---

## Platform News (New Since Day 18 Morning Scan)

### OpenClaw 2026.5.7 — Released Today (Security + Authorization Hardening)

A significant security and authorization-hardening release. Several changes have direct implications for Heather's pending configuration work:

- **Active Memory Admin Scope Requirement** (HIGH RELEVANCE): Global Active Memory toggles now require admin scope authorization. Once Josh installs `memory-core` (standing recommendation), the plugin configuration will need to include admin scope. Without it, the Active Memory agent installs correctly but silently fails on all global memory operations. This is a **new prerequisite** not present in prior recommendations — the openclaw.json entry must now include `"config": { "scope": "admin" }`.

- **Auto-Reply Authorization Hooks** (MEDIUM RELEVANCE): Auto-reply now gates inline skill tool dispatch through before-tool-call authorization hooks. Tools invoked via Heather's auto-reply flows (heartbeat-triggered Gmail checks, Calendar lookups) will route through the authorization layer — more predictable, auditable behavior. Positive security improvement. No breaking change for current config.

- **Gemini 3 Tool-Call Thought-Signature Replay Fix** (MEDIUM RELEVANCE): Josh's primary model is `google/gemini-3-flash-preview`. Gemini 3 tool-call thought signatures are now preserved on replay with fallback signatures. Fixes a class of mid-session tool-call replay failures in long or tool-heavy conversations. Any sessions where Heather seemed to lose track mid-task may have been partially caused by this — upgrading eliminates it.

- **APNG/PNG Upload Normalization** (LOW RELEVANCE): Model providers now normalize APNG-sniffed PNG uploads. Relevant if Josh ever sends images to Heather for visual analysis via Discord.

- **`openclaw channels list` Improvements**: Now channel-only by default, `--all` to include bundled and catalog channels, with installed/configured/enabled status per channel. Post-update, `openclaw channels list` provides a clean view of the Discord channel's health state.

- **Cached Skills Snapshot Clearing on `/new`**: Gateway clears cached skills snapshots on session reset. After skills are properly configured, session resets will correctly rebuild the visible skill list instead of serving a stale cached version.

**Josh version gap update: 2026.3.22 → 2026.5.7 = 13 releases behind** (grew by 1 since Day 18 Morning Scan).

### AlphaClaw — No New Releases
No new AlphaClaw releases since yesterday morning. Prior git sync reliability and Docker EBUSY fixes remain the relevant pending improvements for Josh's VPS at `5.78.142.81`.

---

## New Findings — Josh Instance (Day 19)

### 18. Active Memory Admin Scope Now Required — New Prerequisite for memory-core
**Risk: MEDIUM (New Sequencing)**

OpenClaw 2026.5.7 requires admin scope for Active Memory global memory toggles. The standing recommendation to enable `memory-core` in `plugins.entries` must now include admin scope configuration. Without it, the memory-core plugin may appear installed but silently fail on all global memory operations — a failure mode that would be difficult to diagnose.

**Updated memory-core entry for openclaw.json (replaces prior recommendation):**
```json
"memory-core": {
  "enabled": true,
  "config": {
    "scope": "admin"
  }
}
```

**Preferred approach:** Update to 2026.5.7 → run `openclaw doctor --fix` first — the doctor may auto-wire admin scope. Only manually edit if doctor does not resolve it.

---

### 19. Gemini 3 Thought-Signature Replay Now Fixed — Upgrade Unlocks Reliability
**Risk: INFO (Opportunity)**

Josh's Gemini 3 Flash Preview model was subject to tool-call thought-signature replay failures in long or multi-tool sessions. OpenClaw 2026.5.7 fixes this with fallback signature replay. Any sessions where Heather appeared to lose mid-task context or stall during tool-heavy workflows may have been partially caused by this bug. Upgrading eliminates it with no config change.

---

## Implementation Status — Day 19

Version gap: **2026.3.22 → 2026.5.7 (13 releases, 2.5+ months)**. iMessage monitoring paused for the entire 19-day scan window. Zero implementations across all scan days. Bootstrap TOOLS.md now **49 days stale**.

| Finding | Severity | Days Pending | Status |
|---|---|---|---|
| OpenClaw 13 releases outdated | HIGH | 19 | ⬜ Pending |
| iMessage monitoring paused | HIGH | 19 | ⬜ Pending |
| HEARTBEAT.md empty | HIGH | 19 | ⬜ Pending |
| No daily memory files (sessions leave no trace) | HIGH | 5 | ⬜ Pending |
| MEMORY.md missing | MEDIUM | 19 | ⬜ Pending |
| SOUL.md never evolved (stock template) | MEDIUM | 5 | ⬜ Pending |
| No-emoji rule not in SOUL.md (contradiction) | MEDIUM | 5 | ⬜ Pending |
| Bootstrap TOOLS.md stale — Google contradiction (49 days) | MEDIUM | 19 | ⬜ Pending |
| inbox-state.json malformed JSON | MEDIUM | 19 | ⬜ Pending |
| TOOLS.md template only | MEDIUM | 19 | ⬜ Pending |
| Active Memory plugin not installed | MEDIUM | 19 | ⬜ Pending |
| Active Memory admin scope required (new in 5.7) | MEDIUM | new | ⬜ Noted |
| Discord streaming off | LOW | 19 | ⬜ Pending |
| No compaction config | LOW | 19 | ⬜ Pending |
| Update before enabling heartbeats (sequencing) | INFO | 3 | ⬜ Noted |

**Correct implementation order (target updated to 2026.5.7):**
1. Fix `inbox-state.json` duplicate key (2 min — immediate data integrity)
2. Update OpenClaw to **2026.5.7** via AlphaClaw UI (5 min)
3. Run `openclaw doctor --fix` (1 min — auto-migrate legacy config + potentially auto-resolve memory-core scope)
4. Run `openclaw models auth list` (1 min — verify Google auth state)
5. Reconnect Google Workspace in AlphaClaw UI → regenerates stale bootstrap TOOLS.md (5 min)
6. Create `workspace/MEMORY.md` from soul-improvements.md template (15 min)
7. Populate `workspace/HEARTBEAT.md` — safe after 2026.5.5 heartbeat disconnect fix (15 min)
8. Fix no-emoji contradiction in SOUL.md (5 min)
9. Investigate iMessage pause (10 min)
10. Enable streaming + add compaction in openclaw.json (5 min)
11. Enable memory-core with admin scope (new 5.7 requirement) in openclaw.json (5 min)
12. Add Heather identity section to SOUL.md (10 min)

---
*Generated by AlphaClaw Apex Fleet Research Agent — Evening Scan — 2026-05-08*
