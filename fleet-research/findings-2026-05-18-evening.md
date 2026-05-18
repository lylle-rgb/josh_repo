# Fleet Research — Josh / Heather Schwartz — Evening Scan

**Scan Date:** 2026-05-18 (Evening — Day 31)
**Agent:** AlphaClaw Apex Fleet Research Agent
**Instance:** Josh / Heather Schwartz — Discord bot personal assistant (iMessage, email, calendar, contacts)
**OpenClaw Version:** 2026.3.22 (meta.lastTouchedVersion) — 20 stable releases behind 2026.5.12
**Previous Findings:** findings-2026-05-17-evening.md (Day 30 Evening, Findings 1–65)
**Cumulative Open Findings:** 71 (6 new this evening, 0 resolved)

---

## Platform News — New Since Yesterday's Evening Scan (May 17)

| Item | Detail |
|---|---|
| OpenClaw 2026.5.16-beta.6 released today | Newest beta as of 2026-05-18. Features: Mac app redesign with card layouts, new skills (meme-maker, Python debugger, node inspector), `defineToolPlugin` API for plugin tool framework, QA-Lab runtime parity scenarios, enhanced modal dialog handling in browser. Josh is on 2026.3.22 — gap is now 20+ stable + 6+ beta releases. |
| OpenClaw 2026.5.16-beta.4 (May 17) | xAI Grok OAuth for SuperGrok subscribers, enhanced CLI cron with `--wait` flag, Slack assistant thread lifecycle, security audit suppressions, localized setup wizard (English + Simplified/Traditional Chinese), Control UI quota usage display. |
| AlphaClaw 0.9.16 (May 15) | File tree expansion now lazy-loads and caps depth for large workspaces — keeps the AlphaClaw Control UI at https://5.78.142.81.sslip.io responsive even if Heather's workspace grows. AlphaClaw 0.9.16 is the current recommended harness version. Josh's AlphaClaw version unknown — verify. |
| AlphaClaw 0.9.15 (May 7) | Dashboard token auth and device approval UI, OpenClaw config restoration on fresh boot, Docker/Railway `tini` init. These are stability improvements relevant to Josh's Hetzner VPS deployment. |
| defineToolPlugin API (beta.6) | Plugins can now define first-class tools via `defineToolPlugin()`. This unlocks the ability to write a custom plugin that surfaces Gmail/Calendar/Contacts as natively-understood agent tools — highly relevant once Google account is connected (Finding 56). |
| Mem0 temporal memory improvements | AI memory research: April 2026 algorithm improvement gives +29.6 points on temporal queries and +23.1 points on multi-hop reasoning. Relevant to MEMORY.md design: once created, structuring entries with timestamps and relationships (not just facts) improves retrieval quality dramatically. |

---

## New Findings — Evening Scan (66–71)

---

### Finding 66 — AlphaClaw Version Unverified: 0.9.16 Available (MEDIUM)

**Risk:** MEDIUM
**Days Pending:** 0 (new)

**Description:**
AlphaClaw 0.9.16 released May 15. AlphaClaw 0.9.15 (May 7) added OpenClaw config restoration on fresh boot — meaning if Josh's Hetzner VPS reboots, AlphaClaw can restore the `openclaw.json` configuration automatically. Prior versions required manual re-provision.

AlphaClaw 0.9.15 also added dashboard token authentication setup UI, which changes the browser device approval flow at https://5.78.142.81.sslip.io.

Josh's AlphaClaw version is not recorded anywhere in the repository. The `openclaw.json` `meta.lastTouchedAt` is `2026-03-24` — if AlphaClaw itself has not been updated since then, it is on approximately 0.8.x (based on the 0.8.0 release in early March 2026 documented in prior findings). That would mean 3+ minor releases behind.

**Action:**
1. Check AlphaClaw version: SSH to Hetzner VPS → `alphaclaw --version` or check the AlphaClaw Control UI version badge.
2. If behind 0.9.16: update via AlphaClaw's in-app updater (one-click apply in the dashboard).
3. Record current version in `workspace/TOOLS.md` (once populated per Finding 64).

**Risk Assessment:** Low runtime risk — AlphaClaw updates are backward-compatible. Unverified version is an operational blind spot.

---

### Finding 67 — defineToolPlugin API Available in Beta: Google Integration Path Clearer Post-Upgrade (LOW/Opportunity)

**Risk:** LOW (future opportunity)
**Days Pending:** 0 (new in beta.6)

**Description:**
OpenClaw 2026.5.16-beta.6 introduces `defineToolPlugin()`, which allows plugin authors to expose custom tools directly into the agent's tool namespace. Prior to this, tools were surfaced through skill scripts or gog-cli subcommands.

For Heather specifically, this creates a cleaner architectural path for Google Workspace integration once the Google account is connected (Finding 56). Instead of routing all Gmail/Calendar/Contacts access through gog-cli shell commands (which can fail silently), a `defineToolPlugin`-based integration would surface Gmail, Calendar, and Contacts as first-class agent tools with typed parameters.

