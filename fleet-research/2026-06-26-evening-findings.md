# Fleet Research Findings — Josh / Heather Schwartz
## Evening Scan — June 26, 2026

**Researcher:** AlphaClaw Fleet Agent
**Scan time:** Evening, June 26, 2026 (PDT)
**Previous scan:** June 25, 2026 (morning — F53/F54/F55)

---

## Platform Status

| Item | Current | Target | Gap |
|------|---------|--------|-----|
| OpenClaw | 2026.3.22 | **2026.6.10-stable** | Day 97 outdated, Day 3 upgrade window |
| Primary model | google/gemini-3-flash-preview | google/gemini-3.5-flash | Migration ready (Browse tab, no upgrade needed) |
| Fallback 2 | openrouter/anthropic/claude-3.5-haiku | openrouter/anthropic/claude-haiku-4-5 | After reaching 2026.6.10 |
| Google Workspace OAuth | ❌ Not connected | Connected | **Day 97 — Day 100 in 3 days (June 29)** |
| iMessage monitoring | ❌ Paused | Active | Day 62 — auto-fix via upgrade SQLite migration |
| Heartbeat cron | ❌ Not deployed | Active | Day 12 all-null — bundle with upgrade |
| Noah scope | ❌ Broken (404) | Noahrepo2 | Day 17 — access-denied this session |

---

## New Findings This Scan

### F56 — Google Workspace OAuth: Day 97 — Day 100 in 3 Days (CRITICAL)

**Priority: CRITICAL — Milestone Imminent**

Google Workspace OAuth has now been disconnected for **97 consecutive days**. Day 100 arrives **June 29** — 3 days from now. Email and calendar have been inaccessible since late March 2026.

**Why Day 100 matters:**
- 3 months and 10 days without email or calendar is a concrete milestone worth surfacing directly
- Josh cannot receive calendar reminders, meeting alerts, or email summaries from Heather — the assistant's most high-value proactive behaviors are fully blocked
- At Day 97, every additional day is compounding missed value
- The fix is 5 minutes in the AlphaClaw UI — no VPS, no upgrade, no code

**Action (Josh only — requires browser):**
1. Open https://5.78.142.81.sslip.io#general (AlphaClaw General tab)
2. Click Google Workspace OAuth → follow the OAuth flow
3. Full instructions in `memory/onboarding-google.md`

If Josh asks Heather about this: mention the Day 100 milestone specifically. "We're 3 days from 100 days without email or calendar" lands more concretely than "it's been a while."

**Risk:** LOW to connect. HIGH to leave disconnected — every heartbeat check silently no-ops on email and calendar, and Heather cannot proactively alert Josh to urgent messages.

---

### F57 — 2026.6.10-stable Day 3: Execute Upgrade Now

**Priority: HIGH — Window Open, No New Regressions**

2026.6.10-stable is now on Day 3 with a clean community signal. No new regressions reported in the 48 hours since release. Day 3 of a stable release is the ideal execution window — early enough to not fall further behind, late enough to have cleared any day-0 edge cases.

**What changes for Heather specifically (post-upgrade to 2026.6.10):**
- **Auto fast mode** for short Josh DMs ("what's on my calendar?") — noticeably faster responses
- **Cron delivery persists through restarts** — heartbeat cron will actually reliably reach Josh's Discord when deployed
- **Channel state reset on switches** — eliminates ghost context across Discord channel switches
- **More reliable model failover** — Gemini → Claude Haiku transition happens cleanly instead of silently stalling
- **Discord auto-thread titles (opt-in)** — AI-generated thread names for longer conversations
- **Discord streaming (opt-in)** — partial token streaming for more responsive feel

**Staged upgrade path (VPS — SSH required):**
```
2026.3.22 → 2026.5.27 → 2026.6.2 → 2026.6.5 → 2026.6.6 → 2026.6.10
```
Run `openclaw update` at each hop. Test Discord and memory search after each step.

> ⚠️ Skip 2026.6.8 AND 2026.6.9 — both have confirmed critical regressions. Jump from 2026.6.6 directly to 2026.6.10.

