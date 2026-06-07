# Fleet Findings — Josh (Heather Schwartz) | Current State

**Last updated:** 2026-06-07 (Morning Scan)
**Instance:** Josh Meyers — Heather Schwartz (personal assistant)
**Repo:** lylle-rgb/josh_repo

> For full dated reports, see the dated files in this directory.
> Latest detailed scan: `2026-06-07-morning-findings.md`

---

## Platform Status (as of 2026-06-07)

| Item | Current | Latest Stable | Gap |
|------|---------|---------------|-----|
| OpenClaw | 2026.3.22 | **2026.6.2** | **77 days behind — CRITICAL** |
| AlphaClaw | Unknown | **0.9.18** | 2 releases ahead of prior analysis |
| Primary model | google/gemini-3-flash-preview | — | Active (verify AIza key) |
| iMessage | Paused | Fix in 2026.6.1+ SQLite migration | 77+ days paused |
| Google Workspace | **NOT CONNECTED** | — | CRITICAL — core tools inaccessible |

---

## 🔴 Critical / High Priority (Active)

### JOSH-30 | MEMORY.md Never Created — Day 77+
**Severity:** CRITICAL | **Fix:** GitHub file create

Heather has run for 77 days with no long-term memory file. This is the single highest-ROI fix — zero cost, immediate value.

**Fix:** Create `workspace/MEMORY.md`.

---

### JOSH-31 | HEARTBEAT.md Empty — Day 77+
**Severity:** HIGH | **Fix:** GitHub file replace

No proactive monitoring tasks configured. Heather has never autonomously checked Josh's inbox or calendar.

---

### JOSH-33 | iMessage Paused — Fix in OpenClaw 2026.6.1+ (SQLite Migration)
**Severity:** MEDIUM | **Fix:** Upgrade + `openclaw doctor --fix`

iMessage paused due to malformed JSON / duplicate key in `inbox-state.json`. OpenClaw 2026.6.1 migrates to SQLite — auto-cleans on upgrade.

---

### JOSH-34 | Emoji Contradiction — AGENTS.md vs USER.md
**Severity:** MEDIUM | **Fix:** GitHub file edit

USER.md: `STRICT: DO NOT SEND EMOJI REACTIONS TO MESSAGES.`
AGENTS.md (stock template): "React Like a Human!" encouraging emoji reactions. Direct contradiction every session.

**Fix:** Add Josh override block at top of `workspace/AGENTS.md`.

---

### JOSH-37 | SOUL.md Never Personalized — Day 77+
**Severity:** MEDIUM | **Fix:** GitHub file replace

SOUL.md is identical to Noah's (stock template). No Josh/Heather context.

---

### JOSH-39 | Upgrade Target: OpenClaw 2026.6.2
**Severity:** HIGH | **Fix:** VPS upgrade

Josh is 77 days behind. Current stable: **2026.6.2**.

Key additions vs 2026.3.22:
- **iMessage SQLite migration** (2026.6.1) — auto-cleans malformed inbox-state.json
- **Gateway startup caching** (2026.5.22) — /models latency 20s → 5ms
- **Skill Workshop** (2026.6.1) — install skills from Control UI without CLI
- **defineToolPlugin SDK** (2026.5.18) — custom typed tool plugins
- **SQLite-backed state, memory QMD improvements, runtime recovery**
- Security: group prompt isolation, tightened Discord wake matching
- Discord: self-reply echo suppression, voice session follow

**Fix:** Upgrade via AlphaClaw to OpenClaw 2026.6.2. Run `openclaw doctor --fix` after upgrade.

---

### JOSH-44 | Google Workspace Not Connected — Day 77+
**Severity:** CRITICAL | **Fix:** VPS setup via AlphaClaw UI

Gmail, Calendar, and Contacts are inaccessible. 70-80% of Heather's intended tasks impossible.

**Fix:** Connect via AlphaClaw UI → General tab → Google Workspace. Authorize Gmail, Calendar, Contacts.

---

### JOSH-45 | Discord Security Over-Open
**Severity:** MEDIUM | **Fix:** GitHub JSON edit

`groupPolicy: "open"`, `dmPolicy: "open"`. Any Discord user can DM Heather and access Josh's data.

**Fix:** Set `dmPolicy: "pairing"` to match Noah's security posture.

---

### JOSH-46 | No Compaction / contextPruning Config
**Severity:** MEDIUM | **Fix:** GitHub JSON edit

Noah has `reserveTokensFloor: 40000` + `memoryFlush`. Josh has nothing.

**Fix in `openclaw.json` under `agents.defaults`:**
```json
"compaction": {
  "reserveTokensFloor": 40000,
  "memoryFlush": { "enabled": true, "softThresholdTokens": 4000 }
},
"contextPruning": {
  "mode": "cache-ttl",
  "ttl": "30m"
}
```

---

### JOSH-47 | memory-core Plugin Not Configured (Including Slots + Dreaming)
**Severity:** MEDIUM | **Fix:** GitHub JSON edit + post-upgrade install

