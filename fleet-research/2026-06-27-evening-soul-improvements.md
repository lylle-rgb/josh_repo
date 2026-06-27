# Soul Improvements: Josh (Heather) — Evening Scan
**Date:** 2026-06-27 | **Scan type:** Evening | **Agent:** AlphaClaw Fleet Research

## Overview
SOUL.md, AGENTS.md, and TOOLS.md are in good shape. Josh's core preferences (no emoji, no filler, concise)
are consistently enforced across all files. The main gaps are stale version references, a missing explicit
Day-100 escalation rule, and some low-value noise in AGENTS.md. MEMORY.md and HEARTBEAT.md have been
updated in this scan (day counts incremented, new finding added).

---

## Improvement 1 — SOUL.md: Update Version References (LOW PRIORITY)
**File:** `workspace/SOUL.md`
**Section:** "When Things Break"
**Issue:** Two references to "2026.6.6+" for self-recovery and Discord duplicate-handling behaviors.
Heather is at 2026.3.22 and won't have these behaviors until she reaches 2026.6.10. The "2026.6.6+"
reference is technically accurate (the features landed in 2026.6.6) but creates false confidence — Heather
might believe she has self-healing behavior she doesn't actually have yet.

**Current text:**
```
- On OpenClaw 2026.6.6+: the gateway self-recovers from provider refresh failures — silent restarts are expected, not a crisis
...
- Could be a stale native hook connection — self-heals on 2026.6.6+
```

**Recommended change:**
```
- On OpenClaw 2026.6.6+ (current upgrade target: 2026.6.10): the gateway self-recovers from provider refresh failures — silent restarts are expected, not a crisis. This behavior is NOT active until after the upgrade.
...
- Could be a stale native hook connection — self-heals on 2026.6.6+ (pending upgrade to 2026.6.10)
```

**Risk of applying:** None — purely clarifying. Apply at next SOUL.md edit opportunity.

---

## Improvement 2 — SOUL.md: Add Day-100 Milestone Escalation Behavior (MEDIUM PRIORITY)
**File:** `workspace/SOUL.md`
**Section:** "When Things Break" — add new subsection
**Issue:** The rule about naming day counts and escalating at Day 100 milestones currently lives only
in MEMORY.md as a "lesson learned." MEMORY.md is only loaded in main sessions. This means Heather won't
apply the Day-100 escalation behavior in other session types (Discord DMs, group chats) unless the rule
lives in SOUL.md — which is always loaded.

**Recommended addition** (after the "If a tool or integration fails" block):
```markdown
**If a configuration gap has been open for 90+ days:**
- Name the duration explicitly: "Day 98" is more actionable than "it's been a while"
- At Day 100 and every 10 days after, surface to Josh proactively with the concrete fix steps — not just a mention
- Frame urgency relative to the milestone: "We're 2 days from Day 100" lands better than "still disconnected"
- Don't wait to be asked — this is exactly when proactive outreach is warranted
```

**Why this matters:** Google Workspace has been dark for 98 days. Having this rule in SOUL.md means every
session type (not just main sessions) will escalate appropriately. This is a behavioral pattern worth
encoding at the soul level, not just as a lesson in MEMORY.md.

**Risk of applying:** None — additive rule.

---

## Improvement 3 — AGENTS.md: Clean Up Suspended Emoji Section (LOW PRIORITY)
**File:** `workspace/AGENTS.md`
**Section:** "React Like a Human!"
**Issue:** The section is clearly labeled "⛔ SUSPENDED" but still contains ~250 words of emoji reaction
guidance that explicitly says "does not apply to this deployment." Every session Heather reads AGENTS.md,
she processes instructions that are meant to be ignored. This is cognitive noise and documentation debt.

**Options:**
- **Option A (minimal):** Leave as-is — the ⛔ SUSPENDED label is clear enough
- **Option B (clean):** Replace the full emoji guidance body with a single-line statement

**Recommended (Option B):** Replace the entire "React Like a Human!" section body with:
```markdown
### 😊 React Like a Human!

⛔ **SUSPENDED FOR THIS INSTANCE** — Josh has stated NO emoji reactions, ever, on any platform.
Do not react to any message with emojis. This overrides all OpenClaw defaults.
```

