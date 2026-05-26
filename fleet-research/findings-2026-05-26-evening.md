# Fleet Research — Josh (Heather Schwartz) | 2026-05-26 Evening Scan

**Scan type:** Evening (web research + platform release tracking + deep codebase analysis)
**Date:** 2026-05-26
**Instance:** Josh Meyers — Heather Schwartz (personal assistant)
**Repo:** lylle-rgb/josh_repo
**Previous scan:** 2026-05-25 evening

---

## Platform Status

| Item | Current | Latest | Gap |
|------|---------|--------|-----|
| OpenClaw | 2026.3.22 | **2026.5.20 stable** | **~2 months behind; upgrade recommendation now Day 4** |
| AlphaClaw | Unknown | 0.9.16 | No new release since May 15 — 11 days |
| Primary model | google/gemini-3-flash-preview | — | Active |
| 2026.5.25-alpha.1 | In train | — | Alpha — do NOT upgrade |
| 2026.5.22 stable | Incoming | — | "Faster Gateway, Meeting Notes, Safer Defaults" |

---

## New Since Last Scan (2026-05-25 Evening)

### FINDING-JOSH-51 | OpenClaw 2026.5.25-alpha.1 In Train — Do Not Upgrade
**Severity:** INFO (track)
**Status:** NEW

OpenClaw shipped `2026.5.25-alpha.1` today. This is an alpha channel release — not suitable for production. Features included: faster gateway auth warmups, improved onboarding flow, chat and session UI polish, stronger plugin and SDK support, expanded diagnostics (OTLP spans, sanitized secrets, bounded skill usage metrics).

**Action:** None yet. Continue targeting `2026.5.20 stable` for the upgrade. `2026.5.22` stable is the next milestone once it exits beta (ETA ~5-7 days).

**Risk level:** INFO — informational only.

---

### FINDING-JOSH-52 | OpenClaw 2026.5.22 Confirmed: "Faster Gateway Startup, Meeting Notes, Safer Agent Defaults"
**Severity:** INFO (opportunity — post-upgrade)
**Status:** NEW — confirmed stable content from community blog

The OpenClaw Playbook blog has confirmed the 2026.5.22 stable release is titled **"Faster Gateway Startup, Meeting Notes, and Safer Agent Defaults"**. Key confirmed features:

- **Faster gateway startup** — auth warmup improvements reduce cold-start latency. Heather's boot time on Josh's VPS will be faster after upgrading to 2026.5.22.
- **Meeting Notes plugin** — Discord voice capture architecture (documented yesterday) now has a named release milestone. This is the version that officially ships the meeting-notes plugin as a supported integration.
- **Safer agent defaults** — "agent defaults" tightening means OpenClaw's out-of-the-box safety posture improves. Exact scope TBD until stable releases, but likely affects external action confirmation flows.
- **Diagnostics privacy improvements** — OTLP spans now scrub secret IDs, provider names from spans. This is relevant once Josh upgrades — gateway monitoring becomes privacy-safe by default.

**Why this matters for Josh's upgrade path:**
Previous recommendation was to upgrade to `2026.5.20 stable` immediately. New recommendation:
- Upgrade to `2026.5.20` NOW (still 4 days overdue)
- Plan to upgrade to `2026.5.22` stable ~5-7 days after it lands
- Skip `2026.5.25-alpha.1` entirely

**Risk level:** INFO — no action yet, but upgrade urgency to 2026.5.20 unchanged.

---

### FINDING-JOSH-53 | Bootstrap Hook Files Confirmed Present — JOSH-41 Resolved
**Severity:** INFO (resolved)
**Status:** NEW — resolved via direct inspection today

Direct inspection of `workspace/hooks/bootstrap/` confirms:
- `workspace/hooks/bootstrap/AGENTS.md` — present (1,755 bytes, SHA: 1cfc2b557...)
- `workspace/hooks/bootstrap/TOOLS.md` — present (3,866 bytes, SHA: 286f8ced8...)

These files are loaded at bootstrap per `openclaw.json` hook config:
```json
"bootstrap-extra-files": {
  "enabled": true,
  "paths": ["hooks/bootstrap/AGENTS.md", "hooks/bootstrap/TOOLS.md"]
}
```

**JOSH-41 is RESOLVED.** Bootstrap file injection is working. The hook is configured and the files exist.

**Residual concern:** The bootstrap hook injects AGENTS.md and TOOLS.md from the hooks directory into every session startup — but BOTH of those bootstrap files still contain the generic upstream template content. They are being loaded, but they're not teaching Heather anything session-specific. The lack of personalization in workspace/SOUL.md and workspace/USER.md is still the primary gap.

**Risk level:** INFO — resolved concern, no action needed on hooks specifically.

---

