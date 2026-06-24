# Soul Improvements — June 24, 2026 Evening Scan
## Heather Schwartz — Josh Personal Assistant

**Based on:** Evening findings F43-F46, Gemini deprecation wave, platform analysis

---

## New Recommendations This Scan

### Rec 17 — Add Monthly Model Health Check to HEARTBEAT.md

**Priority: MEDIUM — Apply now (no upgrade needed)**

**Why:** The Gemini preview deprecation wave (F42/F43) demonstrates that Google retires preview
models with minimal notice and no notification to running agents. Two sister models to Heather's
primary shut down tomorrow. A monthly model health check prevents silent degradation from going
undetected for weeks.

**Add to `workspace/HEARTBEAT.md` as a new "Monthly" section:**

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

### Rec 18 — SOUL.md: Add Silent Model Failure Awareness

**Priority: LOW to MEDIUM — Apply anytime**

**Why:** SOUL.md has no guidance for when Heather's primary model silently fails over to a
fallback. The Gemini deprecation wave makes this a real risk. Without this awareness, Heather
doesn't know she's running degraded and Josh gets no notification.

**Add to `workspace/SOUL.md` under `## When Things Break`:**

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

## Prior Recommendations Status

| Rec | Description | Status |
|-----|-------------|--------|
| 1–6 | Emoji, memory, identity, heartbeat, behavioral recs | ✅ All resolved June 16–17 |
| 7 | Create MEMORY.md | ✅ Resolved June 16 |
| 8 | Enable Dreaming (openclaw.json) | ⏳ Pending — upgrade window open Day 5 |
| 9–12 | HEARTBEAT.md, gateway awareness, stale connection hygiene, model self-check | ✅ Resolved June 16–17 |
| 13 | Post-2026.6.9 upgrade: SOUL.md / TOOLS.md / MEMORY.md updates | ⏳ Pending upgrade |
| 14 | Post-upgrade: Discord Components V2 guidance | ⏳ Pending upgrade |
| 15 | HEARTBEAT.md cron-not-deployed warning | ✅ Resolved June 23 |
| 16 | MEMORY.md day count staleness | ✅ Resolved June 23 |
| 17 | Monthly model health check in HEARTBEAT.md | 🆕 Added June 24 — apply anytime |
| 18 | SOUL.md: silent model failure awareness | 🆕 Added June 24 — apply anytime |

---

## Priority Actions (June 24)

**Tonight (before June 25):**
1. Check https://ai.google.dev/gemini-api/docs/deprecations for `gemini-3-flash-preview`
2. If listed: edit openclaw.json via Browse tab → migrate primary to `gemini-3.5-flash` → restart gateway

**Anytime (AlphaClaw UI — no VPS needed):**
3. Set BRAVE_API_KEY in Envars tab (Finding 30)
4. Apply Rec 17: add monthly model health check block to HEARTBEAT.md via Browse tab
5. Apply Rec 18: add silent model failure awareness to SOUL.md via Browse tab

**Bundle in VPS upgrade session:**
6. Connect Google Workspace OAuth → https://5.78.142.81.sslip.io#general (Day 95 — CRITICAL)
7. Add userTimezone, dreaming, compaction, cron job to openclaw.json
8. Run staged upgrade: 2026.3.22 → 2026.5.27 → 2026.6.2 → 2026.6.5 → 2026.6.6 → 2026.6.9

**After upgrade:**
9. Apply Rec 13 (SOUL.md, TOOLS.md, MEMORY.md post-upgrade updates)
10. Apply Rec 14 (Discord Components V2 behavioral guidance)
