# Fleet Research — Josh / Heather Schwartz — Evening Scan

**Scan Date:** 2026-05-19 (Evening — Day 32)
**Agent:** AlphaClaw Apex Fleet Research Agent
**Instance:** Josh / Heather Schwartz — Discord bot personal assistant (iMessage, email, calendar, contacts)
**OpenClaw Version:** 2026.3.22 (meta.lastTouchedVersion) — now 21+ stable releases behind 2026.5.18
**Previous Findings:** findings-2026-05-18-evening.md (Day 31 Evening, Findings 1–71)
**Cumulative Open Findings:** 79 (8 new this evening, 0 resolved)

---

## Platform News — New Since Yesterday's Evening Scan (May 18)

| Item | Detail |
|---|---|
| **OpenClaw 2026.5.18 stable released** | The 2026.5.18 stable build dropped today, incorporating features from the recent beta track into the stable channel. This is now the recommended production version. Josh is on 2026.3.22 — gap is now 21+ stable releases. Key newly-stable features listed below. |
| **defineToolPlugin now stable** | The `defineToolPlugin()` API, previewed in beta.6, is now stable in 2026.5.18. Plugins can define first-class typed tools that appear natively in the agent's tool namespace. For Josh: this is the architectural path to a proper Google Workspace plugin (Gmail/Calendar/Contacts as agent tools) rather than shell-wrapped gog-cli calls. Not actionable until Josh upgrades. |
| **Active Memory per-conversation filters** | New `allowedChatIds` and `deniedChatIds` fields on Active Memory allow scoping recall to specific chat contexts. For Heather: this solves the MEMORY.md security concern from AGENTS.md — instead of relying on agent discipline to not load MEMORY.md in group chats, the platform can enforce it at the harness level. This is the right long-term architecture for Josh's private data. |
| **TTS full upgrade now stable** | ElevenLabs v3 provider, Azure Speech, `/tts latest`, chat-scoped auto-TTS controls, per-agent/per-account voice overrides and personas are now stable. If/when Josh decides to enable voice for Heather, ElevenLabs v3 is the highest-quality option available. Previously beta-only. |
| **Realtime Android voice sessions** | Android devices can now join realtime voice sessions via the Gemini voice bridge, with paced audio streaming, backpressure-aware buffering, and barge-in queue clearing. Not directly relevant to Heather's current Discord/text deployment, but opens a future modality: voice interaction with Heather via mobile. |
| **OpenTelemetry expanded** | Model calls, token usage, tool loops, harness runs, exec processes, outbound delivery, context assembly, and memory pressure now get OpenTelemetry coverage across the runtime. Practical benefit: Josh can instrument the AlphaClaw deployment to track token costs per heartbeat, session durations, and model fallback rates — especially valuable once Heather's heartbeats are active. |
| **Node.js 22.19 minimum** | OpenClaw 2026.5.18 raises the minimum supported Node.js version to 22.19. Before Josh upgrades, the VPS Node.js version must be verified. Hetzner VPS (5.78.142.81) — if running Node.js < 22.19, the OpenClaw upgrade will fail on startup. |
| **AlphaClaw: OPENCLAW_STATE_DIR durable state** | AlphaClaw now exports `OPENCLAW_STATE_DIR` through managed startup, directing OpenClaw plugins to write persistent artifacts under `/data/.openclaw` instead of ephemeral `/tmp` paths. This means any future plugin Josh installs (Google Workspace, Gmail, etc.) will persist its state across VPS reboots. Prior to this fix, plugin state was lost on restart. |
| **AlphaClaw: Docker EBUSY self-update fix** | AlphaClaw fixed self-update failures on Docker deployments (EBUSY error when npm tried to rename directories held open by the running process). If Josh's Hetzner deployment uses Docker (likely via Railway or standard AlphaClaw deploy pattern), this fix ensures in-app AlphaClaw updates work reliably. |
| **Pi packages 0.75.1** | Dependency update in AlphaClaw/OpenClaw ecosystem. No user-facing behavior change, but part of the upgrade bundle for 2026.5.18. |

