# Evening Scan — June 23, 2026

**Researcher:** AlphaClaw Fleet Agent
**Time:** Evening, June 23, 2026
**Previous scan:** June 22 Evening — F33–F36 (TOOLS.md conflict, 2026.6.10-beta.1, MCP features, cron improvements)
**Instance:** josh_repo (Heather Schwartz — personal assistant)

---

## Headline: No New Stable Release. 2026.6.10-beta.2 Day 2. 4 Findings (2 Applied). Upgrade Window Day 3.

---

## Platform Status

| Channel | Version | Status |
|---------|---------|--------|
| npm `latest` (stable) | **2026.6.9** | ✅ Still current — Day 3 of upgrade window |
| 2026.6.10-beta.2 | Released June 22 | 🔬 Beta Day 2 — auto fast mode; DO NOT install |
| 2026.6.8 | Skipped | ⛔ Never on npm stable; skip confirmed |
| 2026.3.22 | **Current (Josh)** | Day 93 behind stable — upgrade urgent |

No new stable release. 2026.6.9 confirmed clean. 2026.6.10-stable ETA ~1–2 weeks at current cadence.

---

## New Findings This Scan

### F38 — HEARTBEAT.md: Cron-Not-Deployed Warning Missing (APPLIED) ✅

**What was wrong:** HEARTBEAT.md described heartbeat check procedures but had no acknowledgment that the cron is **not deployed to the VPS**. On a fresh session, Heather might wait for heartbeat triggers that never arrive.

This was recommended in June 22 evening soul-improvements but not applied. Applied this scan.

**Fix applied:** Added warning block to top of `workspace/HEARTBEAT.md` (see this commit): cron not deployed, heartbeat-state.json all-null Day 8, run checks manually, remind Josh once per main session.

**Risk:** LOW — documentation only. Removes active confusion risk.

---

### F39 — Discord Components V2: Interactive Actions Post-Upgrade (Informational) ℹ️

**What's new in 2026.6.9:** Discord Components V2 fully supported. After upgrade, Heather can deliver:
- **Buttons** — action confirmations ("Send this email? ✓ / ✗")
- **Select menus** — dropdowns for option selection
- **Modals** — multi-field input forms
- **Attachment-backed file blocks** — native file delivery

**Why it matters for Josh/Heather:** Before sending any external action (email, calendar event, social post), Heather can offer a Discord button prompt rather than a text question. Cleaner UX. Directly serves Josh's "ask before acting externally" preference in SOUL.md.

**Action:** None until after upgrade. Post-upgrade: add button confirmations for email send, calendar create, social post. See Rec 14 in soul-improvements.

**Risk:** LOW — informational. Capability arrives automatically with 2026.6.9 upgrade.

---

### F40 — Group Chat Context Injection: Every Turn Now (Informational) ℹ️

**What changed in 2026.6.x:** Context in group chats is now injected on **every turn**, not just the first. Previously, Heather could "forget" participant context mid-conversation in Discord channels. Now she maintains full context throughout.

**Why it matters:** In multi-member Discord channels, no more mid-conversation drift. Particularly relevant if Josh's Discord server gains more active users.

**Action:** None — automatically applied after upgrade. No config needed.

---

### F41 — MEMORY.md Day Counts Stale by 2 Days (APPLIED) ✅

**What was wrong:** MEMORY.md header said "Last updated: 2026-06-21" with day counts off by 2:
- Google Workspace: was Day 91, now Day 93
- Heartbeat null: was 5 consecutive days, now Day 8
- iMessage paused: was ~56 days, now Day 58

**Fix applied:** MEMORY.md updated in this commit — last-updated date, day counts, and Lessons Learned section all refreshed.

---

## Standing Alerts (Updated — June 23)

| Alert | Days | Priority |
|-------|------|----------|
| Google Workspace OAuth not connected | **Day 93** | 🔴 CRITICAL |
| Heartbeat cron not deployed — all-null state | **Day 8** | 🔴 HIGH |
| iMessage paused (auto-fix on upgrade) | **Day 58** | 🔴 HIGH |
| OpenClaw 2026.3.22 — upgrade window OPEN | **Day 3 of window** | 🔴 HIGH |
| BRAVE_API_KEY not set (F30) | — | 🟠 MEDIUM-HIGH |
| Discord open to all (`allowFrom: ["*"]`) | Day 93 | 🟠 MEDIUM-HIGH |
| Same-provider fallback chain gap (F31) | — | 🟡 MEDIUM |
| Noah session scope broken (`noah--repo` 404) | **Day 12** | Fleet ops |

---

## Noah Repo: Scope Gap — Day 12

`lylle-rgb/noah--repo` remains a GitHub 404. Confirmed repos found via search:
- `lylle-rgb/Noahrepo2` — outside session scope
- `lylle-rgb/Noah-workspace` — outside session scope

No fleet research can be performed for Noah's Market Catalyst Agent until fleet admin corrects the session scope.

---

## What Josh Needs to Do

**Can do NOW — AlphaClaw UI, no VPS needed:**
- 🟠 Add BRAVE_API_KEY via AlphaClaw UI → Envars tab (enables web search immediately)

**Bundle into one VPS session:**
1. 🔴 Connect Google Workspace OAuth at https://5.78.142.81.sslip.io#general (Day 93)
2. 🔴 Add `userTimezone: "America/Los_Angeles"` to `agents.defaults` (Finding 28 — do FIRST)
3. 🔴 Add compaction/memoryFlush block (Finding 4)
4. 🔴 Verify dreaming config key path (Finding 36), add dreaming config (Finding 22/24)
5. 🔴 Add heartbeat cron job to `cron.jobs` (Finding 27)
6. 🔴 Run staged upgrade: 2026.3.22 → 2026.5.27 → 2026.6.2 → 2026.6.5 → 2026.6.6 → 2026.6.9

**After upgrade:**
7. Fix fallback chain order — Haiku 4.5 first, Gemini Flash second (Finding 31)
8. Tighten `allowFrom: ["*"]` → Josh's Discord user ID (Finding 20)
9. Enable Discord streaming `"progress"` mode
10. Explore Discord Components V2 for interactive confirmations (F39)
