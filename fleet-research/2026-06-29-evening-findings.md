# Fleet Research — Josh / Heather — Evening Scan
**Date:** 2026-06-29 | **Scan:** Evening | **Agent:** AlphaClaw Fleet Research

---

## 🚨 CRITICAL: Google Workspace Day 100 — TODAY

**Finding:** Day 100 of Google Workspace disconnection has arrived. As of today, June 29, Heather has had no access to Josh's email or calendar for exactly 100 days.

**Why it matters:** Email and calendar are core to Heather's job as Josh's personal assistant. Day 100 is the milestone defined in SOUL.md requiring persistent proactive escalation at every main session, every 10 days thereafter.

**Action:**
1. Josh opens https://5.78.142.81.sslip.io#general
2. Clicks Google Workspace OAuth — 5-minute browser flow
3. Email + calendar access restored immediately. No VPS required.

**Heather's script for Josh:** "Today is Day 100. Email and calendar have been disconnected for 100 days. The fix takes 5 minutes in the browser — https://5.78.142.81.sslip.io#general."

**Risk:** LOW (user action only, no code changes)

---

## ✅ CONFIRMED: OpenClaw 2026.6.11 Still Beta — Stay on 2026.6.10

**Finding:** Web research June 29 confirms 2026.6.11-beta.1 is NOT stable — still pre-release. The June 28 evening scan noted it "may have gone stable" — this is now confirmed incorrect. Production installs must stay on 2026.6.10-stable.

**Why it matters:** Prevents premature upgrade to an unstable version. MEMORY.md and SOUL.md references to "verify 2026.6.11 before acting" were correct — today's research confirms it is still beta.

**Action:** No change. Continue targeting 2026.6.10-stable. After landing there, check `npm show openclaw@2026.6.11 version` — if stable tag is present, evaluate 2026.6.11 at that point.

**Risk:** NONE (confirmation finding, no action)

---

## INFO: 2026.6.10 Upgrade Window — Day 6, Green Light Continues

**Finding:** 2026.6.10-stable is now 6 days old (released June 24 at 03:01 UTC). Community signal remains clean. No new regressions reported. PR #96233 (heartbeat prompt contribution fix) and PR #93051 (cron retry backoff) both remain locked behind this upgrade.

**Action:** Execute staged upgrade when Josh is available for ~30 minutes on VPS:
```
2026.3.22 → 2026.5.27 → 2026.6.2 → 2026.6.5 → 2026.6.6 → 2026.6.10
```
- SSH into VPS, run `openclaw update` at each hop
- Test Discord + memory after each step
- Skip 2026.6.8 and 2026.6.9 — jump directly from 2026.6.6 to 2026.6.10
- After final hop: enable Active Memory + Dreaming in openclaw.json

**Risk:** MEDIUM (staged upgrade — follow path exactly, verify `npm show openclaw@latest version` = `2026.6.10` first)

---

## INFO: New OpenClaw Features to Leverage Post-Upgrade

**Finding:** Several features released since Josh's current version (2026.3.22) are worth enabling after upgrade:

### Skill Workshop (2026.6.1)
- Proposal management UI for reviewing, approving, and deploying skill upgrades
- Reduces manual skill installation overhead; no more hand-editing openclaw.json for skills

### Task Flow (2026.4.2)
- Background orchestration infrastructure
- Enables async tasks that run without blocking the main session
- Useful for: scheduled email summaries, calendar prep, parallel background research

### openclaw migrate (2026.4.26)
- `openclaw migrate --plan` and `--dry-run` modes
- Preview migration changes before applying — useful during staged upgrade

### openclaw backup create/verify (2026.3.8)
- State archives — back up workspace state before major version hops
- Recommended: run `openclaw backup create` before starting the staged upgrade

### Active Memory + Dreaming (available at 2026.4.9/2026.4.10, enabled post-upgrade)
- Active Memory: pre-reply context sub-agent, auto-recalls preferences before each response
- Dreaming: 3-phase background consolidation (Light Sleep → REM → Deep Sleep → MEMORY.md)
- Both opt-in via openclaw.json after upgrade

**Why it matters:** Task Flow and Skill Workshop directly support Heather's proactive assistant role and reduce manual configuration overhead.

**Action:** Add to post-upgrade checklist: run backup before upgrade, enable Task Flow, explore Skill Workshop, enable Active Memory + Dreaming.

**Risk:** LOW (opt-in features, post-upgrade)

---

