# Soul Improvements — Heather Schwartz
**Instance:** Josh — personal assistant (Discord/iMessage/email/calendar/contacts)
**Last updated:** 2026-06-23 (evening scan)
**Based on:** Codebase analysis + OpenClaw 2026.6.x research + June 13–23 findings

---

## Status Summary (June 23)

| Rec | Description | Status |
|-----|-------------|--------|
| 1 | Fix emoji contradiction in SOUL.md/AGENTS.md | ✅ RESOLVED (June 17) |
| 2 | Add Josh-specific rules to SOUL.md | ✅ RESOLVED (June 17) |
| 3 | Add personal assistant identity to SOUL.md | ✅ RESOLVED (June 17) |
| 4 | Add memory discipline to SOUL.md | ✅ RESOLVED (June 17) |
| 5 | Add error recovery posture to SOUL.md | ✅ RESOLVED (June 17) |
| 6 | Add heartbeat schedule to AGENTS.md | ✅ RESOLVED (June 17) |
| 7 | Create MEMORY.md | ✅ RESOLVED (June 16) |
| 8 | Enable Dreaming (openclaw.json) | ⏳ Pending — upgrade window open Day 3 |
| 9 | HEARTBEAT.md contacts refresh + state JSON | ✅ RESOLVED (June 16) |
| 10 | SOUL.md gateway awareness | ✅ RESOLVED (June 17) |
| 11 | SOUL.md stale connection hygiene | ✅ RESOLVED (June 17) |
| 12 | AGENTS.md model self-check | ✅ RESOLVED (June 17) |
| 13 | Post-2026.6.9 upgrade checklist for Heather | ⏳ June 21 — pending upgrade |
| 14 | Post-upgrade: Discord Components V2 behavioral guidance | ⏳ June 23 — pending upgrade |
| 15 | HEARTBEAT.md cron-not-deployed warning | ✅ RESOLVED June 23 |
| 16 | MEMORY.md day count staleness | ✅ RESOLVED June 23 |

---

## Context

Most behavioral recs (1–12) were applied during the June 16–17 scans. The workspace files are well-personalized for Josh. Remaining gaps are infrastructure (dreaming, cron, upgrade) rather than soul/personality issues.

Rec 15 (HEARTBEAT.md cron warning) and Rec 16 (MEMORY.md day counts) were applied in the June 23 evening scan.

Recs 13 and 14 are post-upgrade items — apply after Josh completes the 2026.6.9 upgrade.

---

## Recommendation 14 — Post-Upgrade: Discord Components V2 Behavioral Guidance (NEW — June 23)

**Priority:** LOW — apply immediately after Josh upgrades to 2026.6.9
**Why:** 2026.6.9 brings full Discord Components V2 support. Heather can offer native button confirmations, select menus, and modals. Directly serves Josh's "ask before acting externally" preference in SOUL.md — a button confirmation is cleaner and less disruptive than a text question.

### 14a — Add to `workspace/SOUL.md` `## Boundaries` section post-upgrade

```
**Interactive confirmations (post-2026.6.9):** For external actions (sending emails, creating
calendar events, posting content), prefer a Discord button prompt over a text question. Use
a Confirm / Cancel button pair so Josh can respond with one tap. Only use buttons when an
action can actually be cancelled — don't add confirmation friction to time-sensitive sends.
```

### 14b — Add to `workspace/AGENTS.md` `## Tools` section post-upgrade

```
**Discord Interactive Components (2026.6.9+):**
- Buttons for action confirmations (send email, create event, post content)
- Select menus for option selection (which calendar? which contact?)
- Modals for multi-field input (draft new event details)
- Use sparingly — one clear prompt beats a cascade of menus
```

**Risk:** LOW — post-upgrade behavioral guidance. No immediate action.

---

## Recommendation 13 — Post-2026.6.9 Upgrade: Heather Behavior Updates (June 21)

**Priority:** HIGH — apply immediately after Josh upgrades to 2026.6.9

### 13a — Update SOUL.md error recovery section

```markdown
**If the gateway restarts or feels degraded:**
- Write what you're doing to `memory/YYYY-MM-DD.md` first, then let the restart happen
- After restart: re-read SOUL.md, USER.md, and today's memory file before responding
- If 3+ restarts in one hour, note it in memory and mention it to Josh
- On OpenClaw 2026.6.9: gateway self-recovers from provider refresh failures and interrupted
  turns now reliably reach a visible final result — silent restarts are expected, not a crisis

**If Discord messages feel echoed or arrive out of order:**
- Self-heals on 2026.6.9+ — do not respond twice to the same message
- If duplicates persist after 30 minutes, note in memory and mention to Josh
```

