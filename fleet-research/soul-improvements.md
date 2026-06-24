# Soul Improvements — Heather Schwartz
**Instance:** Josh — personal assistant (Discord/iMessage/email/calendar/contacts)
**Last updated:** 2026-06-24 (evening scan — Rec 17-18 added)
**Based on:** Codebase analysis + OpenClaw 2026.6.x research + June 13–24 findings

---

## Status Summary (June 24)

| Rec | Description | Status |
|-----|-------------|--------|
| 1 | Fix emoji contradiction in SOUL.md/AGENTS.md | ✅ RESOLVED (June 17) |
| 2 | Add Josh-specific rules to SOUL.md | ✅ RESOLVED (June 17) |
| 3 | Add personal assistant identity to SOUL.md | ✅ RESOLVED (June 17) |
| 4 | Add memory discipline to SOUL.md | ✅ RESOLVED (June 17) |
| 5 | Add error recovery posture to SOUL.md | ✅ RESOLVED (June 17) |
| 6 | Add heartbeat schedule to AGENTS.md | ✅ RESOLVED (June 17) |
| 7 | Create MEMORY.md | ✅ RESOLVED (June 16) |
| 8 | Enable Dreaming (openclaw.json) | ⏳ Pending — upgrade window open Day 5 |
| 9 | HEARTBEAT.md contacts refresh + state JSON | ✅ RESOLVED (June 16) |
| 10 | SOUL.md gateway awareness | ✅ RESOLVED (June 17) |
| 11 | SOUL.md stale connection hygiene | ✅ RESOLVED (June 17) |
| 12 | AGENTS.md model self-check | ✅ RESOLVED (June 17) |
| 13 | Post-2026.6.9 upgrade checklist for Heather | ⏳ June 21 — pending upgrade |
| 14 | Post-upgrade: Discord Components V2 behavioral guidance | ⏳ June 23 — pending upgrade |
| 15 | HEARTBEAT.md cron-not-deployed warning | ✅ RESOLVED June 23 |
| 16 | MEMORY.md day count staleness | ✅ RESOLVED June 23 |
| 17 | Add monthly model health check to HEARTBEAT.md | 🆕 Added June 24 — apply anytime |
| 18 | SOUL.md: add silent model failure awareness | 🆕 Added June 24 — apply anytime |

---

## Context

Most behavioral recs (1–12, 15–16) were applied during the June 16–23 scans. The workspace files
are well-personalized for Josh. The primary remaining gaps are infrastructure (dreaming, cron, upgrade)
rather than soul/personality issues.

Recs 17–18 were added June 24 evening in response to the Gemini deprecation wave (F42/F43). They
address a behavioral gap: Heather has no protocol for detecting or reporting silent model failover,
and no recurring check to catch upcoming deprecations before they hit.

---

## Recommendation 18 — SOUL.md: Silent Model Failure Awareness (NEW — June 24)

**Priority:** LOW to MEDIUM — apply anytime via Browse tab
**Why:** SOUL.md has no guidance for when Heather's primary model silently fails over to a fallback.
The Gemini deprecation wave (F42/F43) makes this a real risk. Without this awareness, Heather
doesn't know she's running degraded and Josh gets no notification.

### Add to `workspace/SOUL.md` under `## When Things Break`:

```markdown
**If responses feel slower or quality seems lower than usual:**
- This may mean the primary model was deprecated and you're running on a fallback
- Check MEMORY.md model config to confirm what the expected primary model is
- Verify the primary model isn't deprecated: https://ai.google.dev/gemini-api/docs/deprecations
- If fallback is running: notify Josh with which model failed and which you're now using
- Update MEMORY.md with what you observe — don't silently absorb a degraded state
- OpenClaw does not alert on primary-model silent failover — diagnose this proactively
```

**Risk:** LOW. Behavioral guidance only. Apply via AlphaClaw Browse tab — no upgrade needed.

---

## Recommendation 17 — Add Monthly Model Health Check to HEARTBEAT.md (NEW — June 24)

**Priority:** MEDIUM — apply now (no upgrade needed)
**Why:** Google retires Gemini preview models on a rolling schedule with minimal notice and no
notification to running agents. Two sister models to Heather's primary shut down June 25. A monthly
check prevents silent model degradation from going undetected for weeks.

### Add to `workspace/HEARTBEAT.md` as a new "Monthly" section:

