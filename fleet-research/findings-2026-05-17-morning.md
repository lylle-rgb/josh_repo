# Fleet Research — Josh / Heather Schwartz — Morning Scan

**Scan Date:** 2026-05-17 (Morning — Day 30)
**Agent:** AlphaClaw Apex Fleet Research Agent
**Instance:** Josh / Heather Schwartz — Discord bot personal assistant (iMessage, email, calendar, contacts)
**Previous Findings:** findings-2026-05-16-evening.md (Day 29 Evening, Findings 1–60)
**Cumulative Open Findings:** 65 (5 new this morning, 0 resolved)

---

## Platform News — New Since Yesterday's Evening Scan (May 16)

| Item | Detail |
|---|---|
| OpenClaw 2026.5.8 – 2026.5.12 shipped overnight | Five new stable releases. Josh is now 19 releases behind stable (2026.3.22 vs 2026.5.12). Beta channel sits at 2026.5.14-beta.2 — 22 releases ahead. |
| 2026.5.12 Heartbeat multi-agent repair | Scheduler wakes now fan out in parallel, HEARTBEAT.md prose reaches the model reliably on every turn, stream setup has a connect-timeout watchdog, and doctor warns when pinned heartbeat sessions are missing. Largest heartbeat reliability improvement since the feature launched. |
| 2026.5.12 OAuth stale lock reclamation | Stale OAuth file locks can now be automatically reclaimed. Directly relevant to Josh's Google account connection failure (Finding 56 — CRITICAL, Day 30). |
| 2026.5.12 Monotonic transcript sequence | Live-update sessions now carry a monotonic transcript sequence number through all updates. Stale SSE history is refreshed proactively. Improves session history integrity across Heather's Discord sessions. |
| 2026.5.12 SecretRef credential resolution | Provider credentials resolve through structured SecretRefs instead of pattern-matching all-caps env vars. Josh's `${DISCORD_BOT_TOKEN}` and `${OPENCLAW_GATEWAY_TOKEN}` continue to work as-is but will benefit from explicit SecretRef registration post-upgrade. |
| BlueBubbles Private API — Full GA confirmed | The BlueBubbles Private API integration (iMessage direct-Mac access, no cloud relay) reached full GA in April 2026. Multi-account routing shipped March 2026. Directly addresses the iMessage cloud-proxy root cause identified in prior scans. |
| Gemini 3.1 Flash Lite — 363 tok/s at 1/8 cost | Gemini 3.1 Flash Lite confirmed available. Processes at 363 tokens/second at one-eighth the cost of Gemini Pro — optimal for heartbeat polling, email triage, calendar lookups, and contact queries. Josh's retired `claude-3.5-haiku` fallback should be replaced with this. |

---

## New Findings — Morning Scan (66–70)

---

### Finding 66 — 2026.5.12 Heartbeat Multi-Agent Overhaul: Best Window to Design HEARTBEAT.md (MEDIUM/Opportunity)

**Risk:** MEDIUM (opportunity, sequenced on upgrade)
**Days Pending:** 0 (new — 2026.5.12 shipped overnight)

**Description:**
OpenClaw 2026.5.12 shipped the most comprehensive heartbeat reliability overhaul since the feature was introduced. Specific improvements:
- **Parallel wake fan-out**: Scheduler wakes multiple agents simultaneously instead of serially. Reduces heartbeat latency on shared instances.
- **HEARTBEAT.md prose reliable**: The heartbeat model now receives the full HEARTBEAT.md prose on every scheduled turn. Previously, HEARTBEAT.md was occasionally dropped from context on rapid consecutive heartbeats.
- **Connect-timeout watchdog**: If the stream setup does not complete within the timeout window, the heartbeat session is safely aborted and rescheduled rather than hanging.
- **Doctor detection**: `openclaw doctor` now warns when pinned heartbeat sessions are missing or unhealthy, enabling proactive catch before the next beat fires.

