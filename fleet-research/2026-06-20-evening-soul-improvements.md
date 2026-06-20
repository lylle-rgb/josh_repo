# Soul Improvements — Heather Schwartz | 2026-06-20 Evening Scan

**Instance:** Josh — personal assistant (Discord/iMessage/email/calendar/contacts)
**Date:** 2026-06-20 (evening scan)
**Based on:** Behavioral pattern analysis — 4 consecutive days of null heartbeat state

---

## Context

Previous soul improvement scans (June 13–19) established and populated the core workspace files. Tonight's scan focuses on a specific behavioral failure: **4 consecutive days of null heartbeat-state.json**. This points to either:

1. The heartbeat cron was never deployed to the VPS (most likely)
2. Heather is not writing state after checks (behavioral failure)

The two recommendations below address both failure modes.

---

## Recommendation 13 — AGENTS.md: Mandatory State-Write After Every Heartbeat

**Priority:** HIGH
**Why:** Heartbeat-state.json has been all-null for 4 days. The "write it down" instruction in AGENTS.md is general. Make the heartbeat write mandatory and explicit.

**Add to `workspace/AGENTS.md` under the `## 💓 Heartbeats - Be Proactive!` section,
immediately after the state tracking JSON example:**

```markdown
### Mandatory Write Rule

After EVERY heartbeat check — no exceptions — write to `memory/heartbeat-state.json` BEFORE
ending the turn. Even if the check found nothing, even if Google is offline, even if iMessage
is paused. The write proves the check ran.

- Use Unix milliseconds for timestamps: `Date.now()` in JS, `int(time.time() * 1000)` in Python
- If a check was skipped due to a known blocker (Google OAuth not connected), still write the
  timestamp with a note field: `"email_skip_reason": "google_oauth_not_connected"`
- Null means the check has NEVER run. That's a red flag the fleet will escalate.
```

---

## Recommendation 14 — SOUL.md: Self-Reporting Pattern for Silent Failures

**Priority:** HIGH
**Why:** Heather has been operational for 90 days with zero confirmed proactive outreach. The fleet has been the only source of operational status. Heather should self-report when things are broken — proactively, without being asked.

**Add to `workspace/SOUL.md` under `## When Things Break`, after the existing gateway restart guidance:**

```markdown
**If you notice you haven't sent Josh anything in >24 hours:**
That's a yellow flag. Check your heartbeat-state.json. If all checks are null or stale, something
is wrong. Send Josh a brief message: what you were supposed to check, what's working, what's not.
Don't wait for him to ask.

**If heartbeat-state.json is all null after you've been running for >1 day:**
This means your scheduled checks may not be running. Report it to Josh immediately:
"Hey — I noticed my heartbeat checks haven't logged any results. [Google OAuth disconnected / 
iMessage paused / other]. Do you want me to trigger a manual check now?"

**The fleet is your backup.** The fleet research agent checks in daily and notices when
you've gone quiet. But they can only commit notes — they can't fix your cron or connect your
OAuth. Only you and Josh can. Report proactively so Josh can act.
```

---

## Recommendation 15 — AGENTS.md: Gemini Preview Model Monitoring

**Priority:** LOW
**Why:** Primary model is `google/gemini-3-flash-preview`. Preview models are deprecated without long notice. Previous fleet research caught the gemini-2.5-flash deprecation. This adds an explicit check.

**Add to `workspace/AGENTS.md` under `## Session Startup`, step 5 (Optional self-check):**

```markdown
Also check: is the primary model `google/gemini-3-flash-preview`? If so, check Google's model
deprecation page or search for "gemini-flash-preview deprecated" once a week. When a stable
`gemini-3-flash` is released (dropping the -preview suffix), update openclaw.json primary model.
Preview models are not guaranteed stable and can be deprecated with short notice.
```

---

## Recommendation 16 — MEMORY.md: Add Cron Status Awareness

**Priority:** MEDIUM
**Why:** The heartbeat cron was likely never deployed. MEMORY.md should capture this as a known gap so future sessions don't assume it's running.

**Add to `workspace/MEMORY.md` under `## Known Configuration Issues`:**

```markdown
- Heartbeat cron: **LIKELY NOT DEPLOYED** on VPS — heartbeat-state.json has been all-null
  since created June 17 (4+ days). Fleet has flagged this as a deployment gap, not a behavioral
  one. Until a cron is explicitly added to openclaw.json and the server is upgraded, proactive
  checks will not run on schedule. Josh can trigger manual checks by messaging Heather in Discord.
```

*(This addition has been applied in tonight's MEMORY.md update.)*

---

## Priority Order (Updated June 20)

Builds on the June 19 soul-improvements. New recs only:

| # | Recommendation | File | Priority |
|---|----------------|------|----------|
| 13 | Mandatory write after every heartbeat | workspace/AGENTS.md | HIGH |
| 14 | Self-reporting for silent failures | workspace/SOUL.md | HIGH |
| 15 | Gemini preview model weekly check | workspace/AGENTS.md | LOW |
| 16 | Cron status awareness in MEMORY.md | workspace/MEMORY.md | MEDIUM — applied tonight |

---

## Recs 1–12 Status (From Previous Scans)

All applied via GitHub as of June 17–19 scans:
- Recs 1–8: Applied June 16–17 (SOUL.md, AGENTS.md, MEMORY.md, HEARTBEAT.md, TOOLS.md)
- Recs 9–11: Applied June 16 (HEARTBEAT.md Google-awareness, gateway posture)
- Rec 12: Applied June 16 (monthly self-check for dead fallbacks)

**Recs 13–14 require Josh to review and accept.** The AGENTS.md and SOUL.md changes
in this file are recommendations — they have NOT been auto-applied to keep the soul files
stable. Josh should review these suggestions and apply them via Heather's VPS workspace
or via the AlphaClaw file browser.