```markdown
## Monthly: Model Health Check
- Check https://ai.google.dev/gemini-api/docs/deprecations — look for current primary model
  (gemini-3-flash-preview or its successor)
- If listed with shutdown date: flag to Josh immediately with migration target
  (gemini-3.5-flash stable is the safe GA target)
- Check OpenRouter provider status for any degraded endpoints in the fallback chain
- Send Josh a brief Discord DM with findings — even if all-clear (confirms the check ran)
- Update MEMORY.md model config section if any change is needed
```

**Risk:** LOW. No upgrade needed; edit HEARTBEAT.md via AlphaClaw Browse tab today.

---

## Recommendation 14 — Post-Upgrade: Discord Components V2 Behavioral Guidance (June 23)

**Priority:** LOW — apply immediately after Josh upgrades to 2026.6.9

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
- **Primary:** google/gemini-3.5-flash  ← migrated from preview (F42/F43)
- **Fallback 1:** openrouter/anthropic/claude-haiku-4-5  ← cross-provider first
- **Fallback 2:** openrouter/google/gemini-3.5-flash
```

### 13c — Update MEMORY.md model config section post-upgrade

```markdown
## Model Configuration
- **Primary:** google/gemini-3.5-flash (migrated from gemini-3-flash-preview — F42/F43)
- **Fallback 1:** openrouter/anthropic/claude-haiku-4-5 (upgraded + reordered post-2026.6.9)
- **Fallback 2:** openrouter/google/gemini-3.5-flash
- **Platform:** OpenClaw 2026.6.9 (upgraded June 2026)
```

### 13d — Add Discord streaming note to AGENTS.md post-upgrade

```markdown
**Discord Streaming (2026.6.9+):** Long responses now stream progressively.
Users see Heather typing in real time rather than waiting for a full response.
```

### 13e — Simplify SOUL.md version reference post-upgrade

Replace: `On OpenClaw 2026.6.6+: the gateway self-recovers from provider refresh failures`

With: `The gateway self-recovers from provider refresh failures`

---

## Recommendation 8 — Enable Dreaming (Automated Memory Consolidation)

**Priority:** HIGH — upgrade window OPEN (Day 5 as of June 24)

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

## Priority Order (Updated June 24)

| # | Action | File | Priority | Status |
|---|--------|------|----------|--------|
| 0 | Check gemini-3-flash-preview deprecation TONIGHT (F43) | AlphaClaw Browse | CRITICAL | ⏳ Act TONIGHT |
| 1 | Upgrade to 2026.6.9 (staged, skip 2026.6.8) | VPS shell | HIGH | ⏳ Window open Day 5 |
| 2 | Add `userTimezone` to openclaw.json (FIRST) | openclaw.json | HIGH | ⏳ Pending |
| 3 | Enable Dreaming (verify key path first) | openclaw.json | HIGH | ⏳ Pending |
| 4 | Add compaction/memoryFlush block | openclaw.json | HIGH | ⏳ Pending |
| 5 | Add heartbeat cron job | openclaw.json | HIGH | ⏳ Pending |
| 6 | Connect Google Workspace OAuth | AlphaClaw UI | CRITICAL | ⏳ Day 95 |
| 7 | Apply Rec 17: monthly model health check | HEARTBEAT.md | MEDIUM | 🆕 Anytime via Browse tab |
| 8 | Apply Rec 18: silent model failure awareness | SOUL.md | MEDIUM | 🆕 Anytime via Browse tab |
| 9 | Post-upgrade: Components V2 guidance (Rec 14a/14b) | SOUL.md + AGENTS.md | LOW | After upgrade |
| 10 | Post-upgrade: fallback chain reorder (Rec 13b / F31/F43) | openclaw.json | MEDIUM | After upgrade |
| 11 | Post-upgrade: enable Discord streaming (Rec 13b) | openclaw.json | LOW | After upgrade |
| 12 | Post-upgrade: update SOUL.md error recovery (Rec 13a/13e) | workspace/SOUL.md | LOW | After upgrade |
| 13 | Post-upgrade: update TOOLS.md version block (Rec 13b) | workspace/TOOLS.md | LOW | After upgrade |
| 14 | Post-upgrade: update MEMORY.md model config (Rec 13c) | workspace/MEMORY.md | LOW | After upgrade |
| 15 | Post-upgrade: add streaming note to AGENTS.md (Rec 13d) | workspace/AGENTS.md | LOW | After upgrade |
| 16 | Tighten Discord allowFrom (Finding 20) | openclaw.json | MEDIUM-HIGH | ⏳ After upgrade |