---

## New Findings — Evening Scan (72–79)

---

### Finding 72 — OpenClaw 2026.5.18 Stable Released: Josh Now 21+ Releases Behind (MEDIUM)

**Risk:** MEDIUM
**Days Pending:** 0 (new today)

**Description:**
OpenClaw 2026.5.18 is now the current stable release. Josh was already identified as behind (20 releases, per Finding 61/Finding 78 carried). The gap has widened by one more stable release.

More importantly: the features that were beta-only yesterday are now stable:
- `defineToolPlugin()` API — enables native Google Workspace tooling
- Cron `--wait` flag — enables synchronous heartbeat patterns
- Grok OAuth — enables X/Twitter social monitoring
- ElevenLabs v3 TTS — production-quality voice
- Active Memory per-conversation filters — harness-level MEMORY.md security

The practical meaning: once Josh upgrades to 2026.5.18, all of these capabilities are production-ready — not experimental.

**The upgrade dependency chain for Josh:**
1. Verify Node.js ≥ 22.19 on Hetzner VPS (Finding 76 — new today)
2. Back up `openclaw.json` (`cp openclaw.json openclaw.json.bak-pre-5.18`)
3. Apply upgrade via AlphaClaw Control UI at https://5.78.142.81.sslip.io
4. Verify session startup after upgrade

**Action:** Add Node.js version check to the pre-upgrade checklist. Target upgrade: 2026.5.18 (skip intermediate 2026.5.12 target from prior findings — go directly to current stable).

**Risk Assessment:** The upgrade itself is low-risk given AlphaClaw's one-click apply. The risk of staying on 2026.3.22 increases with each release: security patches, bug fixes, and capability gaps continue to compound.

---

### Finding 73 — Active Memory Per-Conversation Filters: MEMORY.md Security Architecture Upgrade (MEDIUM/Opportunity)

**Risk:** MEDIUM (opportunity — changes recommended architecture)
**Days Pending:** 0 (new in stable 2026.5.18)

**Description:**
Active Memory now supports `allowedChatIds` and `deniedChatIds` filters that restrict memory recall to specific chat contexts. This changes the recommended security architecture for Josh's MEMORY.md.

**Current AGENTS.md design (Finding 50/carried):** Relies on agent discipline — "ONLY load in main session (direct chats with your human). DO NOT load in shared contexts (Discord, group chats)". This is a behavioral instruction, not a technical enforcement. If Heather is confused about context, MEMORY.md could be inadvertently loaded in a group chat.

**Better design with Active Memory filters:** Configure `allowedChatIds` to include only Josh's private DM channel ID on Discord. MEMORY.md recall will be blocked at the harness level in group channels regardless of what Heather's context says.

Josh's Discord setup: Guild 1484448262290276464, no mention required. His DM channel ID is a separate value that would need to be looked up once Active Memory is configured.

**Action (post-upgrade to 2026.5.18):**
1. Configure Active Memory in `openclaw.json` with `allowedChatIds: ["<Josh's DM channel ID>"]`
2. Remove the "ONLY load in main session" behavioral instruction from AGENTS.md — replace with "Active Memory filters enforce this at the platform level"
3. This transforms a behavioral rule into an architectural guarantee.

**Risk Assessment:** No immediate risk — Active Memory isn't configured yet. Design note for when it is.

---

### Finding 74 — ElevenLabs v3 Now Stable: TTS Upgrade Path Ready When Josh Enables Voice (LOW/Opportunity)

**Risk:** LOW (future opportunity)
**Days Pending:** 0 (new — promoted to stable)

**Description:**
ElevenLabs v3 TTS provider, along with Azure Speech, Inworld, and others, is now in the stable channel. TOOLS.md has a placeholder: "TTS: Not configured. ElevenLabs (sag) not installed." The voice capability now available is significantly better than what existed at Josh's last configuration date (March 2026).