### 13b — Update TOOLS.md version block post-upgrade

```markdown
## Platform
- **OpenClaw version:** 2026.6.9
- **Upgrade path completed:** 2026.3.22 → 2026.5.27 → 2026.6.2 → 2026.6.5 → 2026.6.6 → 2026.6.9
- **Next watch:** Monitor for 2026.6.10-stable (beta track active) — no urgency
- **Discord streaming:** Enabled ("progress" mode)
- **Auto-thread titles:** Enabled (60s timeout, 4096-token budget)
- **Discord Components V2:** Enabled — buttons, select menus, modals available

## Models (Post-2026.6.9)
- **Primary:** google/gemini-3-flash-preview
- **Fallback 1:** openrouter/anthropic/claude-haiku-4-5  ← cross-provider first
- **Fallback 2:** openrouter/google/gemini-3.5-flash
```

### 13c — Update MEMORY.md model config section post-upgrade

```markdown
## Model Configuration
- **Primary:** google/gemini-3-flash-preview
- **Fallback 1:** openrouter/anthropic/claude-haiku-4-5 (upgraded + reordered post-2026.6.9)
- **Fallback 2:** openrouter/google/gemini-3.5-flash
- **Platform:** OpenClaw 2026.6.9 (upgraded June 2026)
- **Note:** gemini-3-flash-preview is a preview model — watch for GA or deprecation
```

### 13d — Add Discord streaming note to AGENTS.md post-upgrade

```markdown
**Discord Streaming (2026.6.9+):** Long responses now stream progressively.
Users see Heather typing in real time rather than waiting for a full response.
```

### 13e — Simplify SOUL.md version reference post-upgrade

Replace: `On OpenClaw 2026.6.6+: the gateway self-recovers from provider refresh failures — silent restarts are expected, not a crisis`

With: `The gateway self-recovers from provider refresh failures — silent restarts are expected, not a crisis`

---

## Recommendation 8 — Enable Dreaming (Automated Memory Consolidation)

**Priority:** HIGH — upgrade window OPEN (Day 3 as of June 23)

Add to `openclaw.json` under `agents.defaults` (add `userTimezone` first; verify key path per Finding 36):
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

---

## Priority Order (Updated June 23)

| # | Action | File | Priority | Status |
|---|--------|------|----------|--------|
| 1 | Upgrade to 2026.6.9 (staged, skip 2026.6.8) | VPS shell | HIGH | ⏳ Window open Day 3 |
| 2 | Add `userTimezone` to openclaw.json (FIRST) | openclaw.json | HIGH | ⏳ Pending |
| 3 | Enable Dreaming (verify key path first) | openclaw.json | HIGH | ⏳ Pending |
| 4 | Add compaction/memoryFlush block | openclaw.json | HIGH | ⏳ Pending |
| 5 | Add heartbeat cron job | openclaw.json | HIGH | ⏳ Pending |
| 6 | Connect Google Workspace OAuth | AlphaClaw UI | CRITICAL | ⏳ Day 93 |
| 7 | Post-upgrade: Components V2 guidance (Rec 14a/14b) | SOUL.md + AGENTS.md | LOW | After upgrade |
| 8 | Post-upgrade: fallback chain reorder (Rec 13b / F31) | openclaw.json | MEDIUM | After upgrade |
| 9 | Post-upgrade: enable Discord streaming (Rec 13b) | openclaw.json | LOW | After upgrade |
| 10 | Post-upgrade: update SOUL.md error recovery (Rec 13a/13e) | workspace/SOUL.md | LOW | After upgrade |
| 11 | Post-upgrade: update TOOLS.md version block (Rec 13b) | workspace/TOOLS.md | LOW | After upgrade |
| 12 | Post-upgrade: update MEMORY.md model config (Rec 13c) | workspace/MEMORY.md | LOW | After upgrade |
| 13 | Post-upgrade: add streaming note to AGENTS.md (Rec 13d) | workspace/AGENTS.md | LOW | After upgrade |
| 14 | Tighten Discord allowFrom (Finding 20) | openclaw.json | MEDIUM-HIGH | ⏳ Pending |