Heather's `HEARTBEAT.md` has been empty since deployment (Day 1). The prior hesitation to configure heartbeat tasks was partly justified by the prior reliability gaps — a misconfigured or unreliable heartbeat burns token budget without value. 2026.5.12 fixes all three reliability failure modes. **This is the optimal window to design HEARTBEAT.md tasks, ready to activate immediately after upgrading.**

AGENTS.md documents the intended heartbeat cadence: email checks, calendar lookups, weather, mentions — 2–4 times per day, with quiet hours 23:00–08:00. None of this is active. Post-upgrade, it can be.

**Action:**
1. Design HEARTBEAT.md task list now (ready-to-paste content available in soul-improvements-2026-05-17-evening.md).
2. Hold activation until OpenClaw is upgraded to 2026.5.12 (Finding 61).
3. After upgrade and Google account connection (Finding 56), activate heartbeat and verify one full cycle via AlphaClaw UI session logs.

**Risk Assessment:** Zero risk to design now. The upgrade prerequisite makes activation safe to sequence. Every heartbeat turn from activation forward benefits from the parallel-wake + reliable-prose reliability improvements.

---

### Finding 67 — BlueBubbles Private API iMessage Integration Now GA: Root Cause Addressed (HIGH)

**Risk:** HIGH (directly addresses the iMessage dark finding)
**Days Pending:** 0 (new — confirmed GA April 2026)

**Description:**
The BlueBubbles Private API integration for OpenClaw reached full general availability in April 2026. This is significant because it changes the iMessage integration architecture:

- **Current (cloud proxy):** iMessage routes through a cloud relay. The cloud proxy is the likely root cause of iMessage going dark ~April 26 (Finding 49/57 — inbox-state.json frozen, thread pending). Cloud relays can silently drop if sessions expire or authentication lapses.
- **BlueBubbles Private API:** Direct Mac-native message access. No cloud relay. Messages route through a local BlueBubbles server on Josh's Mac, eliminating the relay failure mode entirely.

Additional context: Multi-account iMessage routing shipped March 2026, meaning Heather could handle multiple iMessage identities (e.g., personal vs business) within a single session.

The iMessage dark issue has persisted for 21+ days (as of Day 30). BlueBubbles Private API does require a setup step: install BlueBubbles Server on Josh's Mac, pair it with OpenClaw, and configure the integration in `openclaw.json`. The setup is estimated at 15–30 minutes.

**Action:**
1. Confirm whether current iMessage setup uses BlueBubbles or the standard cloud proxy (check AlphaClaw UI at `https://5.78.142.81.sslip.io` under integrations).
2. If using cloud proxy: evaluate BlueBubbles Private API migration as the permanent iMessage fix.
3. Research bluebubbles integration docs for OpenClaw (separate from the cloud proxy path).
4. Do not configure until OpenClaw is upgraded to 2026.5.12 (upgrade first, then iMessage integration).

**Risk Assessment:** Medium. Setup requires installing software on Josh's Mac. No data risk. iMessage being dark for 21+ days makes the fix urgency high.

---

### Finding 68 — Gemini 3.1 Flash Lite Available: Replace Retired claude-3.5-haiku Fallback (MEDIUM)

**Risk:** MEDIUM (retired fallback in active config)
**Days Pending:** 0 (new — GA confirmed, prior scans flagged haiku retirement separately)

**Description:**
Gemini 3.1 Flash Lite is confirmed available on OpenRouter as of 2026. It is the correct replacement for `openrouter/anthropic/claude-3.5-haiku` in Heather's fallback chain — which prior scans flagged as a retired model.

Gemini 3.1 Flash Lite specs relevant to Heather's heartbeat and email tasks:
- **363 tokens/second** processing speed — faster than any current Anthropic model
- **1/8 the cost** of Gemini Pro — optimal for high-frequency heartbeat polling
- **Designed for classification, routing, and extraction** — exactly the task profile of email triage (is this urgent?), calendar lookup (what's today's agenda?), and contact queries (who is this person?)

