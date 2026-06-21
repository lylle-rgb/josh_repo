# Soul Improvements — Heather Schwartz
**Instance:** Josh — personal assistant (Discord/iMessage/email/calendar/contacts)
**Last updated:** 2026-06-21 (evening scan)
**Based on:** Codebase analysis + OpenClaw 2026.6.x research + June 13–21 findings

---

## Status Summary (June 21)

| Rec | Description | Status |
|-----|-------------|--------|
| 1 | Fix emoji contradiction in SOUL.md/AGENTS.md | ✅ RESOLVED (June 17) |
| 2 | Add Josh-specific rules to SOUL.md | ✅ RESOLVED (June 17) |
| 3 | Add personal assistant identity to SOUL.md | ✅ RESOLVED (June 17) |
| 4 | Add memory discipline to SOUL.md | ✅ RESOLVED (June 17) |
| 5 | Add error recovery posture to SOUL.md | ✅ RESOLVED (June 17) |
| 6 | Add heartbeat schedule to AGENTS.md | ✅ RESOLVED (June 17) |
| 7 | Create MEMORY.md | ✅ RESOLVED (June 16) |
| 8 | Enable Dreaming (openclaw.json) | ⏳ Pending — upgrade window now OPEN |
| 9 | HEARTBEAT.md contacts refresh + state JSON | ✅ RESOLVED (June 16) |
| 10 | SOUL.md gateway awareness | ✅ RESOLVED (June 17) |
| 11 | SOUL.md stale connection hygiene | ✅ RESOLVED (June 17) |
| 12 | AGENTS.md model self-check | ✅ RESOLVED (June 17) |
| 13 | Post-2026.6.9 upgrade checklist for Heather (NEW) | 🆕 June 21 |

---

## Context

Most behavioral recs (1–12) were applied to SOUL.md, AGENTS.md, MEMORY.md, and HEARTBEAT.md during the June 16–17 scans. The workspace files are now well-personalized for Josh. The remaining gaps are infrastructure (dreaming, cron, upgrade) rather than soul/personality issues.

Rec 8 (Dreaming) was blocked pending 2026.6.9-stable. That release shipped June 21. The window is now open.

---

## Recommendation 13 — Post-2026.6.9 Upgrade: Heather Behavior Updates (NEW — June 21)

**Priority:** HIGH — apply immediately after Josh upgrades to 2026.6.9
**Why:** Several SOUL.md and TOOLS.md entries reference the old version ceiling and warn about specific bugs that are fixed in 2026.6.9. These will become stale guidance if not updated post-upgrade.

### 13a — Update SOUL.md error recovery section

After upgrading to 2026.6.9, the `## When Things Break` section in SOUL.md should be updated. The current version references version-specific self-healing starting at 2026.6.6. Update to reflect 2026.6.9's improved session history repair:

```markdown
**If a tool or integration fails:**
- Write what you were trying to do to `memory/YYYY-MM-DD.md` before giving up
- Try a graceful fallback before asking Josh
- If stuck, report clearly: what you tried, what failed, what you need from Josh to fix it

**If the gateway restarts or feels degraded:**
- Write what you're doing to `memory/YYYY-MM-DD.md` first, then let the restart happen
- After restart: re-read SOUL.md, USER.md, and today's memory file before responding to anything
- If 3+ restarts in one hour, note it in memory and mention it to Josh
- On OpenClaw 2026.6.9: gateway self-recovers from provider refresh failures and interrupted turns
  now reliably reach a visible final result — silent restarts are expected, not a crisis

**If Discord messages feel echoed or arrive out of order:**
- Stale native hook connection — self-heals on 2026.6.9+
- Do not respond twice to the same message — check if already acknowledged
- If duplicates persist after 30 minutes, note in memory and mention to Josh

**If Google Workspace tools fail:**
- Google Workspace OAuth not yet connected (as of June 2026)
- At morning heartbeat, note the status once — don't repeat-alarm
- Josh can connect at https://5.78.142.81.sslip.io#general
```

### 13b — Update TOOLS.md version block post-upgrade