### FINDING-JOSH-54 | HEARTBEAT.md Empty — Zero Proactive Monitoring Configured
**Severity:** HIGH (persistent — newly confirmed by direct inspection)
**Status:** ESCALATING — Day 37+ with zero heartbeat tasks

Direct file inspection today confirms `workspace/HEARTBEAT.md` contains only comments:
```
# Keep this file empty (or with only comments) to skip heartbeat API calls.
# Add tasks below when you want the agent to check something periodically.
```

The heartbeat system fires periodically. AGENTS.md explicitly instructs Heather to use heartbeats productively — checking email, calendar, mentions, weather 2-4 times per day. But HEARTBEAT.md has been blank since setup. Every heartbeat poll triggers a response of just `HEARTBEAT_OK` and burns a small API cost while doing nothing.

**What Heather should be checking but isn't:**
- Gmail inbox — urgent unread emails from Josh's contacts
- Google Calendar — upcoming events in next 24-48h (meetings, appointments)
- iMessage (if/when bridge is re-enabled) — pending messages
- Calendar prep — brief upcoming event summaries

**What a working HEARTBEAT.md looks like:**
```markdown
# Heather's Heartbeat Checklist

## Check Each Heartbeat (rotate, 2-4x/day, skip 23:00-08:00 PST)

1. Gmail: Any urgent unread? (From known contacts, with urgent/action keywords)
2. Calendar: Events in next 48h? (Prep notes if meeting <2h away)
3. Memory: Update memory/heartbeat-state.json with lastChecks timestamps

## Reach Out If:
- Important email from a contact Josh would care about
- Calendar event <2h away Josh hasn't prepped for
- Anything time-sensitive discovered

## Stay Quiet If:
- Late night (23:00-08:00 PST)
- Nothing new since last check
- Last check was <30min ago
```

**Risk level:** HIGH — Heather is running blind on Josh's inbox and calendar. The integrations are connected (Google auth confirmed); the automation just isn't configured.

---

### FINDING-JOSH-55 | TOOLS.md Empty After 37+ Days — No Environment Notes
**Severity:** MEDIUM
**Status:** NEW — confirmed by direct inspection today

`workspace/TOOLS.md` (SHA: 917e2fa86cc...) is the exact upstream generic template — unchanged since installation. The file contains only placeholder examples (fake camera names, fake SSH hosts, fake TTS prefs). After 37+ days of operation, no environment-specific notes have been written.

**What TOOLS.md should contain for Heather:**
```markdown
### Auth & Integrations
- Google: api_key mode — Gmail, Calendar, Drive, Contacts, Tasks all active
- iMessage: Bridge installed, currently PAUSED (see memory/inbox-state.json)
- Discord: Active via bot token — Guild 1484448262290276464

### Models
- Primary: google/gemini-3-flash-preview (Google AI Studio api_key)
- Fallback 1: openrouter/google/gemini-2.5-flash
- Fallback 2: openrouter/anthropic/claude-3.5-haiku (DEAD — remove)

### OpenClaw Version
- Current: 2026.3.22 (outdated — target 2026.5.20)
```

**Why this matters:** When Heather reads TOOLS.md at session start, it should tell her the actual environment she's running in. Instead, it tells her about cameras and SSH hosts that don't exist.

**Risk level:** MEDIUM — minor efficiency impact. Without TOOLS.md populated, Heather may try to use tools that aren't configured or miss tools that are.

---

### FINDING-JOSH-56 | AGENTS.md SHA Identical to Noah's — Zero Customization
**Severity:** MEDIUM (newly confirmed cross-repo gap)
**Status:** NEW — confirmed by SHA comparison today

Both `josh_repo/workspace/AGENTS.md` and `noah--repo/workspace/AGENTS.md` share identical SHA `3faead9716a2c168df79c2fba558bd04cd8c76d0`. This is the upstream OpenClaw generic template — byte-for-byte identical across both customer instances.

**What Josh's AGENTS.md is missing that it should have:**
- No mention of the `STRICT: DO NOT SEND EMOJI REACTIONS` rule from USER.md
- No mention of the iMessage bridge status (paused)
- No mention of Josh's business context (Bliss luxury brand, Oben HiFi) for voice/tone guidance
- No mention of Google Workspace tools being active and preferred
- No LA timezone anchoring for heartbeat quiet hours
- No documentation of which Discord server/guild Heather operates in

**The emoji rule contradiction remains the most dangerous gap:** AGENTS.md section "React Like a Human!" explicitly tells Heather to use emoji reactions on Discord. USER.md says `STRICT: DO NOT SEND EMOJI REACTIONS`. The AGENTS.md instruction actively contradicts Josh's explicit preference. This will cause violations on every Discord session until resolved.

**Risk level:** MEDIUM — the emoji contradiction is a recurring behavioral risk that will manifest in every Discord session.

---

## Persistent Findings (Unresolved from 2026-05-25 Evening)