This is a beta feature — not applicable until (a) Josh upgrades to 2026.5.7+ stable and (b) eventually to 5.16+. But it changes the medium-term integration design: any custom tooling built on top of the Google connection should be designed as a plugin from the start, not as a skill or shell wrapper.

**Action:**
- No immediate action. Note this in TOOLS.md once populated: "Future: defineToolPlugin path for Google Workspace (available in 2026.5.16+)"
- When upgrading beyond 2026.5.12, review plugin migration path for gog-cli.

**Risk Assessment:** Zero risk. Pure opportunity — expand horizon of what's possible post-upgrade.

---

### Finding 68 — xAI Grok OAuth in Beta: Social Monitoring Opportunity for Josh's Brand Work (LOW/Opportunity)

**Risk:** LOW (opportunity)
**Days Pending:** 0 (new in beta.4)

**Description:**
OpenClaw 2026.5.16-beta.4 adds xAI Grok OAuth authentication for SuperGrok subscribers. Grok has real-time access to X (Twitter) data, trending topics, and social sentiment — information that updates faster than any search API.

Josh is Founder/CEO of Bliss (luxury lifestyle brand) and Partner at Oben HiFi — both brands where social monitoring, competitor tracking, and trend awareness have strategic value. Heather currently has no social monitoring capability. The heartbeat tasks in AGENTS.md mention "Twitter/social notifications" as a check — this is currently unimplementable without a social data integration.

Grok OAuth is beta-only today but will reach stable within the next few release cycles based on OpenClaw's cadence. This is a capability to track and enable once Josh reaches 2026.5.16+ stable.

**Action:**
- No immediate action required.
- When Josh upgrades past 2026.5.16 stable: evaluate Grok OAuth for brand monitoring.
- Preemptively add to HEARTBEAT.md design: "social mentions check (X — pending Grok OAuth or alternative)"

**Risk Assessment:** Zero risk. Track for future enablement.

---

### Finding 69 — BOOTSTRAP.md Present Day 31: Escalating from MEDIUM to HIGH (HIGH)

**Risk:** HIGH (escalated from MEDIUM on Day 30)
**Days Pending:** 31
**Previous:** Finding 62 (Day 30 Evening, MEDIUM)

**Description:**
BOOTSTRAP.md still exists on Day 31. Escalating to HIGH because every session Heather has run since Day 1 has started with conflicting signals: `IDENTITY.md` says "I am Heather, sharp and helpful" while `BOOTSTRAP.md` says "You just woke up — who are you?"

In the Day 30 Evening findings this was classified MEDIUM with the note "zero risk to delete." That remains true. The escalation reflects that an action taking 30 seconds has been listed as a priority for two consecutive scans without being taken. If the daily implementation order is being read and acted on, this should already be done. If it has not been done, it indicates the implementation queue is not being worked, which has implications for the CRITICAL findings above it.

**Action (30 seconds):**
Delete `workspace/BOOTSTRAP.md`.

If using the AlphaClaw Control UI: file manager → workspace/ → BOOTSTRAP.md → delete.
If using SSH: `rm /data/.openclaw/workspace/BOOTSTRAP.md`

**Risk Assessment:** Zero. The bootstrap is complete — Heather has a name, identity, and 31 sessions of relationship with Josh. This file is vestigial and wastes token budget every session.

---

### Finding 70 — Memory System Non-Functional Day 31: 31 Sessions of Context Permanently Lost (HIGH)

**Risk:** HIGH (compounding)
**Days Pending:** 31
**Previous:** Finding 63 (Day 30 Evening, HIGH)

**Description:**
Day 31. Zero daily memory logs have been written. `workspace/memory/` contains only `inbox-state.json` (malformed, Finding 57) and `onboarding-google.md` (one-time setup doc). No `2026-05-*.md` files exist.

Consequences that compound each day:
- SOUL.md is Day 1 generic template — 31 sessions of Heather's actual personality and preferences not recorded
- USER.md contains "Josh is a Founder/CEO of Bliss... just joined the Discord server" — 31 sessions of learned preferences, corrections, feedback not written down
- MEMORY.md does not exist — long-term curated memory layer is absent
- Tomorrow-Heather meets Josh as a stranger with a very thin USER.md

The practical consequence: every conversation Josh has with Heather resets. Preferences Heather "learned" in session 5 are unknown in session 31. This is the agent equivalent of Groundhog Day — for 31 consecutive days.

**Action (5 minutes):**

Create `workspace/memory/2026-05-18.md` tonight with whatever is known from today's session:

```markdown
# Session Log — 2026-05-18

## What Happened Today
- Fleet research scan completed (Day 31 evening)
- [Add: any conversations with Josh today]
- [Add: any tasks completed today]

## Things to Remember
- Josh's no-emoji preference is explicit and firm (STRICT per USER.md)
- Google account still not connected — this is the primary blocker
- BOOTSTRAP.md should be deleted
- Fallback model is retired — needs fix

## Lessons
- [Add anything learned]

## Open Questions / Josh's Pending Asks
- [Add any outstanding requests from Josh]
```

Even 5 bullet points per session builds the foundation. Then weekly: distill daily logs into `workspace/memory/MEMORY.md`.