After upgrading, update the Platform section in TOOLS.md:
```markdown
## Platform
- **OpenClaw version:** 2026.6.9
- **Upgrade path completed:** 2026.3.22 → 2026.5.27 → 2026.6.2 → 2026.6.5 → 2026.6.6 → 2026.6.9
- **Next watch:** Monitor for 2026.7.x releases — no urgency
- **Discord streaming:** Enabled ("progress" mode)
- **Auto-thread titles:** Enabled (60s timeout, 4096-token budget)

## Models (Post-2026.6.9)
- **Primary:** google/gemini-3-flash-preview
- **Fallback 1:** openrouter/google/gemini-3.5-flash
- **Fallback 2:** openrouter/anthropic/claude-haiku-4-5  ← upgraded from claude-3.5-haiku
- **Note:** Google deprecates flash models every 6–9 months — monitor for gemini-3-flash-preview GA or deprecation
```

### 13c — Update MEMORY.md model config section post-upgrade

After upgrading, update MEMORY.md `## Model Configuration` section:
```markdown
## Model Configuration
- **Primary:** google/gemini-3-flash-preview
- **Fallback 1:** openrouter/google/gemini-3.5-flash
- **Fallback 2:** openrouter/anthropic/claude-haiku-4-5 (upgraded from claude-3.5-haiku post-2026.6.9)
- **Platform:** OpenClaw 2026.6.9 (upgraded June 2026)
- **Note:** google/gemini-3-flash-preview is a preview model — watch for GA or deprecation announcement
```

### 13d — Add Discord streaming note to AGENTS.md post-upgrade

After upgrade, add to the `## Tools` section in AGENTS.md:
```markdown
**Discord Streaming:** Enabled as of 2026.6.9. Long responses now stream progressively 
rather than appearing all at once. Users see Heather "typing" in real time.
```

### 13e — SOUL.md continuity note (version-independent)

The current SOUL.md says "On OpenClaw 2026.6.6+: the gateway self-recovers from provider refresh failures." After upgrading to 2026.6.9, update this to remove the version number (it's now a baseline, not a conditional):

```markdown
# Before (remove):
On OpenClaw 2026.6.6+: the gateway self-recovers from provider refresh failures — silent restarts are expected, not a crisis

# After (simpler):
The gateway self-recovers from provider refresh failures — silent restarts are expected, not a crisis
```

---

## Recommendation 8 — Enable Dreaming (Automated Memory Consolidation)

**Priority:** HIGH — upgrade window NOW OPEN as of June 21
**Add to `openclaw.json` under `agents.defaults` (add `userTimezone` first):**
```json
"dreaming": {
  "enabled": true,
  "schedule": "0 3 * * *",
  "maxPromotion": 10,
  "minScore": 0.8,
  "minRecallCount": 3,
  "minUniqueQueries": 3
}
```
Note: `minScore: 0.8` (corrected from 0.7 — see Finding 24 in findings.md)

---

## Priority Order (Updated June 21)

| # | Action | File | Priority | Status |
|---|--------|------|----------|--------|
| 1 | Upgrade to 2026.6.9 (staged, skip 2026.6.8) | VPS shell | HIGH | 🆕 WINDOW OPEN |
| 2 | Add `userTimezone` to openclaw.json (Finding 28 — do first) | openclaw.json | HIGH | ⏳ Pending |
| 3 | Enable Dreaming (Rec 8 — corrected config) | openclaw.json | HIGH | ⏳ Pending |
| 4 | Add compaction/memoryFlush | openclaw.json | HIGH | ⏳ Pending |
| 5 | Add heartbeat cron job | openclaw.json | HIGH | ⏳ Pending |
| 6 | Connect Google Workspace OAuth | AlphaClaw UI | CRITICAL | ⏳ Day 91 |
| 7 | Post-upgrade: update fallback 2 → claude-haiku-4-5 (Rec 13b) | openclaw.json | MEDIUM | After upgrade |
| 8 | Post-upgrade: enable Discord streaming (Rec 13b) | openclaw.json | LOW | After upgrade |
| 9 | Post-upgrade: update SOUL.md version refs (Rec 13a/13e) | workspace/SOUL.md | LOW | After upgrade |
| 10 | Post-upgrade: update TOOLS.md version block (Rec 13b) | workspace/TOOLS.md | LOW | After upgrade |
| 11 | Post-upgrade: update MEMORY.md model config (Rec 13c) | workspace/MEMORY.md | LOW | After upgrade |
| 12 | Post-upgrade: add streaming note to AGENTS.md (Rec 13d) | workspace/AGENTS.md | LOW | After upgrade |
| 13 | Tighten Discord allowFrom (Finding 20) | openclaw.json | MEDIUM-HIGH | ⏳ Pending |