## WARNING: OPENCLAW_TIMEZONE Still Not Set

**Finding:** No evidence this was set since June 28 evening alert. Cron remains at UTC default.

**Why it matters:** When heartbeat cron is deployed, a "9 AM PST" schedule will fire at 4–5 PM UTC (off by 7–8 hours) without this setting.

**Action:** AlphaClaw UI → Envars tab → add `OPENCLAW_TIMEZONE=America/Los_Angeles`
Do this NOW — no upgrade required, takes 30 seconds.

**Risk:** LOW (environment variable — no restart required)

---

## WARNING: Heartbeat Cron Not Deployed — Day 15

**Finding:** heartbeat-state.json has been all-null since June 17 (15 days as of June 29). No scheduled proactive checks have run.

**Why it matters:** Without cron, Heather only checks proactively when Josh is actively messaging. Urgent emails and calendar events go unmonitored between sessions.

**Dependency:** Requires (1) OPENCLAW_TIMEZONE set, (2) upgrade to 2026.6.10 for PR #96233 heartbeat fix, (3) cron entry added to openclaw.json.

**Risk:** LOW (configuration, post-upgrade)

---

## WARNING: iMessage Monitoring Paused — Day 65

**Finding:** iMessage has been paused since ~April 27, 2026 (65 days as of June 29). inbox-state.json has malformed duplicate key — do not manually edit.

**Auto-fix path:** The staged upgrade through 2026.6.6 runs a SQLite migration that clears the malformed state. No manual action needed beyond executing the upgrade.

**Risk:** LOW (auto-fix via upgrade path)

---

## 🔴 FLEET: Noah Scope Broken — Day 20 — BLIND SPOT

**Finding:** This session again confirms `noah--repo` (configured fleet scope) returns 404. Actual repos are `lylle-rgb/Noahrepo2` and `lylle-rgb/Noah-workspace`. Neither is accessible to this fleet session.

**Why it matters:** Noah's Market Catalyst Agent (Alpaca paper trading, SEC filings, market data) has received ZERO fleet research coverage for 20 consecutive days. Any version regressions, stale configs, model deprecations, or needed upgrades in Noah's setup are completely invisible. For a trading bot, this is high risk.

**Action:** Fleet admin (lylle) must update session scope to include `lylle-rgb/Noahrepo2` (the primary config repo) — or create a dedicated Noah fleet session with correct scope. This is blocking all Noah fleet improvement work.

**Risk:** HIGH (20-day blind spot on a production trading bot)

---

## INFO: AlphaClaw Apex — Multi-Instance Fleet Management

**Finding:** AlphaClaw Apex is a native Mac app for managing multiple OpenClaw VPS instances from one dashboard. Includes one-click Hetzner VPS deploy, unified fleet monitoring, and multi-instance management.

**Why it matters:** Relevant for fleet admin (lylle) managing both Josh and Noah instances. Apex may reduce the overhead of monitoring and managing the fleet — particularly relevant given the Noah scope issue.

**Action:** Fleet admin consideration — evaluate Apex for consolidated fleet management.

**Risk:** NONE (informational)

---

## Summary Priority List for Josh

| Priority | Action | Effort | Requires Upgrade? |
|----------|--------|--------|-------------------|
| 🚨 1 | Connect Google Workspace OAuth — Day 100 TODAY | 5 min in browser | No |
| ⚠️ 2 | Set OPENCLAW_TIMEZONE=America/Los_Angeles | 1 min in Envars tab | No |
| ⚠️ 3 | Run `openclaw backup create` before upgrade | 2 min on VPS | No |
| ⚠️ 4 | Execute staged upgrade to 2026.6.10 | ~30 min on VPS | Yes (self) |
| ⚠️ 5 | Enable Active Memory + Dreaming post-upgrade | 5 min in openclaw.json | After upgrade |
| ⚠️ 6 | Deploy heartbeat cron post-upgrade | 10 min in openclaw.json | After upgrade |
| ℹ️ 7 | Migrate model: gemini-3-flash-preview → gemini-3.5-flash | 5 min via Browse tab | No |
| ℹ️ 8 | Set BRAVE_API_KEY for web search | 5 min in Envars tab | No |

## Fleet Admin Actions (lylle)

| Action | Urgency |
|--------|---------|
| Fix Noah fleet session scope: add `lylle-rgb/Noahrepo2` | HIGH — Day 20 blind spot |
| Evaluate AlphaClaw Apex for multi-instance management | LOW |
