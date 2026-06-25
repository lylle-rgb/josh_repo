# Fleet Research Findings — Josh / Heather Schwartz
## Evening Scan — June 25, 2026

**Researcher:** AlphaClaw Fleet Agent
**Scan time:** Evening, June 25, 2026 (PDT)
**Previous scan:** June 24, 2026 (morning — F47/F48/F49)

---

## New Findings This Scan

### F50 — 2026.6.11-beta.1: Per-DM Model Overrides + Expanded Automation

**Priority: INFO/POSITIVE — Released June 24, 2026**

OpenClaw 2026.6.11-beta.1 shipped on June 24, the same day 2026.6.10 went stable. Preview of upcoming capabilities:

**New capabilities:**
- **Per-DM model overrides:** Configure different AI models per individual Discord DM — lighter/faster model for casual conversation, primary model for complex tasks. Directly reduces latency and cost on quick Josh exchanges.
- **Slack relay mode + native Mattermost `/oc_queue`:** Expanded platform support (not applicable to Josh's current setup but signals platform reach)
- **File-driven operator workflows:** `openclaw agent --message-file` — scripted, batch automation without interactive sessions; opens the door to offline email batching and log processing
- **Richer Discord/Telegram/WhatsApp output:** Rich HTML delivery, markdown preservation, progress drafts, improved table rendering, sticker path support
- **Codex partial delta + prompt-cache stability:** More reliable on interrupted or long streaming agent turns

**Relevance for Josh/Heather:**
- Per-DM model overrides would let Heather use a lighter model for Josh's casual Discord messages (lower cost, faster) while preserving the primary for calendar/email/business tasks — worth configuring post-upgrade to 2026.6.10
- File-driven workflows could power batch operations (process a week of emails, bulk memory updates)
- Better Discord output formatting directly improves Josh's experience

**Action:** Monitor for 2026.6.11-stable. Do not install beta. Upgrade to 2026.6.10 first (current stable target).

---

### F51 — HEARTBEAT.md Stale Version Reference Fixed (2026.6.9 → 2026.6.10) ✅

**Priority: LOW — RESOLVED in this commit**

`workspace/HEARTBEAT.md` contained a stale warning referencing 2026.6.9 as the upgrade target:
> *"You do not receive scheduled heartbeat triggers until Josh adds the cron to openclaw.json and upgrades to 2026.6.9."*

2026.6.9 has critical regressions (F26/F47) and must be skipped entirely. The correct upgrade target is **2026.6.10-stable** (released June 24).

**Fix:** Applied in this commit — HEARTBEAT.md updated to reference 2026.6.10-stable, day count corrected (8 days → 11 days as of June 25), last-updated line updated.

**Risk:** LOW. Behavioral reference only — no functional change. Prevented Heather from potentially following stale upgrade guidance referencing a broken version.

---

### F52 — Gemini Sister Model Shutdown Confirmed Today (June 25) — Primary Unaffected ✅

**Priority: INFORMATIONAL — Monitoring**

As forecasted in F43 (June 24 evening) and confirmed safe in F48 (June 24 morning):
- `gemini-3.1-flash-image-preview` → confirmed shut down **June 25, 2026** ✅
- `gemini-3-pro-image-preview` → confirmed shut down **June 25, 2026** ✅
- `google/gemini-3-flash-preview` (Heather's primary) → **operational, different model ID** — NOT affected

**Significance:** The Gemini preview deprecation cadence is now empirically confirmed — shutdown waves arrive on the announced date, no last-minute reprieves. This makes the proactive migration to `google/gemini-3.5-flash` (GA stable) more compelling: the next preview sunset may not give as much lead time, and the primary model has no GA support SLA.

**Action:** No immediate action needed. Primary model still operational. Migration to `google/gemini-3.5-flash` remains **MEDIUM-HIGH priority** — can be done anytime via AlphaClaw Browse tab, no upgrade needed.

---

## Day Count Updates (June 25 Evening)

| Metric | June 24 Morning | June 25 Evening |
|--------|-----------------|-----------------|
| Google Workspace OAuth disconnected | Day 95 | **Day 96** |
| OpenClaw outdated (2026.3.22) | Day 95 | **Day 96** |
| heartbeat-state.json all-null | Day 10 | **Day 11** |
| iMessage paused | Day 60 | **Day 61** |
| Upgrade window open (2026.6.10-stable) | Day 0 | **Day 1** |
| Noah scope broken | Day 15 | **Day 16** |

> ⚠️ **Milestone approaching:** Google Workspace OAuth reaches **Day 100 on June 29** (4 days from now). Email and calendar have been inaccessible for over 3 months. Recommend flagging this milestone to Josh at the next main session — it's a meaningful threshold worth surfacing directly.

---

## Open Item Status (June 25 Evening)

| Finding | Priority | Status |
|---------|----------|--------|
| **F2. Google Workspace OAuth (Day 96 — Day 100 in 4 days)** | **CRITICAL** | ⏳ Connect at AlphaClaw UI |
| **F47. Upgrade to 2026.6.10 (Day 1 of window)** | **HIGH** | ⏳ Window open |
| F27. Heartbeat cron not deployed — Day 11 | HIGH | ⏳ Bundle with upgrade |
| F22/24. Dreaming not enabled | HIGH | ⏳ Bundle with upgrade |
| F4. compaction/memoryFlush missing | HIGH | ⏳ Bundle with upgrade |
| F50. 2026.6.11-beta.1 features (per-DM overrides, file workflows) | INFO | 🔬 Monitor — stable TBD |
| **F51. HEARTBEAT.md stale 2026.6.9 ref** | LOW | ✅ Fixed in this commit |
| F52. Gemini sister models shut June 25 — primary safe | INFO | ✅ Primary unaffected |
| F48. Migrate primary → gemini-3.5-flash | MEDIUM-HIGH | ⏳ Browse tab anytime |
| F49. Noah scope broken — Day 16 | FLEET OPS | ⏳ Fix scope |
| F30. BRAVE_API_KEY not set | MEDIUM-HIGH | ⏳ AlphaClaw UI anytime |
| F28. userTimezone not set | MEDIUM-HIGH | ⏳ Bundle with upgrade |
| F20. Discord security open | MEDIUM-HIGH | ⏳ Post-upgrade |
| F31. Same-provider fallback gap | MEDIUM | ⏳ Bundle with F48 model migration |
| F39. Discord Components V2 | INFO | Post-upgrade capability |
| F40. Group chat context every turn | INFO | Auto in 2026.6.10 |

---

*Sources: [OpenClaw GitHub Releases](https://github.com/openclaw/openclaw/releases) · [Google Gemini Deprecations](https://ai.google.dev/gemini-api/docs/deprecations) · [OpenClaw Cron Docs](https://docs.openclaw.ai/automation/cron-jobs) · [AlphaClaw](https://github.com/chrysb/alphaclaw)*