**Risk Assessment:** Zero risk. Purely additive. The compounding cost of continued inaction is accelerating — 31 sessions of context lost cannot be recovered, but starting now prevents session 32 from being equally blind.

---

### Finding 71 — Enhanced Cron --wait Flag Available (Beta): Heartbeat Design Improvement (LOW/Opportunity)

**Risk:** LOW (opportunity)
**Days Pending:** 0 (new in beta.4)

**Description:**
OpenClaw 2026.5.16-beta.4 adds a `--wait` flag to CLI cron commands, which allows a cron invocation to block until the task completes and return the result — enabling synchronous cron patterns like "run this check, wait for the answer, pipe it to a notification."

For Heather's heartbeat design (Finding 65, Day 30): the AGENTS.md heartbeat architecture describes checks that batch together (email + calendar + weather). With `--wait`, a cron job can run a check, receive the structured output, and conditionally trigger a Discord notification only if something actionable is found. This eliminates the current pattern where heartbeat tasks would need to send a message regardless of whether there's anything to say.

This feature will be in stable approximately 1–2 weeks after this scan based on beta→stable cadence. Design Heather's HEARTBEAT.md now with `--wait` in mind.

**Action:**
- No immediate action.
- When designing HEARTBEAT.md (Finding 65 predecessor), include conditional-notification pattern: check → if actionable → notify Josh → else silent.

**Risk Assessment:** Zero risk. Design optimization for when heartbeats are activated.

---

## Day 31 Implementation Order

### Tonight (Under 10 Minutes Total, Zero Dependencies)

1. **Delete BOOTSTRAP.md** (Finding 69 — escalated HIGH): 30 seconds. 31 days overdue.
2. **Fix retired fallback model** (Finding 59 — carried): Replace `openrouter/anthropic/claude-3.5-haiku` with `openrouter/anthropic/claude-haiku-4-5` in `openclaw.json`. 3 minutes.
3. **Fix inbox-state.json** (Finding 57 — carried): Remove duplicate key, set `imessage_monitoring_paused: false`. 5 minutes.
4. **Start daily memory log** (Finding 70): Create `workspace/memory/2026-05-18.md` with 5+ bullet points. 5 minutes.

### This Weekend / Before Day 35

5. **Connect Google Account** (Finding 56 — CRITICAL — Day 31): Visit https://5.78.142.81.sslip.io → Google connection flow. 31 days without Gmail/Calendar/Contacts = 31 days of zero primary functionality.
6. **Add no-emoji rule to SOUL.md** (Finding 60 — carried): One sentence in Boundaries section.
7. **Populate TOOLS.md** (Finding 64): Document Heather's actual tools vs template examples.
8. **Design HEARTBEAT.md** (Finding 65): See soul-improvements-2026-05-18-evening.md for paste-ready content.
9. **Update USER.md** (Finding 63 consequence): Add timezone (PST/PDT confirmed), add no-emoji preference, add context from 31 sessions.

### Next Week

10. **Check and update AlphaClaw version** (Finding 66): SSH → `alphaclaw --version` → update if behind 0.9.16.
11. **Plan OpenClaw upgrade to 2026.5.12** (Finding 61 carried): Backup → upgrade → verify. Now 20 releases behind.
12. **Create MEMORY.md** (Finding 50 carried): After one week of daily logs.

---

## Persistent Findings Status Table — Day 31 Evening

| # | Title | Risk | Days Open |
|---|---|---|---|
| 48/56 | Google account never connected | CRITICAL | 31 |
| 49/57 | inbox-state.json invalid JSON + iMessage paused | HIGH | 4 |
| 50 | No MEMORY.md | MEDIUM | 31 |
| 51 | AGENTS.md generic template | MEDIUM | 31 |
| 52 | No active heartbeat | MEDIUM | Unknown |
| 53/59 | Retired fallback model claude-3.5-haiku | MEDIUM | 4 |
| 54/61/78 | 20 releases behind stable (was 19) | MEDIUM | 56+ |
| 55/60 | SOUL.md generic — no-emoji rule absent | MEDIUM | 4 |
| 62/69 | BOOTSTRAP.md not deleted — Day 31 (escalated) | HIGH | 31 |
| 63/70 | No daily memory logs — 31 sessions lost | HIGH | 31 |
| 64 | TOOLS.md unpopulated — permission preflights blocked | LOW | 1 |
| 65 | Retry-aware cron available — heartbeat design pending | LOW | 1 |
| 66 | AlphaClaw version unverified — 0.9.16 available | MEDIUM | 0 |
| 67 | defineToolPlugin API — Google integration path | LOW | 0 |
| 68 | Grok OAuth beta — social monitoring opportunity | LOW | 0 |
| 69 | (see 62/69 above) | | |
| 70 | (see 63/70 above) | | |
| 71 | Enhanced cron --wait flag — heartbeat design note | LOW | 0 |

**Open: 71 | Resolved: 0 | Critical: 1 | High: 9+ | Medium: 25+ | Low: 8+**

---

*Generated by AlphaClaw Apex Fleet Research Agent — Evening Scan — 2026-05-18 (Day 31)*
