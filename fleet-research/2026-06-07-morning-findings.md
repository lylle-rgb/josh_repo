# Findings — Josh (Heather Schwartz) | 2026-06-07 Morning Scan

**Scan date:** 2026-06-07 (morning)
**Instance:** Josh Meyers — Heather Schwartz (personal assistant)
**Repo:** lylle-rgb/josh_repo
**Previous scan:** 2026-06-06 morning (cross-customer analysis)

---

## Platform Status

| Item | Current | Latest | Gap |
|------|---------|--------|-----|
| OpenClaw | 2026.3.22 | **2026.6.2** | **77 days — CRITICAL** |
| AlphaClaw | Unknown | **0.9.18** | Was listed as 0.9.16 — 2 more releases |
| Primary model | google/gemini-3-flash-preview | — | Active |
| iMessage | Paused | Fix via SQLite migration (2026.6.1+) | Day 77 |
| Google Workspace | NOT CONNECTED | — | CRITICAL |

---

## New Findings (June 7 Morning)

### JOSH-NEW-01 | AlphaClaw Updated to 0.9.18 (Was 0.9.16)
**Severity:** LOW | **Type:** Platform update

AlphaClaw has released 2 additional versions since the June 6 scan. Latest is **0.9.18** (released June 1, 2026). Features confirmed:
- Password-protected web dashboard with onboarding/config/management flows
- Watchdog: crash detection, crash-loop recovery, auto-repair with notification channel
- Multi-agent sidebar navigation (create, rename, delete flows)
- Automatic hourly workspace commits to GitHub
- Per-agent Telegram / Discord / Slack channel bindings
- Anti-drift bootstrap prompt hardening

**Action:** Check AlphaClaw version in Control UI after VPS upgrade. Update via AlphaClaw's self-update mechanism.

---

### JOSH-NEW-02 | gog-cli (Google Workspace CLI) Not Installed — HIGH
**Severity:** HIGH | **Type:** Missing capability

`gog-cli` is the OpenClaw Google Workspace skill — it provides Gmail, Calendar, Drive, Contacts, Sheets, and Docs integration via natural-language commands. **Noah's instance has it installed** (`skills/gog-cli`). Josh's instance — whose primary job is managing Gmail and Calendar — does not have it.

This is the skill layer that connects Google OAuth credentials to actual OpenClaw tool calls. Without it, even a configured Google API key cannot drive Gmail or Calendar tasks.

**Fix:** After OpenClaw upgrade to 2026.6.2, install:
```
openclaw skills install gog
```
Then authorize via AlphaClaw UI → Google Workspace → Desktop OAuth.

**Risk:** LOW (additive install, no existing tools affected)

---

### JOSH-NEW-03 | memory-core "Dreaming" + Slots Config — Full Config Now Documented
**Severity:** MEDIUM | **Type:** Config improvement (GitHub-only after memory-core install)

The `memory-core` plugin includes a **dreaming** feature: a nightly cron that consolidates daily session memory into long-term memory using a configured model. For Heather, this means accumulated context about Josh — preferences, decisions, patterns — persists across sessions automatically.

**Critical detail:** `plugins.slots.memory: "memory-core"` is the official activation key. The `entries` config alone is insufficient — without the `slots` key, memory-core is not engaged as the memory engine.