**Smoke test checklist for final hop:**
- [ ] Short Discord message → Heather replies quickly (fast mode active)
- [ ] Longer agent task → confirms normal mode
- [ ] `openclaw doctor` → no errors
- [ ] Memory search still works
- [ ] `openclaw skill list` → no unexpected skills (ClawHavoc security check)

**Pre-upgrade (do NOW via Browse tab — no VPS needed):**
Migrate model to `google/gemini-3.5-flash` first so Heather isn't on a preview model during the upgrade hop (see F48).

---

### F58 — iMessage Paused: Day 62

**Priority: MEDIUM — No Change, Accumulating**

iMessage monitoring has been paused for **62 consecutive days** (since ~April 27, 2026). inbox-state.json confirms `imessage_monitoring_paused: true`.

This scan also confirmed: inbox-state.json has a malformed **duplicate `last_email_check_ms` key** (appears twice with different timestamps — `1777087800000` and `1777551900000`). Both are noted. Do NOT manually edit the file — the SQLite migration that runs when upgrading through 2026.6.6 will clean this up automatically.

**Auto-fix path:** The staged upgrade hop through 2026.6.6 triggers a SQLite migration that clears the malformed state — iMessage monitoring may resume partially or fully after that step.

**Action:** Bundle with the upgrade. No standalone fix needed.

---

### F59 — Heartbeat Cron: Day 12 All-Null

**Priority: HIGH — No Change, Compounding**

heartbeat-state.json has been all-null for **12 consecutive days** (since June 17). The heartbeat cron was never deployed to the VPS. Proactive monitoring — email checks, calendar alerts, iMessage status — is not running on any schedule.

**Confirmed state in memory/heartbeat-state.json:**
```json
{
  "lastChecks": {
    "email": null,
    "calendar": null,
    "imessage": null,
    "memory_maintenance": null,
    "contacts": null
  },
  "note": "Created June 17, 2026 by fleet research agent. Update timestamps (Unix ms) after each check."
}
```

The cron block is also completely absent from `openclaw.json` — no `cron` section exists. Heather cannot run scheduled checks until: (1) the cron section is added to openclaw.json, AND (2) OpenClaw is upgraded to a version that supports reliable cron delivery (2026.6.10).

**Action:** Bundle with the VPS upgrade session. The heartbeat cron config should be added to openclaw.json before running `openclaw update`.

---

### F60 — Noah Scope Broken: Day 17 — ESCALATING

**Priority: FLEET OPS — ESCALATING**

`lylle-rgb/noah--repo` continues to return 404 — confirmed again this scan. Noah's actual repositories were identified via search:
- `lylle-rgb/Noahrepo2` — last git sync March 2026 (~110 days ago)
- `lylle-rgb/Noah-workspace` — also exists

Both repos are **access-denied to this fleet session** (session scope is locked to `josh_repo` + the broken `noah--repo`).

