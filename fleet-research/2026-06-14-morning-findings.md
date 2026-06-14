# Fleet Research — Josh (Heather Schwartz) | 2026-06-14 Morning Scan

**Scan type:** Platform delta + persistent gap escalation + web research  
**Date:** 2026-06-14  
**Instance:** Josh Meyers — Heather Schwartz (personal assistant)  
**Repo:** lylle-rgb/josh_repo  
**Prior scan:** 2026-06-13 morning — zero fixes applied in 9 consecutive days

---

## Platform Status

| Item | Current | Latest Stable | Latest Beta | Gap |
|------|---------|--------------|------------|-----|
| OpenClaw | 2026.3.22 | **2026.6.5** | 2026.6.5-beta.6 (Jun 9) | **84 days** |
| AlphaClaw | Unknown | 0.9.16 | — | Check deployment |
| Primary model | google/gemini-3-flash-preview | gemini-3.5-flash (GA May 19) | — | Preview status |
| Fallback 1 | openrouter/google/gemini-2.5-flash | — | — | **⛔ DEPRECATES IN 3 DAYS (June 17)** |
| Fallback 2 | openrouter/anthropic/claude-3.5-haiku | — | — | OK |

---

## ⛔ JOSH-50 DAY 2 — DEADLINE IN 3 DAYS | gemini-2.5-flash Deprecation

**Status:** CRITICAL ESCALATION — Deadline June 17, 2026 (3 days from now)  
**Days open:** 2 (first identified June 13)  
**Changes since June 13:** Zero. This is still unresolved.

Josh's first fallback model `openrouter/google/gemini-2.5-flash` will stop responding on **June 17** when Google shuts down both `gemini-2.5-flash` and `gemini-2.5-pro`. The OpenRouter route dies when the underlying Google endpoint dies.

**Exact fix — `openclaw.json`, two characters, 30 seconds:**

```json
// agents.defaults.model.fallbacks — CURRENT:
"openrouter/google/gemini-2.5-flash"

// CHANGE TO:
"openrouter/google/gemini-3.5-flash"
```

`gemini-3.5-flash` reached GA on May 19, 2026 at Google I/O. Available on OpenRouter today. Direct successor — same speed tier, improved reasoning.

**Risk level:** NEGLIGIBLE (changing a fallback that is about to break anyway)  
**Time to apply:** 30 seconds via GitHub file editor

If this is not applied by June 17, Heather's fallback chain will be: primary → dead endpoint → claude-3.5-haiku. That's two hops instead of three, but the broken middle hop adds latency before giving up.

---

## NEW — JOSH-53 | Isolated Cron Sessions Available — Best Practice for Automation
**Severity:** MEDIUM  
**Status:** NEW — Platform capability (2026.5.x+, confirmed in 2026.6.5 docs)

OpenClaw cron now supports a `--session isolated` flag that gives each cron run its own clean session:

```
openclaw cron add "daily-brief" --every "24h" --session isolated -- "Check email and summarize overnight"
```

**Why it matters for Heather:**  
Heather's heartbeat polls currently run in the main session, mixing proactive check results with Josh's conversation history. Isolated sessions keep the main thread clean and prevent old context from influencing cron behavior. Each isolated run:
- Starts with a fresh session (no prior conversation bleed)
- Delivers its result then terminates
- Gets pruned automatically after the retention window (24h default)
- Closes any browser tabs or processes it opened (no orphaned processes)

**Recommended uses for Heather:**  
- Daily morning briefing (email + calendar + weather summary to Discord)
- Weekly digest delivery
- One-shot reminders Josh sets during the day

Heartbeat polls (the rolling checks in HEARTBEAT.md) should stay in the main session. Pure one-shot scheduled deliveries belong in isolated cron.

**Risk level:** LOW (additive)  
**Dependency:** Requires cron to be set up at all (HEARTBEAT.md still empty — JOSH-31)

---

## NEW — JOSH-54 | ClawHub Skill Malware Advisory — Audit Any Third-Party Skills
**Severity:** MEDIUM  
**Status:** NEW — Security advisory (January 2026 incident)