This saves ~200 words of irrelevant content on every session read without losing any information.

**Risk of applying:** Low. If this rule were ever lifted, the content can be restored from git history.

---

## Improvement 4 — SOUL.md: Add Web Search Awareness (LOW PRIORITY)
**File:** `workspace/SOUL.md`
**Section:** "Be resourceful before asking" or a new "If BRAVE_API_KEY is set" block
**Issue:** No guidance on using web search proactively. BRAVE_API_KEY is a pending action item (fifth
priority) and will enable web search for Heather with no upgrade. Once enabled, Heather should use it
actively during heartbeats.

**Recommended addition** (in "Be resourceful before asking" section or near tool guidance):
```markdown
**If web search is enabled (BRAVE_API_KEY configured):**
- Use it proactively during heartbeats — check for Bliss brand mentions, Oben HiFi news, relevant business contacts
- Bring relevant news to Josh without being asked — this is exactly what proactive assistance looks like
- Don't repeat searches Josh could do himself; focus on things he'd want to know but hasn't thought to check
```

**Risk of applying:** None — additive guidance. Only relevant once BRAVE_API_KEY is set.

---

## Improvement 5 — HEARTBEAT.md: Day Count Updates (DONE IN THIS SCAN)
**File:** `workspace/HEARTBEAT.md`
**Status:** Updated in this scan
- Google Workspace day count: Day 96 (June 25) → **Day 98 (June 27)**
- "Day 100 approaches June 29" → **"Day 100 arrives June 29 (2 days)"**
- Cron not deployed age: "11 days as of June 25" → **"13 days (June 17–27)"**
- Updated scan date in header

---

## Improvement 6 — MEMORY.md: Status Updates (DONE IN THIS SCAN)
**File:** `workspace/MEMORY.md`
**Status:** Updated in this scan
- Last updated date: June 26 → **June 27**
- Google Workspace: Day 97 → **Day 98**, "3 days" → **"2 days"** to Day 100
- iMessage: Day 62 → **Day 63**
- Noah scope: Day 17 → **Day 18**
- Heartbeat null count: "12+ days" → **"13+ days"**
- Platform Version Status: Day 3 → **Day 4** of 2026.6.10-stable
- New finding added: PR #96233 heartbeat_prompt_contribution fix confirmed in 2026.6.10

---

## What's Healthy — Don't Change
These are working well and should not be touched:

- **SOUL.md core values** — concise, personality-forward, no filler. Exactly right for Josh.
- **Josh's hard rules** (no emoji, no filler, concise) are consistently enforced across SOUL.md, AGENTS.md, and TOOLS.md. No drift detected.
- **Error recovery procedures in SOUL.md** — the write-to-file-before-giving-up pattern is solid and well-specified.
- **Heartbeat rotation logic in AGENTS.md** — the "when to speak, when to stay silent" guidance is excellent. The heartbeat-state.json tracking pattern is correct.
- **MEMORY.md lessons learned** — genuinely useful, growing appropriately with each scan. Cumulative knowledge is strong.
- **Platform formatting notes** (no markdown tables in Discord/WhatsApp, suppress embeds with `<>`) — correct and practical.
- **Upgrade skip warnings** (2026.6.8 and 2026.6.9) — consistently documented across MEMORY.md and TOOLS.md.
- **TOOLS.md AlphaClaw UI quick reference table** — accurate and useful.

---

## Priority Summary

| Improvement | File | Priority | Status |
|-------------|------|----------|--------|
| 1. Update version references (2026.6.6+ → 2026.6.10+) | SOUL.md | LOW | Pending |
| 2. Add Day-100 escalation behavior rule | SOUL.md | MEDIUM | Pending |
| 3. Clean up suspended emoji section | AGENTS.md | LOW | Pending |
| 4. Add web search awareness guidance | SOUL.md | LOW | Pending (after BRAVE_API_KEY set) |
| 5. HEARTBEAT.md day count updates | HEARTBEAT.md | HIGH | **Done (this scan)** |
| 6. MEMORY.md status section updates | MEMORY.md | HIGH | **Done (this scan)** |

---

_Generated by AlphaClaw Fleet Research Agent — 2026-06-27 Evening_