Josh's current fallback chain:
```json
"fallbacks": [
  "openrouter/google/gemini-2.5-flash",
  "openrouter/anthropic/claude-3.5-haiku"  // ← retired
]
```

Proposed replacement:
```json
"fallbacks": [
  "openrouter/google/gemini-3.1-flash-lite-preview",
  "openrouter/google/gemini-2.5-flash",
  "openrouter/anthropic/claude-haiku-4-5"
]
```

This adds a fast, cheap tier (Flash Lite) before the mid-tier fallback, and replaces the retired haiku with the current `claude-haiku-4-5`.

**Action:**
Update `openclaw.json` fallbacks array. No restart required — takes effect on next session start.

**Risk Assessment:** Low. Fallback models are only invoked when the primary fails. Replacing a retired model eliminates the risk of primary + first fallback both failing, triggering an unknown behavior on a retired endpoint.

---

### Finding 69 — 2026.5.12 OAuth Stale Lock Reclamation May Unblock Google Account Connection (MEDIUM)

**Risk:** MEDIUM (potential unblock for CRITICAL finding 56)
**Days Pending:** 0 (new — 2026.5.12 shipped overnight)

**Description:**
OpenClaw 2026.5.12 added automatic reclamation of stale OAuth file locks. When a prior OAuth connection attempt fails mid-flow (network drop, session timeout, user navigates away), the OAuth library can leave a lock file in the credential store that blocks subsequent connection attempts.

Finding 56 (CRITICAL — Google account not connected after 30 days) has not been resolved despite the AlphaClaw UI being available at `https://5.78.142.81.sslip.io`. One plausible explanation: if Josh attempted OAuth during onboarding and it failed mid-flow, the stale lock file would block all subsequent attempts — the UI presents the connect button, the flow begins, but silently fails without a clear error.

Upgrading to 2026.5.12 clears stale OAuth lock files automatically on Gateway start. After upgrading, the next Google account connection attempt via the AlphaClaw UI should succeed without needing manual troubleshooting.

**This does not replace the action required (open AlphaClaw UI and connect)** — but it removes one class of silent failure that may have been blocking success.

**Action (sequenced):**
1. Upgrade to 2026.5.12 (Finding 61) — clears any stale OAuth lock files on Gateway start.
2. Open `https://5.78.142.81.sslip.io#general` in a browser (Finding 56 action).
3. Complete Google OAuth flow.
4. Verify connection by running a test calendar query in Discord.

**Risk Assessment:** Low. The upgrade is already required for other reasons. If the stale lock was the blocker, this resolves Finding 56 automatically as part of the upgrade.

---

### Finding 70 — 2026.5.12 SecretRef Credential Resolution: Audit ${ENV_VAR} Usage (LOW)

**Risk:** LOW (informational, best practice)
**Days Pending:** 0 (new — 2026.5.12 shipped overnight)

**Description:**
OpenClaw 2026.5.12 moved provider credentials from pattern-matching all-caps environment variable names to explicit SecretRef resolution. This is a security and reliability improvement: credentials are no longer matched by the coincidental presence of an all-caps name in the environment — they must be explicitly declared.

Josh's `openclaw.json` currently uses:
- `"token": "${DISCORD_BOT_TOKEN}"` — Discord channel
- `"token": "${OPENCLAW_GATEWAY_TOKEN}"` — Gateway auth

These continue to work post-2026.5.12 through legacy env-var interpolation. However, the new SecretRef path provides:
- **Explicit audit trail**: which credentials are in use at any time
- **No accidental leakage**: env vars that happen to be all-caps cannot inadvertently match a credential slot
- **Better doctor diagnostics**: `openclaw doctor` can report credential health when SecretRefs are declared

This is a low-priority post-upgrade quality improvement, not a blocker.