For Heather's persona: ElevenLabs v3 supports per-agent/per-account voice personas with custom voice overrides. This means Heather could have a consistent voice identity across all of Josh's Discord servers and DMs — a persona that matches the "Sharp, Helpful, Resourceful" vibe in IDENTITY.md.

The `/tts latest` command and chat-scoped auto-TTS controls also mean Josh could configure certain conversation types (e.g., summaries, reminders) to be delivered as voice by default.

**Action:**
- No action until Josh decides to enable voice
- When he does: ElevenLabs v3 via the `sag` skill is the recommended provider
- Add to TOOLS.md once populated: "TTS option: ElevenLabs v3 (via sag skill) — available in stable 2026.5.18+"

**Risk Assessment:** Zero. Pure opportunity.

---

### Finding 75 — Node.js 22.19 Minimum: Verify VPS Before Upgrade (MEDIUM)

**Risk:** MEDIUM (pre-upgrade blocker if not met)
**Days Pending:** 0 (new today)

**Description:**
OpenClaw 2026.5.18 raises the minimum Node.js requirement to 22.19. Josh's VPS was last configured in March 2026. The Node.js version at that time is unknown — it could be on 22.x but below 22.19, or potentially on 20.x LTS which is no longer supported by OpenClaw 2026.5.18.

If the upgrade is attempted without verifying Node.js, the OpenClaw process will fail to start after the upgrade, leaving Josh's Discord bot offline until Node.js is manually updated.

**Action:**
1. SSH to Hetzner VPS (5.78.142.81) or check AlphaClaw Control UI system info
2. Run: `node --version`
3. If output is ≥ 22.19: proceed with OpenClaw upgrade
4. If output is < 22.19: update Node.js first via `nvm install 22 && nvm use 22` or equivalent
5. AlphaClaw 0.9.15+ handles Node.js version detection — the Control UI may surface this check automatically

**Risk Assessment:** MEDIUM. This is a silent blocker: the upgrade will appear to succeed (files downloaded) but OpenClaw won't start. Pre-check takes 30 seconds.

---

### Finding 76 — AlphaClaw OPENCLAW_STATE_DIR: Plugin State Now Durable Across Reboots (LOW/Operational)

**Risk:** LOW (operational improvement)
**Days Pending:** 0 (new today)

**Description:**
AlphaClaw now exports `OPENCLAW_STATE_DIR` pointing to `/data/.openclaw`, directing plugins to write persistent state there rather than `/tmp` (ephemeral). Prior to this, any plugin Josh installed would lose its state on VPS reboot.

For Josh's setup, this has two practical implications:
1. **Current:** usage-tracker plugin state will now persist across reboots — no more loss of token usage history
2. **Future:** when the Google Workspace plugin is installed (post-upgrade), OAuth tokens and auth state will persist across reboots. Previously, OAuth might have needed to be re-completed after every VPS restart.

This fix is included in AlphaClaw 0.9.15+. Josh's AlphaClaw version is unknown but suspected ≤0.8.x if last touched March 2026. This is another reason to update AlphaClaw first.

**Action:** No direct action beyond the AlphaClaw update already recommended in Finding 66. Note this as a resolved concern once AlphaClaw 0.9.16 is confirmed.

**Risk Assessment:** Low. Passive improvement. Becomes significant once Google integration is live.

---

### Finding 77 — BOOTSTRAP.md Present Day 32: CRITICAL Escalation (CRITICAL)

**Risk:** CRITICAL (escalated from HIGH on Day 31)
**Days Pending:** 32
**Previous:** Finding 62 (Day 30 MEDIUM), Finding 69 (Day 31 HIGH)

**Description:**
BOOTSTRAP.md is still present on Day 32. Escalating to CRITICAL.

This has been documented in 6 consecutive scans. The action takes 30 seconds. The fact that it has not been completed after being classified HIGH and then CRITICAL suggests either:
(a) The daily implementation queue is not being read by the operator, or
(b) There is a misunderstanding about what BOOTSTRAP.md does

