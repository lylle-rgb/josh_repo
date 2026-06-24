# Fleet Research Findings — Josh / Heather Schwartz
## Evening Scan — June 24, 2026

**Researcher:** AlphaClaw Fleet Agent
**Scan time:** Evening, June 24, 2026 (PDT)
**Previous scan:** June 23, 2026 (morning + evening)

---

## New Findings This Scan

### F43 — CRITICAL: Gemini Preview Sister Models Shut Down TOMORROW (June 25) ⚠️⚠️

**Priority: CRITICAL — Escalation of F42**

Two Gemini preview models shut down **June 25, 2026 — tomorrow**:
- `gemini-3.1-flash-image-preview` → confirmed shutdown June 25
- `gemini-3-pro-image-preview` → confirmed shutdown June 25

Josh's primary model `google/gemini-3-flash-preview` is in the same naming family and generation.
No confirmed shutdown date for this specific model ID, but the deprecation wave is systematic and accelerating.

**Silent failure risk:** If shut down, Heather silently falls to Fallback 1 (OpenRouter Gemini 3.5 Flash)
with no notification to Josh.

**Actions — TONIGHT or June 25 morning:**
1. Visit https://ai.google.dev/gemini-api/docs/deprecations — check `gemini-3-flash-preview` status
2. If NOT listed: no immediate action; migration to `gemini-3.5-flash` still recommended (see F42)
3. If listed: migrate primary IMMEDIATELY — no upgrade needed, just edit openclaw.json:

```json
"model": {
  "primary": "google/gemini-3.5-flash",
  "fallbacks": [
    "openrouter/anthropic/claude-haiku-4-5",
    "openrouter/google/gemini-3.5-flash"
  ]
}
```

**This can be done via AlphaClaw UI — no VPS upgrade needed:**
Browse tab → `.openclaw/workspace/../openclaw.json` → edit model block → save → restart gateway.

This single edit also resolves F42 (Gemini sunset) and F31 (same-provider fallback gap).

---

### F44 — Fleet Ops: Noah Session Scope Broken — Day 14

Noah's configured repo (`lylle-rgb/noah--repo`) returns 404. Confirmed again this evening scan.

**Actual repos found:**
- `lylle-rgb/Noahrepo2` (last updated 2026-03-08) — likely the correct repo, out of session scope
- `lylle-rgb/Noah-workspace` (last updated 2026-03-07) — alternative, also out of scope

**Risk:** Noah (Market Catalyst Agent, Alpaca paper trading) has had zero fleet coverage for 14 days.
Last known update: March 2026 (~107 days ago). Trading bot with potentially stale OpenClaw version
and unaudited skills — highest risk profile in the fleet. ClawHavoc attack specifically targeted
trading skill packages.

**Action:** Fleet admin update session scope to `lylle-rgb/Noahrepo2`. Full audit on next scan.

---

### F45 — ClawHub SkillSpector Scanning Now Standard (POSITIVE)

All new ClawHub skill installs now include:
- Skill Card documentation (purpose, origin, permissions)
- SkillSpector scan for hidden instructions and agentic risks
- Opt-in host-exec guardrails

No action required. Benefits future skill installs post-upgrade. Still run `openclaw skill list`
post-upgrade to confirm no skills were inadvertently installed (Finding 25).

---

### F46 — 2026.6.10-beta.2 Auto Fast Mode: Day 4 (Do Not Install)

Beta still active. Auto fast mode for short conversational turns would benefit Heather's casual
Discord exchanges with Josh. Monitor for 2026.6.10-stable, expected late June or early July.
**Do not install beta.** Stay on 2026.6.9-stable.

---

## Day Count Updates (June 24 Evening)

| Metric | June 23 | June 24 |
|--------|---------|---------|
| Google Workspace OAuth disconnected | Day 94 | Day 95 |
| Heartbeat-state.json all-null | Day 9 | Day 10 |
| iMessage paused | Day 59 | Day 60 |
| Upgrade window open | Day 4 | Day 5 |
| OpenClaw outdated (2026.3.22) | Day 94 | Day 95 |
| Noah scope broken | Day 13 | Day 14 |

---

## Open Item Status (June 24 Evening)

| Finding | Priority | Status |
|---------|----------|--------|
| **F43. Gemini sister models shut down TOMORROW** | **CRITICAL** | ⏳ Act TONIGHT |
| F44. Noah scope broken — Day 14 | FLEET OPS | ⏳ Fix scope |
| F45. SkillSpector standard on ClawHub | POSITIVE | ✅ Auto post-upgrade |
| F46. 2026.6.10-beta.2 auto fast mode | INFO | 🔬 Monitor — do not install |
| F42. Gemini preview sunset (escalated → F43) | CRITICAL | ⏳ See F43 |
| F31. Same-provider fallback gap | MEDIUM | ⏳ Bundle with F43 fix |
| F30. BRAVE_API_KEY not set | MEDIUM-HIGH | ⏳ AlphaClaw UI anytime |
| F29. Upgrade to 2026.6.9 (Day 5) | HIGH | ⏳ Window open |
| F28. userTimezone not set | MEDIUM-HIGH | ⏳ Bundle with upgrade |
| F27. Heartbeat cron not deployed (Day 10) | HIGH | ⏳ Bundle with upgrade |
| F22/24. Dreaming not enabled | HIGH | ⏳ Bundle with upgrade |
| F4. compaction/memoryFlush missing | HIGH | ⏳ Bundle with upgrade |
| F2. Google Workspace OAuth (Day 95) | CRITICAL | ⏳ Connect at AlphaClaw UI |
| F20. Discord security open | MEDIUM-HIGH | ⏳ Post-upgrade |
| F39. Discord Components V2 | INFO | Post-upgrade capability |
| F40. Group chat context every turn | INFO | Auto in 2026.6.9 |

---

*Sources: [Google Gemini Deprecations](https://ai.google.dev/gemini-api/docs/deprecations) · [OpenClaw 2026.6.9 Release](https://github.com/openclaw/openclaw/releases/tag/v2026.6.9) · [OpenClaw Cron Docs](https://docs.openclaw.ai/automation/cron-jobs) · [ClawHub SkillSpector](https://github.com/openclaw/openclaw) · [AlphaClaw](https://github.com/chrysb/alphaclaw) · [ClawHavoc Security](https://thehackernews.com/2026/02/researchers-find-341-malicious-clawhub.html)*
