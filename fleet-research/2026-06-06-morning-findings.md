# Fleet Research — Josh (Heather) | 2026-06-06 Morning Scan

**Scan type:** Platform delta + web research  
**Date:** 2026-06-06  
**Instance:** Josh Meyers — Heather Schwartz (personal assistant)  
**Repo:** lylle-rgb/josh_repo  
**Prior scan:** 2026-06-03 morning (FINDING-JOSH-40 through JOSH-43)

---

## Platform Status

| Item | Current | Latest Stable | Gap |
|------|---------|--------------|-----|
| OpenClaw | 2026.3.22 | **2026.6.2** | **76 days** |
| AlphaClaw | Unknown | 0.9.16 | Check deployment |
| Primary model | google/gemini-3-flash-preview | — | — |

---

## NEW Findings (June 6 Morning Delta)

### FINDING-JOSH-44 | OpenClaw 2026.6.2 Released — Now Latest Stable
**Severity:** HIGH  
**Status:** NEW — Upgrade target updated

OpenClaw **2026.6.2** is now the latest stable release (graduated between June 3 and June 6). Josh’s upgrade target has changed from 2026.6.1 → **2026.6.2**.

**What’s in 2026.6.2 that matters for Josh:**
- **Safer plugin installs:** Operator install policy replaces old dangerous-code scanner. ClawHub installs now go through an approved policy gate — more reliable and less likely to fail during skill upgrades
- **Discord reliability:** Fixes around duplicate transcript mirrors, streamed-final previews, approval allowlists, Discord voice errors, internal progress traces — directly affects Heather’s Discord stability
- **Interrupted tool call recovery** (confirmed stable): Multi-step tasks (read email → draft → send → confirm) now recover cleanly from network hiccups or container blips mid-sequence — no more forced restarts
- **SQLite-backed iMessage state** (confirmed stable): The malformed `inbox-state.json` (JOSH-33 — duplicate key, iMessage paused ~41 days) is auto-fixed during the SQLite migration triggered by `openclaw doctor --fix` post-upgrade
- **Skill Workshop** (confirmed stable): Josh can approve Heather skill additions from the Control UI without SSH access to the VPS
- **iOS push relay** (confirmed stable): Auto-activates on iPhone/iPad connection — relevant if Josh interacts with Heather from his phone

**Exact changes to apply:**  
On VPS: `openclaw upgrade` then `openclaw doctor --fix` (triggers iMessage SQLite migration).

**Risk level:** MEDIUM (upgrade requires VPS access; test Discord connectivity post-upgrade)

---

### FINDING-JOSH-45 | Discord Config — Security Exposure vs. Noah
**Severity:** MEDIUM  
**Status:** NEW — Cross-customer comparison finding

Josh’s Discord config is significantly more permissive than Noah’s, creating unnecessary exposure given Heather’s access to personal data:

| Setting | Josh (current) | Noah | Risk |
|---------|------|------|------|
| `groupPolicy` | `"open"` | `"allowlist"` | Any server can add Heather |
| `dmPolicy` | `"open"` | `"pairing"` | Any user can DM Heather |
| `allowFrom` | `["*"]` | Allowlist | Anyone can send messages |
| `streaming` | `"off"` | `"off"` | Both could enable `"progress"` post-upgrade |

**Why it matters for Josh:**
Heather has access to Josh’s Gmail, calendar, and contacts. An `"open"` groupPolicy means any Discord server admin can add the bot and any user can DM it — Heather will respond to all of them with her full personal context. `dmPolicy: "pairing"` requires users to pair with the bot before DMing, and `groupPolicy: "allowlist"` restricts which servers the bot operates in.

**Exact changes to apply (openclaw.json — GitHub-only, no VPS):**
```json
"channels": {
  "discord": {
    "groupPolicy": "allowlist",
    "dmPolicy": "pairing",
    "guilds": {
      "1484448262290276464": {
        "requireMention": false
      }
    }
  }
}
```
Remove `"allowFrom": ["*"]` from the discord channel config. Takes effect on next AlphaClaw config reload.

**Risk level:** MEDIUM (test that Josh can still DM Heather after switching `dmPolicy` to `"pairing"`)

---

### FINDING-JOSH-46 | Missing Config: Compaction + ContextPruning
**Severity:** MEDIUM  
**Status:** NEW — Cross-customer config gap

Noah’s `openclaw.json` has two `agents.defaults` settings Josh is missing entirely:

**1. Compaction with memoryFlush** (Noah has this; Josh does not):
```json
"compaction": {
  "reserveTokensFloor": 40000,
  "memoryFlush": {
    "enabled": true,
    "softThresholdTokens": 4000
  }
}
```
Without this, Heather’s context compaction runs without a memory flush — meaning important context from the start of a long conversation can be silently dropped when the context window fills.

**2. ContextPruning** (Noah has this — though with a broken TTL):
```json
"contextPruning": {
  "mode": "cache-ttl",
  "ttl": "30m"
}
```
Note: Noah’s TTL is `"5m"` (CRITICAL bug in that repo). Josh should add this with a sensible `"30m"` TTL from the start.