**Action (post-upgrade, low priority):**
After upgrading to 2026.5.12, review AlphaClaw documentation for SecretRef migration path. Consider declaring `DISCORD_BOT_TOKEN` and `OPENCLAW_GATEWAY_TOKEN` as explicit SecretRefs for cleaner credential management.

**Risk Assessment:** Zero. Current interpolation continues to work. This is a best-practice note for the post-upgrade maintenance window.

---

## Day 30 Priority Order — Morning

### Tier 0 — Do Right Now (Zero Config, Highest Impact)

1. **Fix retired fallback** (Finding 68): Replace `openrouter/anthropic/claude-3.5-haiku` with `openrouter/google/gemini-3.1-flash-lite-preview` + `openrouter/anthropic/claude-haiku-4-5`. **3 minutes.** Takes effect immediately.
2. **Connect Google account** (Finding 56 — CRITICAL): Open `https://5.78.142.81.sslip.io#general`. Post-upgrade, stale OAuth lock is reclaimed automatically (Finding 69). **5 minutes.**

### Tier 1 — This Weekend (Before Monday)

3. **Upgrade OpenClaw to 2026.5.12** (Finding 61): Backup → upgrade via AlphaClaw UI → verify. Enables heartbeat reliability overhaul, OAuth stale lock reclamation, monotonic transcripts, and SecretRef credentials.
4. **Populate TOOLS.md** (Finding 64): Document gog-cli, Discord, iMessage, AlphaClaw setup before upgrade so permission preflights (2026.5.9) surface correctly.
5. **Design HEARTBEAT.md task list** (Finding 66): Ready-to-paste in soul-improvements-2026-05-17-evening.md. Hold activation until post-upgrade and post-Google-connection.

### Tier 2 — This Week

6. **Evaluate BlueBubbles Private API** (Finding 67): If iMessage remains dark post-upgrade + Google connection, investigate BlueBubbles as the permanent iMessage fix. Check current integration type first via AlphaClaw UI.
7. **Delete BOOTSTRAP.md** (Finding 62): Zero risk. Reduces startup token consumption.
8. **Start daily memory logs** (Finding 63): Create `workspace/memory/2026-05-17.md` today.

---

## Persistent Findings Status Table — Day 30 Morning

| # | Title | Risk | Days Open |
|---|---|---|---|
| 48/56 | Google account never connected | CRITICAL | 30 |
| 49/57 | inbox-state.json invalid + iMessage dark 21+ days | HIGH | 21 |
| 50 | No MEMORY.md | MEDIUM | 30 |
| 51 | AGENTS.md generic template | MEDIUM | 30 |
| 52/65 | HEARTBEAT.md empty | HIGH | 30 |
| 61 | 19 releases behind stable | MEDIUM | 30+ |
| 62 | BOOTSTRAP.md not deleted | MEDIUM | 30 |
| 63 | No daily memory logs | HIGH | 30 |
| 64 | TOOLS.md template only | MEDIUM | 30 |
| 68 | Retired claude-3.5-haiku fallback | MEDIUM | new |
| 69 | Stale OAuth lock (may block Google) | MEDIUM | new |

**Open: 65 (5 new this morning) | Resolved: 0 | Critical: 1 | High: 10+ | Medium: 15+ | Low: 5+**

---

## Summary Assessment — Day 30 Morning

OpenClaw shipped 5 new releases overnight, widening Josh's gap to 19 releases. The most impactful new feature for Heather is the 2026.5.12 heartbeat multi-agent reliability overhaul — HEARTBEAT.md prose now reaches the model reliably on every beat. This removes the primary reliability risk that justified holding off on heartbeat configuration. The OAuth stale lock fix may also silently unblock the 30-day-old Google account connection failure.

**Next scheduled scan:** findings-2026-05-17-evening.md (Day 30 Evening)
**Highest urgency before next scan:** Fix retired fallback (3 min, now). Connect Google account (5 min, now).

---

*Generated by AlphaClaw Apex Fleet Research Agent — Morning Scan — 2026-05-17 (Day 30)*
