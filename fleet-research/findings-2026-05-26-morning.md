# Fleet Research — Josh (Heather Schwartz) | 2026-05-26 Morning Scan

**Scan type:** Morning (web research, release tracking, community insights)
**Date:** 2026-05-26
**Instance:** Josh Meyers — Heather Schwartz (personal assistant)
**Repo:** lylle-rgb/josh_repo
**Previous scan:** 2026-05-25 evening
**Day count:** Day 38 of fleet monitoring

---

## Platform Status (Morning Check)

| Item | Current | Latest | Gap |
|------|---------|--------|-----|
| OpenClaw | 2026.3.22 | **2026.5.22 stable** | ~25 releases behind — **Day 5 overdue** |
| AlphaClaw | Unknown | 0.9.16 | No new release (Day 12 without update) |
| Primary model | google/gemini-3-flash-preview | — | Active |
| Alpha train | 2026.5.25-alpha.1 | — | Do NOT upgrade |

---

## New Findings — Morning of 2026-05-26

### FINDING-JOSH-57 | Discord Interactive Messages — Button + Dropdown Confirmations
**Severity:** MEDIUM (opportunity — post-upgrade)
**Status:** NEW — confirmed in OpenClaw 2026.5.22 feature set

OpenClaw's Discord channel now supports **button interactions and dropdown menus** — bots send structured choice-based messages without requiring typed commands. Replies stream in real-time.

**Application for Heather:**
Currently Josh must type approval responses in Discord or iMessage. With button interactions:
- "Respond to this email?" → [Reply] [Skip] [Snooze 1 hour]
- "Meeting in 2 hours" → [Add prep notes] [Dismiss] [Reschedule]
- Email send confirmations → single button press, no typing

This is a lower-friction alternative to the iMessage 👍👎 reactions (still in beta train), available today on Discord post-upgrade. For Josh who is strict about efficiency, this eliminates typed acknowledgment overhead.

**Pre-requisite:** Upgrade to 2026.5.22 only. No additional config required after upgrade.
**Risk level:** LOW — opportunity, no action until after upgrade.

---

### FINDING-JOSH-58 | Subagents for Parallel Heartbeat Checks
**Severity:** LOW (opportunity)
**Status:** NEW — confirmed in OpenClaw subagent documentation

OpenClaw subagents support **parallel execution of independent background tasks** via `sessions_spawn`. Default `maxSpawnDepth: 1` allows a main agent to spawn parallel workers that announce results back to the calling channel.

**Application for Heather:**
Heather's heartbeat checks (email + calendar + contacts) currently run serially. With subagents, three parallel workers could complete the full inbox-calendar-contacts sweep in ~1/3 the latency:

```json
"agents": {
  "defaults": {
    "subagents": {
      "runTimeoutSeconds": 60,
      "model": {
        "primary": "google/gemini-3-flash-preview"
      }
    }
  }
}
```

**Blocker:** HEARTBEAT.md must be populated first (JOSH-31/54 — Day 38 empty). Subagents optimize the tasks once the tasks exist.
**Risk level:** LOW — future optimization opportunity.

---

### FINDING-JOSH-59 | Memory Architecture Gap — No Compaction Config
**Severity:** HIGH (actionable — amplifies the Day-38 MEMORY.md crisis)
**Status:** NEW — confirmed via memory best practices research + openclaw.json inspection

Research confirms: **MEMORY.md should stay under 800 words** (loaded in every main session, directly impacts token budget). The pre-compaction memory flush is described as "the single most impactful memory configuration change" available.

**Josh's openclaw.json has NO compaction config at all.** Noah has it (albeit misconfigured). Josh has nothing:

```json
// Josh's agents.defaults — missing entirely:
// compaction.reserveTokensFloor
// compaction.memoryFlush.enabled
// compaction.memoryFlush.softThresholdTokens
// contextPruning.ttl
```

**Consequence:** Even if MEMORY.md is created today (Day 38), information captured in sessions over ~60-70% context fill will be lost without a flush. The agent will silently lose context without writing to disk.

**Recommended addition to openclaw.json (no restart required):**
```json
"agents": {
  "defaults": {
    "compaction": {
      "reserveTokensFloor": 30000,
      "memoryFlush": {
        "enabled": true,
        "softThresholdTokens": 6000
      }
    },
    "contextPruning": {
      "mode": "cache-ttl",
      "ttl": "30m"
    }
  }
}
```

