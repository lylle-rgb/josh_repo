# Morning Scan — June 26, 2026

**Researcher:** AlphaClaw Fleet Research Agent
**Session:** Morning scan, June 26, 2026
**Repos covered:** josh_repo (Heather Schwartz) | Noah-workspace: ⛔ scope broken (Day 17)

---

## Summary

No new critical alerts. Key status updates:

| Item | Status |
|------|--------|
| 2026.6.10 stable | Day 2 — 48h clean, no regressions — **upgrade window FULLY OPEN** |
| 2026.6.11-beta.1 | Still beta (2 days old) — do not install |
| Google OAuth | Day 97 — **Day 100 in 3 days (June 29)** |
| iMessage | Day 62 paused — auto-fix on upgrade |
| Heartbeat cron | Day 12 null — deploy with upgrade |
| Noah fleet | Day 17 dark — scope fix still pending |

---

## New Findings

### F56 — 2026.6.10 Day 2 Stable: Upgrade Window FULLY OPEN
**Priority: HIGH (POSITIVE)**

48 hours on npm stable with no regression reports. The "Day 1 caution" advisory (F47/F29) is lifted. No new issues found in the 48h monitoring window.

Latest release state:
- `npm latest`: 2026.6.10 (June 24 03:01 UTC, now Day 2)
- `beta`: 2026.6.11-beta.1 (June 24, 2 days old — do not install)
- No new stable or beta published in the last 24h

**Action:** Upgrade window is FULLY OPEN. Execute staged upgrade when Josh has bandwidth:
```
2026.3.22 → 2026.5.27 → 2026.6.2 → 2026.6.5 → 2026.6.6 → 2026.6.10
```
Verify first: `npm show openclaw@latest version` = `2026.6.10`

### F57 — Google Workspace OAuth: Day 97 — 3 Days to Day 100
**Priority: CRITICAL**

Day 97 without Google Workspace OAuth. Day 100 arrives **June 29, 2026**.

Blocked capabilities:
- Gmail completely inaccessible — Heather cannot monitor or act on Josh's emails
- Google Calendar inaccessible — no proactive schedule awareness
- Google Contacts inaccessible — no contact enrichment for iMessage/email threads

Three of Heather's five heartbeat check categories permanently blocked until this is fixed.

The fix is entirely non-technical and takes ~5 minutes:
1. Josh opens https://5.78.142.81.sslip.io#general
2. Click Google Workspace → complete OAuth flow
3. Gmail, Calendar, and Contacts tools activate immediately

**This can be done RIGHT NOW, completely independent of any upgrade.**

Day 100 is a psychological milestone; the urgency is real regardless of day count — Josh has been running a personal assistant with no email or calendar access for over 3 months.

### F58 — Noah Fleet Gap: Day 17 — No Change
**Priority: FLEET OPS**

Noah session scope still broken. Day 17 without fleet coverage.

- `lylle-rgb/noah--repo` → 404 (misconfigured session scope)
- Correct repo (confirmed via GitHub search): `lylle-rgb/Noahrepo2` (last updated 2026-03-08)
- Last known OpenClaw version: unknown (last git sync ~March 2026 = ~111 days ago)

Noah is the higher-risk customer — trading bot with external Alpaca API access and unknown skill list (ClawHavoc exposure risk unverified).

**Fleet admin action:** Fix session scope: `lylle-rgb/noah--repo` → `lylle-rgb/Noahrepo2`

---

## Web Research Summary

- **OpenClaw releases:** No new stable since 2026.6.10 (June 24). 2026.6.11-beta.1 is 2 days old — still beta, no stable imminent.
- **Gemini models:** Sister preview models (gemini-3.1-flash-image-preview, gemini-3-pro-image-preview) confirmed shut down June 25 per F52. Josh's primary (`gemini-3-flash-preview`) unaffected. GA migration to `gemini-3.5-flash` remains MEDIUM-HIGH priority — can do now via Browse tab.
- **AlphaClaw:** No new releases beyond what was documented. Self-healing watchdog and hourly git sync confirmed operating.
- **Alpaca MCP Server v2:** 65 tools confirmed stable. Applicable to Noah post-scope-fix.
- **Discord:** No new advisories. Components V2 awaits 2026.6.10 upgrade.
- **SEC filing tools:** `sec-filing-watcher` skill confirmed available on ClawHub. High-value for Noah.

---

## Open Action List (No Change Since June 25 Evening)

### Josh — Can Do NOW (AlphaClaw UI, no VPS needed)
1. **[CRITICAL]** Connect Google Workspace OAuth → https://5.78.142.81.sslip.io#general ← **5 min, do today**
2. **[MEDIUM-HIGH]** Set BRAVE_API_KEY → Envars tab (web search currently disabled for Heather)
3. **[MEDIUM-HIGH]** Migrate model to `gemini-3.5-flash` + fix fallback chain → Browse tab
   ```json
   "model": {
     "primary": "google/gemini-3.5-flash",
     "fallbacks": [
       "openrouter/anthropic/claude-haiku-4-5",
       "openrouter/google/gemini-3.5-flash"
     ]
   }
   ```

### Josh — Requires VPS (Bundle in One Session)
4. **[HIGH]** Add `userTimezone: "America/Los_Angeles"` to `agents.defaults` (do FIRST — F28)
5. **[HIGH]** Add compaction/memoryFlush block (F4)
6. **[HIGH]** Verify dreaming key path (F36), add dreaming config (F22/F24)
7. **[HIGH]** Add heartbeat cron job (F27)
8. **[HIGH]** Staged upgrade: 2026.3.22 → 2026.5.27 → 2026.6.2 → 2026.6.5 → 2026.6.6 → **2026.6.10**
   - Verify first: `npm show openclaw@latest version` = `2026.6.10`
   - Day 2 of stable — Day 1 caution lifted, proceed confidently

### After Upgrade
9. **[MEDIUM-HIGH]** Tighten Discord `allowFrom: ["*"]` → Josh's Discord user ID (F20)
10. **[LOW]** Enable Discord streaming `"progress"` mode
11. **[LOW]** Enable auto-thread titles

### Fleet Admin
12. **[FLEET OPS]** Fix Noah scope: `noah--repo` (404) → `Noahrepo2` — Day 17 without coverage

---

*Sources: [OpenClaw Releases](https://github.com/openclaw/openclaw/releases) · [releasebot.io/updates/openclaw](https://releasebot.io/updates/openclaw) · [AlphaClaw](https://alphaclaw.md/) · [Google Gemini Deprecations](https://ai.google.dev/gemini-api/docs/deprecations) · [Alpaca MCP Server v2](https://alpaca.markets/integrations)*
