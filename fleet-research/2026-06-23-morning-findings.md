# Morning Scan — June 23, 2026

**Researcher:** AlphaClaw Fleet Agent
**Time:** Morning, June 23, 2026
**Previous scan:** June 23 Evening — F38 (HEARTBEAT.md cron warning applied), F39 (Discord V2), F40 (group chat context), F41 (MEMORY.md day counts applied)
**Instance:** josh_repo (Heather Schwartz — personal assistant)

---

## Headline: Upgrade Window Day 4. 1 New Finding (F42 — Gemini Preview Sunset Wave, 2-Day Warning). No New Beta.

---

## Platform Status

| Channel | Version | Status |
|---------|---------|--------|
| npm `latest` (stable) | **2026.6.9** | ✅ Still current — Day 4 of upgrade window |
| 2026.6.10-beta.2 | Released June 22 | 🔬 Beta Day 3 — do NOT install |
| 2026.6.8 | Skipped forever | ⛔ Skip confirmed |
| 2026.3.22 | **Current (Josh)** | Day 94 behind stable — upgrade urgent |

No new stable or beta release overnight. 2026.6.9 confirmed clean for 4 days. ETA for 2026.6.10-stable: ~1–2 weeks.

---

## New Findings This Scan

### F42 — Gemini Preview Model Sunset Wave: 2-Day Warning ⚠️

**Priority: MEDIUM-HIGH**

**What was found:** Google is actively retiring Gemini preview models in a rolling wave:
- `gemini-3.1-flash-image-preview` — shutting down **June 25, 2026** (2 DAYS)
- `gemini-3-pro-image-preview` — shutting down **June 25, 2026** (2 DAYS)
- `gemini-3.1-flash-lite-preview` — shutting down July 9, 2026
- `gemini-3-pro-preview` — already shut down March 9, 2026 (confirmed gone)

**Why it matters for Josh/Heather:** Josh's primary model is `google/gemini-3-flash-preview` — a preview model in the same cohort as those being retired. While `gemini-3-flash-preview` has no confirmed shutdown date as of this scan, the pattern is clear: Google is systematically retiring the preview generation as stable GA models become available. Gemini 3.5 Flash is now GA stable.

**Concrete risk:** If `gemini-3-flash-preview` is deprecated without sufficient notice (Google's pattern has been ~2–4 weeks), Heather's primary model goes silent. She falls to Fallback 1 (OpenRouter Gemini 3.5 Flash — good) then Fallback 2 (Haiku — also good). The failover chain would catch it, but Josh would see slower response times and no notice of the model swap.

**Action — do before upgrade session:**
1. Verify `gemini-3-flash-preview` status: check https://ai.google.dev/gemini-api/docs/deprecations for the model ID
2. If listed: update `openclaw.json` primary to `google/gemini-3.5-flash` before or during the upgrade session
3. If not listed: no immediate action, but plan migration to `gemini-3.5-flash` (GA, stable) as part of the upgrade bundle — preview models don't receive long-term support

**Suggested primary after migration:**
```json
"model": {
  "primary": "google/gemini-3.5-flash",
  "fallbacks": [
    "openrouter/anthropic/claude-haiku-4-5",
    "openrouter/google/gemini-3.5-flash"
  ]
}
```
Note: This also resolves Finding 31 (same-provider fallback gap) in one shot — Haiku 4.5 becomes cross-provider safety net.

**Risk level:** MEDIUM-HIGH. Preview model sunset is not confirmed for `gemini-3-flash-preview` specifically, but the 2-day shutdown of similar models is a clear signal to verify and plan migration now.

---

## Standing Alerts (Updated — June 23 Morning)

| Alert | Days | Priority |
|-------|------|----------|
| Google Workspace OAuth not connected | **Day 94** | 🔴 CRITICAL |
| Heartbeat cron not deployed — all-null state | **Day 9** | 🔴 HIGH |
| iMessage paused (auto-fix on upgrade) | **Day 59** | 🔴 HIGH |
| OpenClaw 2026.3.22 — upgrade window OPEN | **Day 4 of window** | 🔴 HIGH |
| BRAVE_API_KEY not set (F30) | — | 🟠 MEDIUM-HIGH |
| F42: Gemini preview sunset wave (2-day warning) | **June 25** | 🟠 MEDIUM-HIGH |
| Discord open to all (`allowFrom: ["*"]`) | Day 94 | 🟠 MEDIUM-HIGH |
| Same-provider fallback chain gap (F31) | — | 🟡 MEDIUM |
| Noah session scope broken (`noah--repo` 404) | **Day 13** | Fleet ops |

---

## Noah Repo: Scope Gap — Day 13

`lylle-rgb/noah--repo` remains a GitHub 404. Confirmed repos found via search:
- `lylle-rgb/Noahrepo2` — outside session scope
- `lylle-rgb/Noah-workspace` — outside session scope

No fleet research can be performed for Noah's Market Catalyst Agent until fleet admin corrects the session scope. If Noah's agent also uses a Gemini preview model as primary, F42 applies to him too — priority increases.

---

## What Josh Needs to Do

**Can do NOW — AlphaClaw UI, no VPS needed:**
- 🟠 Add BRAVE_API_KEY via AlphaClaw UI → Envars tab (Finding 30)

**Bundle into one VPS session (add F42 action first):**
0. 🟠 **NEW (F42):** Check gemini-3-flash-preview deprecation page — if listed, update primary to `gemini-3.5-flash` BEFORE running upgrade. If not listed, bundle migration into upgrade session anyway (preview models don't get long-term support).
1. 🔴 Connect Google Workspace OAuth at https://5.78.142.81.sslip.io#general (Day 94)
2. 🔴 Add `userTimezone: "America/Los_Angeles"` to `agents.defaults` (Finding 28 — do FIRST)
3. 🔴 Add compaction/memoryFlush block (Finding 4)
4. 🔴 Verify dreaming config key path (Finding 36), add dreaming config (Finding 22/24)
5. 🔴 Add heartbeat cron job to `cron.jobs` (Finding 27)
6. 🔴 Run staged upgrade: 2026.3.22 → 2026.5.27 → 2026.6.2 → 2026.6.5 → 2026.6.6 → 2026.6.9
   - Verify first: `npm show openclaw@latest version` = `2026.6.9`

**After upgrade to 2026.6.9:**
7. Fix fallback chain (F31 + F42 together): Haiku 4.5 → Fallback 1, Gemini 3.5 Flash (stable) → Primary or Fallback 2
8. Tighten `allowFrom: ["*"]` → Josh's Discord user ID (Finding 20)
9. Enable Discord streaming `"progress"` mode
10. Explore Discord Components V2 for interactive confirmations (F39)

---

*Sources: [Google Gemini Deprecations](https://ai.google.dev/gemini-api/docs/deprecations) · [OpenClaw GitHub Releases](https://github.com/openclaw/openclaw/releases) · [Gemini 3.5 Flash GA](https://ai.google.dev/gemini-api/docs/changelog) · [AlphaClaw GitHub](https://github.com/chrysb/alphaclaw/releases)*