For clarity: BOOTSTRAP.md instructs the agent to behave as if it has never met anyone before — "figure out who you are". IDENTITY.md already answers every question BOOTSTRAP.md would ask. SOUL.md answers the rest. Every session Heather starts with BOOTSTRAP.md in the workspace, she is receiving contradictory signals about her identity.

**The file to delete:** `workspace/BOOTSTRAP.md`

No backup needed. No dependencies. Zero risk.

**Action:** Delete the file. Right now. This is the only finding in 32 days that takes less than 30 seconds and has been carried for 32 days.

**Risk Assessment:** ZERO RISK TO DELETE. The risk of keeping it is a slightly confused agent every session.

---

### Finding 78 — Memory Non-Functional Day 32: Context Loss Now Compounding Toward Irrecoverability (HIGH → CRITICAL)

**Risk:** CRITICAL (escalated from HIGH)
**Days Pending:** 32
**Previous:** Finding 50 (Day 1), Finding 63 (Day 30 HIGH), Finding 70 (Day 31 HIGH)

**Description:**
Day 32. Zero daily memory logs. Zero MEMORY.md. Zero continuity.

Today's consequences:
- USER.md still contains "just joined the Discord server" — Josh joined 32 days ago
- SOUL.md has no record of the no-emoji hard rule (Finding 60) — it must be re-inferred from USER.md every session
- Heather meets Josh as a stranger every single day
- 32 sessions of learned preferences, corrections, and relationship context are permanently gone

The word "irrecoverably" is now accurate. Session 1's context cannot be reconstructed. But session 32 onward can still be saved if logging starts today.

The Mem0 temporal memory research finding (carried from Day 30) is directly relevant here: memory entries structured with timestamps and entity relationships are 29.6 points more retrievable on temporal queries. This means it's not just about writing logs — it's about writing them in a structured way. See soul-improvements-2026-05-19-evening.md for a paste-ready structured daily log format.

**Action (5 minutes — start now):**

Create `workspace/memory/2026-05-19.md` with at minimum:
```markdown
# Session Log — 2026-05-19
## Key Context
- Josh: Founder/CEO Bliss, Partner Oben HiFi, LA (PST/PDT)
- STRICT: NO EMOJI REACTIONS — absolute rule
- Google account not connected — this is the primary blocker for all integrations
- BOOTSTRAP.md needs to be deleted
## Today
- (what happened)
## Open
- (what Josh asked about)
```

**Risk Assessment:** CRITICAL — not because logging is dangerous, but because every day of inaction permanently buries the relationship context that makes Heather actually useful.

---

### Finding 79 — Heartbeat Architecture Update: Cron --wait + Active Memory Filters Now Stable (LOW/Opportunity)

**Risk:** LOW (design improvement, now actionable)
**Days Pending:** 0 (promoted to stable today)

**Description:**
Two heartbeat design improvements are now in stable (2026.5.18), making the HEARTBEAT.md design recommended in soul-improvements-2026-05-18-evening.md directly implementable post-upgrade:

1. **Cron `--wait` flag:** A cron heartbeat task can now block until the check completes and return the result, enabling the conditional notification pattern: check → if actionable → notify Josh → else silent. No more "nothing to report" messages clogging Josh's Discord.

2. **Active Memory `allowedChatIds`:** Heartbeat tasks can be configured to only recall personal context (MEMORY.md) when running in Josh's private DM channel, not in heartbeat cron contexts. This prevents personal context from leaking into automated check outputs.

**Action (post-upgrade to 2026.5.18):**
- Update HEARTBEAT.md design to use `--wait` flag explicitly in cron definitions
- Configure Active Memory allowedChatIds to scope personal memory to Josh's DM channel only
- See soul-improvements-2026-05-19-evening.md for updated HEARTBEAT.md template

**Risk Assessment:** Zero. Design note for post-upgrade activation.

---

## Day 32 Implementation Order

### Right Now (Under 5 Minutes Total, Zero Dependencies)