In early 2026, ClawHub (the OpenClaw skill marketplace) purged **2,419 suspicious skills** after discovering **1,184 were distributing wallet-stealing malware**. One malicious package disguised as a Google Calendar skill was downloaded 14,285 times before detection.

**Josh's current exposure:** The `josh_repo` has no `skills/` directory — Heather appears to be running with built-in skills only. This is actually the safe default. However:
- If Josh or Heather ever installs a ClawHub skill in the future, verify it via GitHub source before installing
- OpenClaw 2026.6.5 added **Skill Workshop**, a control plane that enforces review before agent-created skills touch production workflows — use it post-upgrade
- Pin skills to specific commit hashes (2026.6.5 feature) rather than installing from `@latest`

**Recommendation:** Add a note to `workspace/TOOLS.md` when populating it: "Only install skills from verified GitHub repos or ones we've reviewed. Never install unaudited ClawHub skills."

**Risk level:** LOW for current state; MEDIUM if third-party skills are added later

---

## NEW — JOSH-55 | Brave LLM Context API — Better Web Search Grounding
**Severity:** LOW  
**Status:** NEW — Platform capability (post-upgrade)

Brave recently launched an `llm-context` mode in their Search API. Instead of returning standard web snippets, it returns **extracted page chunks** — pre-processed content optimized for model consumption.

**Impact for Heather:**  
When Josh asks Heather to research something (news, local business info, product comparisons), `llm-context` mode returns cleaner, more complete content than snippet mode. Research tasks that took 45 minutes manually drop to ~4.5 minutes with this enabled.

**How to enable (post-upgrade to 2026.6.5):**  
The Brave search integration in OpenClaw supports this mode. No config change required — upgrading OpenClaw enables it automatically when `BRAVE_API_KEY` is present. Ensure `BRAVE_API_KEY` is set in the VPS environment.

**Risk level:** NEGLIGIBLE (post-upgrade quality improvement)  
**Dependency:** Requires OpenClaw upgrade + BRAVE_API_KEY in environment

---

## NEW — JOSH-56 | Skill Workshop — Agent-Created Skills Now Reviewable
**Severity:** LOW  
**Status:** NEW — Platform feature (2026.6.1+)

OpenClaw 2026.6.1 shipped **Skill Workshop**, a control plane for reviewing skills before they're deployed. If Heather ever creates a skill autonomously (writing a tool to check Josh's flight status, pulling calendar into a digest), Skill Workshop:
- Shows a pending proposal list
- Allows review and approval before the skill touches any production workflow
- Supports rollback metadata if a skill misbehaves
- Includes Control UI screens for management

**Why it matters for Josh:**  
As Heather grows into a more autonomous assistant, she may create skills. Without Skill Workshop, autonomously-created skills go live immediately. With it, you review before they deploy.

**Risk level:** NONE (informational; becomes relevant post-upgrade)

---

## Persistent Critical Findings (9 Days Without Any Resolution)

| Finding | Severity | Days Open | Status |
|---------|----------|-----------|--------|
| JOSH-50: gemini-2.5-flash deprecates June 17 | **CRITICAL** | 2 | **3 days to deadline** |
| JOSH-45: No Google account connected | **CRITICAL** | 9 | Blocks all email/calendar/contacts |
| JOSH-30: MEMORY.md never created | **CRITICAL** | 84 | Heather has no long-term memory |
| JOSH-44/52: Platform 84 days outdated | HIGH | 84 | Requires VPS |
| JOSH-31: HEARTBEAT.md empty | HIGH | 84 | No proactive monitoring |
| JOSH-46: Discord streaming disabled | MEDIUM | 9 | `streaming: "off"` = silence |
| JOSH-53: No isolated cron sessions | MEDIUM | NEW | Best practice not configured |
| JOSH-54: No skill audit policy | MEDIUM | NEW | Security hygiene gap |
| JOSH-37: SOUL.md generic template | MEDIUM | 84 | Never personalized for Heather |
| JOSH-32: TOOLS.md empty template | MEDIUM | 84 | No setup documentation |
| JOSH-34: Emoji contradiction | LOW | 84 | AGENTS.md says use emoji; USER.md says NO |
| JOSH-49: BOOTSTRAP.md stale | LOW | 9 | Dead weight in every session |