**MEMORY.md starter template:**
```markdown
# Heather's Long-Term Memory

## About Josh
- Name: Joshua Meyers | Timezone: LA (PST/PDT)
- Businesses: Bliss (luxury lifestyle brand, CEO), Oben HiFi (Partner)
- Based: Los Angeles
- STRICT: NO emoji reactions on any platform — ever

## Preferences
- [Fill as learned]

## Active Projects
- [Fill as identified]

## Decisions & Notes
- [Fill as discovered]
```

**Risk level:** HIGH — this is a prerequisite quality fix for the MEMORY.md gap. Creating MEMORY.md without compaction config means content may not survive long sessions.

---

### FINDING-JOSH-60 | Node.js 22.19 Minimum — Infrastructure Pre-Check for Upgrade
**Severity:** LOW (infrastructure awareness)
**Status:** NEW — confirmed in OpenClaw Pi package changelog

OpenClaw has raised its minimum supported Node.js 22 line to **22.19** in recent Pi package updates (Pi packages updated to 0.75.1 alongside this change).

**Action before upgrading Josh's VPS:**
```bash
node --version  # Should report >= v22.19.0
```
If below, update Node.js before attempting the OpenClaw 2026.5.22 upgrade to avoid a broken install.

**Risk level:** LOW — check during upgrade prep.

---

### FINDING-JOSH-61 | Session Picker Pagination — Continuity Improvement in 2026.5.22
**Severity:** INFO
**Status:** NEW — confirmed in 2026.5.22 release notes

OpenClaw 2026.5.22 adds search and "Load More" pagination to the Control UI chat session picker. Initial session loads are bounded; older conversations are reachable without UI freeze.

**Why it matters for Heather:** Once MEMORY.md is created and sessions begin accumulating durable context, the improved session picker makes it easier to reference past conversations during troubleshooting or when Heather is reconstructing context after a restart.

**Risk level:** INFO — improvement included automatically on upgrade.

---

## Persistent Critical Issues (Day 38 Morning)

| Finding | Severity | Day # | Zero-Config Fix |
|---------|----------|-------|------------------|
| JOSH-30/48: MEMORY.md never created | CRITICAL | **38** | Create file — 15 min |
| JOSH-31/54: HEARTBEAT.md empty | HIGH | **38** | Add tasks — 5 min |
| JOSH-59: No compaction config | HIGH | **NEW** | Add to openclaw.json — 2 min |
| JOSH-39: Upgrade to 2026.5.22 | HIGH | **5 days overdue** | Upgrade via AlphaClaw UI |
| JOSH-50: Dead fallback (claude-3.5-haiku) | MEDIUM | **18** | Remove 1 line, no restart |
| JOSH-34/56: Emoji rule contradiction in AGENTS.md | MEDIUM | **38** | Edit AGENTS.md — 2 min |
| JOSH-37: SOUL.md generic template | MEDIUM | **38** | Personalize — 30 min |
| JOSH-55: TOOLS.md blank | MEDIUM | **38** | Populate — 10 min |

---

## Morning Action Priority (2026-05-26)

**All zero-downtime, zero-upgrade — apply in GitHub:**

1. **Add compaction + contextPruning config to `openclaw.json`** — prerequisite for MEMORY.md to work (JOSH-59)
2. **Create `workspace/MEMORY.md`** — Day 38, 15 min (JOSH-30)
3. **Populate `workspace/HEARTBEAT.md`** — Gmail + Calendar tasks (JOSH-31)
4. **Fix dead fallback** in `openclaw.json` — remove `openrouter/anthropic/claude-3.5-haiku` (JOSH-50)
5. **Fix emoji contradiction in `workspace/AGENTS.md`** — remove or override "React Like a Human" section (JOSH-34)
6. **Populate `workspace/TOOLS.md`** — Google auth status, Discord guild, model chain, iMessage paused (JOSH-55)

**Post-upgrade (2026.5.22):**
- Discord button interactions (zero config after upgrade)
- Enable memory-core + active-memory plugins
- Reconnect iMessage bridge (unlock 👍👎 reaction approvals in beta train)

---

## Research Sources (Morning 2026-05-26)

- OpenClaw 2026.5.25-alpha.1 release notes (alpha train — do not deploy)
- OpenClaw 2026.5.22 feature set (Discord interactive messages, session picker pagination)
- OpenClaw subagents documentation (parallel task execution, maxSpawnDepth config)
- Memory best practices research (velvetshark.com OpenClaw Memory Masterclass, remoteopenclaw.com)
- Node.js 22.19 minimum confirmed in Pi package 0.75.1 release notes
- AlphaClaw 0.9.16 — no new release (Day 12 without update)