1. **Delete BOOTSTRAP.md** (Finding 77 — escalated CRITICAL): 30 seconds. 32 days overdue. This is the last day this will appear as a standalone item — further delays indicate a systemic issue with the implementation queue.
2. **Fix retired fallback model** (Finding 59 carried): 3 min. Replace `openrouter/anthropic/claude-3.5-haiku` with `openrouter/anthropic/claude-haiku-4-5` in openclaw.json.
3. **Start daily memory log** (Finding 78 — escalated CRITICAL): Create `workspace/memory/2026-05-19.md`. 5 minutes. Use structured format from soul-improvements-2026-05-19-evening.md.

### This Weekend — Pre-Upgrade Checklist

4. **Verify Node.js version** (Finding 75): SSH to VPS → `node --version` → confirm ≥ 22.19
5. **Back up openclaw.json**: `cp openclaw.json openclaw.json.bak-pre-5.18`
6. **Update AlphaClaw to 0.9.16** (Finding 66 carried): AlphaClaw Control UI → Updates → Apply
7. **Upgrade OpenClaw to 2026.5.18** (Finding 72): AlphaClaw Control UI → OpenClaw → Upgrade
8. **Connect Google Account** (Finding 56 — CRITICAL — Day 32): https://5.78.142.81.sslip.io → Google OAuth. 32 days without Gmail/Calendar/Contacts = 32 days of zero primary functionality.

### Post-Upgrade (This Week or Next)

9. **Configure Active Memory allowedChatIds** (Finding 73): Scope MEMORY.md recall to Josh's private DM channel only.
10. **Design HEARTBEAT.md with cron --wait** (Finding 79): Use updated template from soul-improvements file.
11. **Add no-emoji rule to SOUL.md** (Finding 60 carried)
12. **Populate TOOLS.md** (Finding 64 carried)
13. **Enrich USER.md** (Finding 63 carried)

---

## Persistent Findings Status Table — Day 32 Evening

| # | Title | Risk | Days Open |
|---|---|---|---|
| 48/56 | Google account never connected | CRITICAL | 32 |
| 49/57 | inbox-state.json invalid + iMessage paused | HIGH | 5 |
| 50 | No MEMORY.md | HIGH | 32 |
| 52 | No active heartbeat | MEDIUM | Unknown |
| 53/59 | Retired fallback model claude-3.5-haiku | MEDIUM | 5 |
| 54/61 | 21+ releases behind stable (2026.5.18) | MEDIUM | 57+ |
| 55/60 | SOUL.md no-emoji rule absent | MEDIUM | 5 |
| 62/69/77 | BOOTSTRAP.md not deleted — Day 32 (CRITICAL) | CRITICAL | 32 |
| 63/70/78 | No daily memory logs — 32 sessions lost (CRITICAL) | CRITICAL | 32 |
| 64 | TOOLS.md unpopulated | LOW | 2 |
| 66 | AlphaClaw 0.9.16 unverified | MEDIUM | 1 |
| 67 | defineToolPlugin — Google plugin path (now stable) | LOW | 1 |
| 68 | Grok OAuth (now stable) — social monitoring | LOW | 1 |
| 72 | OpenClaw 2026.5.18 stable — 21+ releases behind | MEDIUM | 0 |
| 73 | Active Memory allowedChatIds — MEMORY.md security | MEDIUM | 0 |
| 74 | ElevenLabs v3 now stable — TTS upgrade path | LOW | 0 |
| 75 | Node.js 22.19 minimum — pre-upgrade check required | MEDIUM | 0 |
| 76 | AlphaClaw OPENCLAW_STATE_DIR durable state | LOW | 0 |
| 77 | (see 62/69/77) | | |
| 78 | (see 63/70/78) | | |
| 79 | Cron --wait + Active Memory now stable — heartbeat design | LOW | 0 |

**Open: 79 | Resolved: 0 | Critical: 3 | High: 10+ | Medium: 25+ | Low: 10+**

---

*Generated by AlphaClaw Apex Fleet Research Agent — Evening Scan — 2026-05-19 (Day 32)*