---

## Priority Action Queue

### ⛔ Deadline June 17 — Do This Today:

1. **[CRITICAL] Fix fallback model** — Change `openrouter/google/gemini-2.5-flash` → `openrouter/google/gemini-3.5-flash` in `openclaw.json`. GitHub file editor, 30 seconds, zero risk.

### GitHub-Only (No VPS, No Downtime):

2. **[CRITICAL] Create `workspace/MEMORY.md`** — Heather has no long-term memory. 84 days open. Template:
   ```markdown
   # MEMORY.md — Heather's Long-Term Memory
   
   ## About Josh
   - Founder & CEO @blisslifestyleofficial, Partner @obenhifi
   - Based in Los Angeles (PST/PDT)
   - Do NOT send emoji reactions to messages
   - Named me Heather
   - Georgia State University alum
   
   ## Preferences
   - Communication: Discord (primary), no emojis in reactions
   
   ## Open Loops
   (fill in over time)
   ```
3. **[MEDIUM] Fill in `workspace/TOOLS.md`** — Document Discord as primary, iMessage status, no Google yet.
4. **[MEDIUM] Personalize `workspace/SOUL.md`** — See prior soul-improvements files for recommendations.
5. **[LOW] Delete `workspace/BOOTSTRAP.md`** — Stale; loaded every session for no reason.
6. **[LOW] Fix emoji contradiction** — Remove emoji reaction encouragement from AGENTS.md lines (conflicts with USER.md "STRICT: DO NOT SEND EMOJI REACTIONS").

### Requires VPS / Setup UI:

7. **[CRITICAL] Connect Google account** — Setup UI at `https://5.78.142.81.sslip.io#general` → Google Workspace → authorize Gmail + Calendar + Contacts. Documented steps in `workspace/memory/onboarding-google.md`.
8. **[HIGH] Upgrade to 2026.6.5** — Stage: 2026.3.22 → 2026.5.27 → 2026.6.2 → 2026.6.5.
9. **[HIGH] Add compaction + memoryFlush to openclaw.json** — Borrow Noah's working config:
   ```json
   "compaction": {
     "reserveTokensFloor": 40000,
     "memoryFlush": { "enabled": true, "softThresholdTokens": 4000 }
   }
   ```
10. **[MEDIUM] Enable Discord streaming** — `channels.discord.streaming: "on"` after upgrade.
11. **[MEDIUM] Add memory-core plugin** — Add to plugins.allow + plugins.entries post-upgrade.

---

## Platform Research Notes (2026-06-14)

- **OpenClaw 2026.6.5** remains current stable (June 3). Beta train is at 2026.6.5-beta.6 (June 9) — no new stable since June 13 scan.
- **gemini-2.5-flash deprecation is confirmed for June 17** by Google. Three days from today. Josh is the only fleet customer affected (Noah uses Anthropic). This is a sub-minute fix.
- **Gemini 3.5 Flash (GA)** is available on OpenRouter as `openrouter/google/gemini-3.5-flash`. Google I/O May 19, 2026. Drop-in replacement for the fallback slot.
- **ClawHub malware incident** (January 2026): 1,184 malicious skills detected among 2,419 purged. OpenClaw 2026.6.5 added pinned-commit installs and Skill Workshop review flow as mitigations. Josh's no-skills-directory stance is inadvertently safe.
- **Nine days without any GitHub-only fixes.** The MEMORY.md (JOSH-30) and BOOTSTRAP.md deletion (JOSH-49) require a GitHub file create and a GitHub file delete. Combined: under 5 minutes. Both remain unresolved since May 12.
- **Heather's value to Josh is gated entirely on Google Workspace.** iMessage monitoring, email triage, calendar management, contacts lookup — all blocked. The gap between what Heather was deployed to do and what she can currently do is 100%.