**Full config for `openclaw.json` after memory-core install:**
```json
"plugins": {
  "slots": {
    "memory": "memory-core"
  },
  "entries": {
    "memory-core": {
      "enabled": true,
      "subagent": {
        "allowModelOverride": true
      },
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

**Note on embeddings:** memory-core defaults to OpenAI embeddings. Plan for `OPENAI_API_KEY` on the VPS (or configure Gemini as the embedding provider) when installing.

**Risk:** LOW (config-only until install; install requires 2026.5.17+)

---

### JOSH-NEW-04 | Gateway Startup Caching — /models Latency 20s vs 5ms
**Severity:** MEDIUM | **Type:** Performance gap (auto-resolved on upgrade)

Since OpenClaw 2026.5.22, the Gateway caches provider auth state, plugin metadata, and channel catalogs so `/models` calls drop from ~20 seconds to ~5 milliseconds. Josh is on 2026.3.22 and does not have this — every session startup pays a 20-second model-resolution penalty.

**Fix:** Upgrade to 2026.6.2. No config change needed — caching is automatic post-upgrade.

---

### JOSH-NEW-05 | Discord Streaming — Enable "progress" Mode
**Severity:** LOW | **Type:** User experience improvement

Josh's `openclaw.json` has `"streaming": "off"`. Since 2026.5.x, `"streaming": "progress"` provides real-time typing indicators in Discord without sending intermediate messages — Heather feels more responsive.

**Fix in `openclaw.json` under `channels.discord`:**
```json
"streaming": "progress"
```
**Risk:** LOW. Requires restart.

---

### JOSH-NEW-06 | Community Tip: Subagent Spawning
**Severity:** LOW | **Type:** Capability opportunity

Community tip from @boringmarketer (May 2026):
> "Setup the ability to spawn subagents. I use Gemini 3.1 Pro, Opus 4.6, Kimi k2.5."

Subagent spawning lets Heather delegate expensive tasks (deep email triage, calendar planning, research) to a dedicated subagent model while staying responsive in Discord. Available in 2026.6.2.

---

## Persisting Critical Issues (from prior scans — still unresolved)

All issues from the June 6 cross-customer analysis remain open:

| ID | Issue | Days Open |
|----|-------|----------|
| JOSH-30 | MEMORY.md never created | **Day 77** |
| JOSH-31 | HEARTBEAT.md empty — no monitoring | **Day 77** |
| JOSH-44 | Google Workspace not connected | **Day 77** |
| JOSH-33 | iMessage paused (malformed JSON) | **Day 77** |
| JOSH-39 | OpenClaw 77 days behind (target: 2026.6.2) | **Day 77** |
| JOSH-34 | Emoji contradiction AGENTS.md vs USER.md | Day 77 |
| JOSH-45 | Discord security over-open (open DM + group) | Day 77 |
| JOSH-46 | No compaction / contextPruning config | Day 77 |
| JOSH-47 | memory-core not configured | Day 77 |
| JOSH-48 | Gemini OAuth — verify AIza key | Day 77 |
| JOSH-50 | Dead OpenRouter fallback | Day 77 |
| JOSH-55 | TOOLS.md empty template | Day 77 |
| JOSH-63 | BOOTSTRAP.md stale | Day 77 |
| JOSH-37 | SOUL.md generic template | Day 77 |

---

## Immediate Action Checklist (June 7 Morning)

**GitHub only (no VPS, no restart):**
- [ ] Create `workspace/MEMORY.md` stub **(JOSH-30 — CRITICAL, Day 77)**
- [ ] Replace `workspace/HEARTBEAT.md` with email/calendar monitoring tasks (JOSH-31)
- [ ] Replace `workspace/SOUL.md` with Josh-specific version (JOSH-37)
- [ ] Add Josh emoji override block to `workspace/AGENTS.md` (JOSH-34)
- [ ] Replace `workspace/TOOLS.md` with environment-specific content (JOSH-55)
- [ ] Delete `workspace/BOOTSTRAP.md` (JOSH-63)
- [ ] Fix `openclaw.json`: add compaction + contextPruning 30m (JOSH-46)
- [ ] Fix `openclaw.json`: add memory-core slots + entries + dreaming (JOSH-47/NEW-03)
- [ ] Fix `openclaw.json`: enable `"streaming": "progress"` in discord channel (JOSH-NEW-05)
- [ ] Fix `openclaw.json`: remove dead OpenRouter fallback (JOSH-50)
- [ ] Fix `openclaw.json`: harden Discord `dmPolicy` to `"pairing"` (JOSH-45)

**VPS access required:**
- [ ] Verify `GOOGLE_API_KEY` starts with `AIza` (not `ya29`) (JOSH-48)
- [ ] Connect Google Workspace via AlphaClaw UI → General tab (JOSH-44)
- [ ] Upgrade OpenClaw to **2026.6.2** (JOSH-39)
- [ ] Run `openclaw doctor --fix` after upgrade (SQLite migration, iMessage resume)
- [ ] Install gog-cli: `openclaw skills install gog` (JOSH-NEW-02)
- [ ] Install Memory Core from Skill Workshop post-upgrade (JOSH-47)
- [ ] Update AlphaClaw to 0.9.18 (JOSH-NEW-01)

---

*Scan completed: 2026-06-07 morning by AlphaClaw Fleet Research daemon.*