**Why it matters for Josh:**
Heather handles long multi-step tasks (email drafts, calendar management, web research chains). Without `memoryFlush`, a compaction mid-task can lose the initial task context. ContextPruning controls how aggressively old cached context is discarded — `30m` is appropriate for a personal assistant.

**Exact changes to apply (openclaw.json under `agents.defaults` — GitHub-only):**
```json
"compaction": {
  "reserveTokensFloor": 40000,
  "memoryFlush": {
    "enabled": true,
    "softThresholdTokens": 4000
  }
},
"contextPruning": {
  "mode": "cache-ttl",
  "ttl": "30m"
}
```
Takes effect on next agent session start. No VPS required.

**Risk level:** LOW (additive config — improves context management, no behavioral regression)

---

### FINDING-JOSH-47 | Missing Plugin: memory-core (Noah Has It)
**Severity:** MEDIUM  
**Status:** NEW — Cross-customer plugin gap

Noah’s `openclaw.json` has `memory-core` in `plugins.allow`. Josh does not. memory-core is the Dreaming plugin — it runs context consolidation and long-term memory synthesis between sessions.

**Why it matters for Josh:**
As a personal assistant with 76+ days of operational history, Heather has extensive context worth preserving — Josh’s preferences, his businesses (Bliss, Oben HiFi), past tasks, lessons learned. Without memory-core, long-term memory synthesis never runs. Adding memory-core alongside creating MEMORY.md (JOSH-30) gives Heather both the manual persistence and the automated Dreaming synthesis.

**Exact changes to apply (openclaw.json plugins section — GitHub-only):**
```json
"plugins": {
  "allow": [
    "discord",
    "usage-tracker",
    "memory-core"
  ],
  "entries": {
    "discord": { "enabled": true },
    "usage-tracker": { "enabled": true },
    "memory-core": { "enabled": true }
  }
}
```

**Risk level:** LOW (requires OpenClaw 2026.5.x+ to function; safe to add to config now, activates post-upgrade)

---

## Persistent Findings (Carried from June 3 — All Unresolved)

| Finding | Severity | Days Open | Status |
|---------|----------|-----------|--------|
| JOSH-30: MEMORY.md never created | **CRITICAL** | 76 | Unresolved — highest-priority GitHub-only fix |
| JOSH-31: HEARTBEAT.md empty | HIGH | 76 | Unresolved — zero proactive monitoring |
| JOSH-44: Platform 76 days outdated (2026.6.2) | HIGH | 76 | Upgrade target updated |
| JOSH-37: SOUL.md never personalized | MEDIUM | 76 | Unresolved — GitHub-only |
| JOSH-32: Bootstrap TOOLS.md stale | MEDIUM | 76 | Unresolved — GitHub-only |
| JOSH-33: iMessage monitoring paused | MEDIUM | 41 | Hold — wait for 2026.6.2 upgrade, then `doctor --fix` |
| JOSH-34: Emoji contradiction | LOW | 76 | Unresolved |
| JOSH-35: Streaming progress available | INFO | 76 | Post-upgrade opportunity (`"progress"`) |
| JOSH-88: Dreaming activation | HIGH | — | Blocked on upgrade + JOSH-47 |
| JOSH-85: Gemini 3.1-flash-lite prep | MEDIUM | — | GitHub-only, zero risk |

---

## Priority Action Queue

All items 1–7 are GitHub-only (no VPS, no downtime):

| # | Action | Type | Risk |
|---|--------|------|------|
| 1 | Create `workspace/MEMORY.md` stub | GitHub | None |
| 2 | Replace `workspace/HEARTBEAT.md` with email/calendar tasks | GitHub | None |
| 3 | Add compaction + contextPruning to `openclaw.json` | GitHub | None |
| 4 | Tighten Discord security (`groupPolicy`, `dmPolicy`, remove `allowFrom`) | GitHub | Low — test DMs |
| 5 | Add memory-core to `openclaw.json` plugins | GitHub | None |
| 6 | Personalize `workspace/SOUL.md` with Josh/Heather context | GitHub | None |
| 7 | Fix `workspace/TOOLS.md` with actual tool notes | GitHub | None |
| 8 | **Upgrade OpenClaw to 2026.6.2** | VPS | Medium |
| 9 | Run `openclaw doctor --fix` post-upgrade | VPS | Low |

---

## Platform Research Notes (2026-06-06)

- **OpenClaw latest stable:** 2026.6.2 (NEW — target updated from 2026.6.1)
- **2026.6.2 key adds for Josh:** Operator install policy, Discord/iMessage/channel reliability, interrupted tool call recovery, SQLite-backed iMessage state, Skill Workshop, iOS push relay
- **Next expected:** 2026.5.31-stable (Tailscale Serve, Communication Notifications) — still in beta, mid-to-late June
- **AlphaClaw watchdog:** Crash notifications to Discord not yet configured. Self-healing watchdog (`openclaw doctor --fix` auto-run) available in AlphaClaw 0.9.x — configure after upgrade
- **GitHub-only priority unchanged:** JOSH-30 (MEMORY.md creation) remains the single highest-leverage zero-risk action available today
- **New this scan:** JOSH-44 (upgrade target), JOSH-45 (Discord hardening), JOSH-46 (compaction config), JOSH-47 (memory-core plugin)