| Finding | Severity | Status | Day # |
|---------|----------|--------|---------|
| JOSH-30: MEMORY.md never created | CRITICAL | PERSISTENT | **38+** |
| JOSH-31: HEARTBEAT.md empty (now confirmed) | HIGH | **CONFIRMED** | **38+** |
| JOSH-39: Upgrade to OpenClaw 2026.5.20 | HIGH | **4 DAYS OVERDUE** | 4 |
| JOSH-41: Bootstrap hook files | INFO | **RESOLVED** | — |
| JOSH-37: SOUL.md never personalized | MEDIUM | PERSISTENT | **38+** |
| JOSH-32: Bootstrap TOOLS.md false Google auth | MEDIUM | PERSISTENT | **38+** |
| JOSH-33: iMessage paused + malformed JSON | MEDIUM | PERSISTENT | 29+ |
| JOSH-34: Emoji contradiction (AGENTS vs USER) | MEDIUM | **CONFIRMED ACTIVE** | 5 |
| JOSH-35: streaming.mode progress available | INFO | OPPORTUNITY | — |
| JOSH-36: Mem0 / Active Memory plugin | INFO | OPPORTUNITY | — |
| JOSH-38: Crash notifications | INFO | OPPORTUNITY | — |
| JOSH-40: 2026.5.21 transcript durability | INFO | PERSISTENT | 3 |
| JOSH-42: ClawHub skills security advisory | MEDIUM | PERSISTENT | 3 |
| JOSH-43: defineToolPlugin custom skills | INFO | OPPORTUNITY | — |
| JOSH-44: Meeting capture plugin | INFO | TRACK | 2 |
| JOSH-45: Package integrity gates | INFO | TRACK | 2 |
| JOSH-46: Meeting capture architecture confirmed | INFO | PERSISTENT | 1 |
| JOSH-47: OpenRouter routing controls in 2026.5.22 | LOW | PERSISTENT | 1 |
| JOSH-48: MEMORY.md Day 38+ confirmed | CRITICAL | ESCALATING | 38+ |
| JOSH-49: SOUL.md SHA unchanged — generic template | MEDIUM | PERSISTENT | 38+ |
| JOSH-50: Dead OpenRouter fallback — Day 18 | MEDIUM | PERSISTENT | 18 |
| JOSH-51: 2026.5.25-alpha.1 in train | INFO | NEW | 0 |
| JOSH-52: 2026.5.22 stable content confirmed | INFO | NEW | 0 |
| JOSH-53: Bootstrap hooks confirmed present | INFO | **RESOLVED** | — |
| JOSH-54: HEARTBEAT.md empty — confirmed | HIGH | NEW/CONFIRMED | 38+ |
| JOSH-55: TOOLS.md empty — confirmed | MEDIUM | NEW/CONFIRMED | 38+ |
| JOSH-56: AGENTS.md SHA identical to Noah — zero customization | MEDIUM | NEW | 0 |

---

## Immediate Action List (No Upgrade Required)

These fixes require **zero platform changes**, **zero downtime**, and can be applied directly to the repo:

1. **Create `workspace/MEMORY.md`** — the single highest-value fix. 5 minutes. See soul-improvements for template.
2. **Fix `workspace/SOUL.md`** — add Josh-specific rules: no emoji reactions, LA timezone, Bliss/Oben HiFi business context.
3. **Fix `workspace/AGENTS.md`** — remove or override the "React Like a Human" emoji reaction section with Josh's STRICT rule.
4. **Populate `workspace/HEARTBEAT.md`** — add Gmail + Calendar check tasks.
5. **Populate `workspace/TOOLS.md`** — add actual environment notes (Google auth, iMessage status, Discord guild, model chain).
6. **Fix `openclaw.json` dead fallback** — remove `openrouter/anthropic/claude-3.5-haiku` (30 seconds, no restart).

**None of these require touching the VPS or upgrading OpenClaw.**

---

## Platform Research Notes (2026-05-26)

- **OpenClaw latest stable:** 2026.5.20 — no new stable release today (Day 4 overdue for Josh)
- **OpenClaw 2026.5.25-alpha.1:** Alpha today. Faster gateway auth, diagnostics improvements, plugin/SDK hardening. Do NOT upgrade.
- **OpenClaw 2026.5.22:** Next stable. Confirmed content: Faster Gateway Startup, Meeting Notes plugin, Safer Agent Defaults, diagnostics privacy (OTLP scrubbing). ETA approximately 5-7 days.
- **AlphaClaw:** 0.9.16 — no new release (Day 11 without update).
- **Community sentiment:** No regressions reported on 2026.5.20. Meeting notes plugin documentation is mature. Community adoption positive for the upgrade path Josh needs.
- **Diagnostics improvement note:** Post-upgrade, OTLP spans scrub secret IDs by default. If Josh is running any monitoring, the upgrade makes it privacy-safe automatically.