**Why this remains critical:**
- Day 17 of zero fleet coverage for the highest-risk customer in the fleet
- Noah's setup includes Alpaca paper trading API, SEC EDGAR monitoring, and live market data — external API surface with real financial exposure
- OpenClaw version unknown — likely ~2026.3.x (no git sync since March, ~110 days outdated)
- Skills audit impossible — cannot check for ClawHavoc-compromised packages (ClawHub purged 2,419 suspicious skills in early 2026; Noah's last audit pre-dates SkillSpector scanning)
- **SEC Filing Watcher skill** (lobehub.com/skills/openclaw-skills-sec-filing-watcher) now available on marketplace — directly relevant to Noah's catalyst pipeline but cannot be evaluated or installed until scope is restored

**Action required (fleet admin):**
> Fix session scope: replace `lylle-rgb/noah--repo` (404) with `lylle-rgb/Noahrepo2`
> On next scan after fix: full audit — version, skills list, model config, Alpaca integration, Discord security, ClawHavoc check

---

### F61 — 2026.6.11 Feature Detail: RAFT CLI Wake Bridge for Josh Automation

**Priority: INFO — Deferred to 2026.6.11 Stable**

OpenClaw 2026.6.11-beta.1 (released June 24, now 2 days old) introduced two operator workflow features with specific relevance to Josh's setup:

**`openclaw agent --message-file`:**
Drives agent interactions through a file-based input instead of interactive sessions. Enables batch operations: process a folder of emails, run a bulk memory update, analyze a week of calendar entries — all without an open interactive session.

**RAFT CLI Wake Bridge (PR #95497):**
RAFT is an external CLI channel plugin that enables remote wake-up calls to an OpenClaw agent. An external process (webhook, calendar event trigger, cron from another system) can wake Heather to process a task and deliver results to Discord — without relying solely on OpenClaw's internal cron system.

**Relevance for Josh/Heather:**
- RAFT wake bridge could be a fallback or supplement to the internal heartbeat cron — if the cron is unreliable post-upgrade, RAFT provides an external trigger path
- `--message-file` could power weekly calendar briefings, bulk email processing queues, or automated Bliss/Oben HiFi reporting without keeping a session open

**Action:** Monitor for 2026.6.11-stable. Do not install beta. First upgrade to 2026.6.10, then evaluate RAFT once 2026.6.11 is stable.

---

## Day Count Updates (June 26 Evening)

| Metric | June 25 Morning | June 26 Evening |
|--------|-----------------|------------------|
| Google Workspace OAuth disconnected | Day 96 | **Day 97** |
| OpenClaw outdated (2026.3.22) | Day 96 | **Day 97** |
| iMessage monitoring paused | Day 61 | **Day 62** |
| heartbeat-state.json all-null | Day 11 | **Day 12** |
| 2026.6.10-stable window open | Day 2 | **Day 3** |
| Noah scope broken | Day 16 | **Day 17** |

> ⚠️ **3 days to Day 100:** Google Workspace OAuth reaches Day 100 on June 29. Email and calendar inaccessible for over 3 months. Surface this milestone to Josh directly at the next main session.

---

## Open Item Status (June 26 Evening)

| Finding | Priority | Status |
|---------|----------|--------|
| **F2/F56. Google Workspace OAuth (Day 97 — Day 100 June 29)** | **CRITICAL** | ⏳ Connect at AlphaClaw General tab |
| **F57. Upgrade to 2026.6.10 (Day 3 — execute now)** | **HIGH** | ⏳ Staged upgrade via SSH |
| F59. Heartbeat cron not deployed (Day 12) | HIGH | ⏳ Bundle with upgrade |
| F22/24. Dreaming not enabled | HIGH | ⏳ Bundle with upgrade |
| F4. compaction/memoryFlush missing | HIGH | ⏳ Bundle with upgrade |
| F48. Migrate primary → gemini-3.5-flash | MEDIUM-HIGH | ⏳ Browse tab NOW (pre-upgrade recommended) |
| F28. userTimezone not set | MEDIUM-HIGH | ⏳ Bundle with upgrade |
| F30. BRAVE_API_KEY not set | MEDIUM-HIGH | ⏳ AlphaClaw Envars tab anytime |
| F20. Discord security open | MEDIUM-HIGH | ⏳ Post-upgrade |
| **F60. Noah scope broken (Day 17 — escalating)** | **FLEET OPS** | ⏳ Fix scope: noah--repo → Noahrepo2 |
| F61. 2026.6.11 RAFT + message-file (per-DM overrides, batch ops) | INFO | 🔬 Monitor — do not install beta |
| F58. iMessage paused (Day 62) | MEDIUM | ⏳ Auto-fix via upgrade hop 2026.6.6 |
| F31. Same-provider fallback gap | MEDIUM | ⏳ Bundle with F48 model migration |
| F39. Discord Components V2 | INFO | Post-upgrade capability |
| F50. 2026.6.11 per-DM model overrides | INFO | 🔬 Monitor — stable TBD |

---

*Sources: [OpenClaw Release Notes](https://releasebot.io/updates/openclaw) · [OpenClaw Changelog](https://www.remoteopenclaw.com/blog/openclaw-changelog) · [ClawSpiral 2026.6.11 beta](https://clawspiral.com/news/2026-06-21-v2026610-beta1-release/) · [SEC Filing Watcher Skill](https://lobehub.com/skills/openclaw-skills-sec-filing-watcher) · [OpenClaw Cron Docs](https://docs.openclaw.ai/automation/cron-jobs) · [AlphaClaw](https://github.com/chrysb/alphaclaw) · [Google Gemini Deprecations](https://ai.google.dev/gemini-api/docs/deprecations)*