memory-core is absent entirely from Josh's config. Also need `plugins.slots.memory: "memory-core"` for proper activation.

**Fix in `openclaw.json`:**
```json
"plugins": {
  "slots": { "memory": "memory-core" },
  "allow": ["discord", "usage-tracker", "memory-core"],
  "entries": {
    "memory-core": {
      "enabled": true,
      "subagent": { "allowModelOverride": true },
      "config": {
        "dreaming": {
          "enabled": true,
          "frequency": "0 3 * * *",
          "model": "google/gemini-3-flash-preview"
        }
      }
    }
  }
}
```

---

### JOSH-48 | Gemini OAuth Warning — Verify AIza Key
**Severity:** HIGH | **Fix:** VPS env var check

Verify `GOOGLE_API_KEY` starts with `AIza`, NOT `ya29` (OAuth access token triggers 403 TOS violations).

---

### JOSH-50 | Dead OpenRouter Fallback
**Severity:** MEDIUM | **Fix:** GitHub file edit

`openrouter/anthropic/claude-3.5-haiku` present but OpenRouter not configured. 30-second timeout risk.

**Fix:** Remove from `agents.defaults.model.fallbacks`.

---

### JOSH-55 | TOOLS.md Never Populated — Day 77+
**Severity:** MEDIUM | **Fix:** GitHub file replace

Stock template. No Google auth details, no Discord guild info, no iMessage status.

---

### JOSH-63 | BOOTSTRAP.md Stale — Day 77+
**Severity:** MEDIUM | **Fix:** GitHub file delete

---

## New Findings (2026-06-07 Morning)

### JOSH-NEW-01 | AlphaClaw 0.9.18 Available
Latest AlphaClaw: **0.9.18** (released June 1, 2026). Prior analysis referenced 0.9.16. Includes watchdog improvements and multi-agent management.

### JOSH-NEW-02 | gog-cli Skill Not Installed — Google Workspace CLI Missing
**Severity:** HIGH

`gog-cli` is the OpenClaw Google Workspace skill (Gmail, Calendar, Drive, Contacts). Noah has it; Josh doesn't. Without it, even with Google OAuth connected, natural-language Workspace commands won't work.

**Fix post-upgrade:** `openclaw skills install gog`

### JOSH-NEW-03 | memory-core Dreaming — Nightly Memory Consolidation
Nightly cron at 3 AM consolidates session memory into long-term memory. Requires `plugins.slots.memory: "memory-core"` + dreaming config. See JOSH-47 for full config.

**Note:** memory-core uses OpenAI embeddings by default — plan for `OPENAI_API_KEY` on VPS.

### JOSH-NEW-04 | Gateway Startup Caching — Performance Win on Upgrade
Upgrading to 2026.6.2 auto-enables Gateway caching. /models latency drops from 20s to 5ms.

### JOSH-NEW-05 | Discord Streaming — Enable "progress" Mode
Change `"streaming": "off"` → `"streaming": "progress"` in `openclaw.json` channels.discord. Adds typing indicators in Discord. Low risk, requires restart.

### JOSH-NEW-06 | Subagent Spawning Available
Community tip (May 2026): spawn subagents with Gemini 3.1 Pro, Opus 4.6. Enables delegating expensive tasks (email triage, research) while staying responsive. Available post-upgrade.

---

## Immediate Action Checklist

**GitHub only (no VPS access required):**
- [ ] Create `workspace/MEMORY.md` **(CRITICAL — Day 77)**
- [ ] Replace `workspace/HEARTBEAT.md` with email/calendar monitoring tasks
- [ ] Replace `workspace/SOUL.md` with Josh-specific version
- [ ] Add Josh emoji override to `workspace/AGENTS.md`
- [ ] Replace `workspace/TOOLS.md` with environment-specific content
- [ ] Delete `workspace/BOOTSTRAP.md`
- [ ] Fix `openclaw.json`: add compaction + contextPruning (30m)
- [ ] Fix `openclaw.json`: add memory-core slots + entries + dreaming
- [ ] Fix `openclaw.json`: enable `"streaming": "progress"` in discord channel
- [ ] Fix `openclaw.json`: remove dead OpenRouter fallback
- [ ] Fix `openclaw.json`: harden Discord `dmPolicy` to `"pairing"`

**VPS access required:**
- [ ] Verify `GOOGLE_API_KEY` starts with `AIza` (not `ya29`)
- [ ] Connect Google Workspace via AlphaClaw UI → General tab
- [ ] Upgrade OpenClaw to **2026.6.2**
- [ ] Run `openclaw doctor --fix` after upgrade (SQLite migration, iMessage resume)
- [ ] Install gog-cli: `openclaw skills install gog`
- [ ] Install Memory Core from Skill Workshop
- [ ] Update AlphaClaw to 0.9.18

---

## Finding History

Scans run since: 2026-05-12. Most recent: 2026-06-07 morning.
