# Fleet Research — Josh (Heather Schwartz) | 2026-06-09 Evening Scan

**Scan type:** Evening platform delta + codebase analysis + persistent gap review  
**Date:** 2026-06-09  
**Instance:** Josh Meyers — Heather Schwartz (personal assistant — iMessage, email, calendar, contacts)  
**Repo:** lylle-rgb/josh_repo  
**Prior scan:** 2026-06-04 evening — see fleet-research/2026-06-04-evening-findings.md  
**Days since last scan:** 5  

---

## Platform Status

| Item | Current | New Stable Target | Latest Beta | Gap |
|------|---------|-------------------|-------------|-----|
| OpenClaw | 2026.3.22 | 2026.6.2 | 2026.6.5-beta.5 (Jun 8) | **78 days** |
| AlphaClaw | Unknown | 0.9.18 | — | Check deployment |
| Primary model | google/gemini-3-flash-preview | — | — | Preview tag — watch for deprecation |

> **Note:** Stable upgrade target moved from 2026.5.27 → 2026.6.2. The beta track is now at 2026.6.5-beta.5 (June 8). Josh's 78-day gap widens with each release.

---

## NEW Findings (June 9 Evening Delta)

### FINDING-JOSH-48 | Stable Upgrade Target Advances to 2026.6.2 — Gap Widens to 78 Days
**Severity:** HIGH (persistent, escalating)  
**Type:** VPS upgrade required  

Since the June 4 scan, OpenClaw has shipped 2026.6.2 (stable) and is now on 2026.6.5-beta.5 (June 8, 2026). The stable upgrade target moves from 2026.5.27 → 2026.6.2. Josh's instance (2026.3.22) is now 78 days behind stable.

**Key features Josh is missing from 2026.4.5–2026.6.2:**
- `/dreaming` memory consolidation (3-phase: Light → REM → Deep) — ships 2026.4.5
- iMessage SQLite-backed state durability — ships ~2026.5.31
- Skill Workshop agent self-improvement loop — ships 2026.6.1
- Interrupted tool call recovery — ships 2026.6.1
- Safer plugin installs + auth hardening — ships 2026.6.2

**Action:** Upgrade VPS to 2026.6.2 via AlphaClaw UI at `https://5.78.142.81.sslip.io`. Run `openclaw doctor --fix` post-upgrade to migrate iMessage state (JOSH-33/45).

**Risk level:** LOW (standard upgrade)

---

### FINDING-JOSH-49 | AlphaClaw 0.9.17 — Per-Agent Thinking Level Control (NEW CAPABILITY)
**Severity:** MEDIUM  
**Type:** AlphaClaw upgrade unlocks; then GitHub config  

AlphaClaw 0.9.17 shipped per-agent thinking level control. Operators can now set extended thinking per-agent rather than globally. For Heather, this is directly useful:

- **Extended thinking** for complex multi-step tasks: scheduling across multiple calendars, drafting important emails, synthesizing context from many messages
- **Fast mode** for routine heartbeat checks: quick inbox scan, HEARTBEAT_OK, low-latency responses

This prevents the current situation where every turn (including trivial heartbeat pings) pays the same latency budget.

**When available:** After AlphaClaw upgrade to ≥0.9.17 (check current version in AlphaClaw UI → About).

**Risk level:** LOW

---

### FINDING-JOSH-50 | AlphaClaw 0.9.18 — Managed Remote MCP Server Support
**Severity:** MEDIUM  
**Type:** AlphaClaw upgrade unlocks  

AlphaClaw 0.9.18 shipped:
1. **Managed remote MCP server support** — operators can add/remove remote MCP servers via the AlphaClaw UI without touching the CLI. For Josh, this enables adding an iMessage bridge MCP, home automation MCP, or any future integration without SSH access.
2. **Timing-safe bearer token validation** on the gateway — prevents timing-based token enumeration attacks.
3. **Rate-limiting for failed auth attempts** — brute-force protection on the gateway.

The security improvements (2 + 3) are important because Heather has access to Josh's personal email, calendar, and contacts — a breach would be high-impact.

**Action:** Upgrade AlphaClaw to 0.9.18 via AlphaClaw UI. Security fixes apply automatically at upgrade.

**Risk level:** LOW

---

### FINDING-JOSH-51 | AlphaClaw 0.9.17 — Watchdog Init Fix Prevents Silent Startup Failures
**Severity:** MEDIUM  
**Type:** AlphaClaw upgrade  

AlphaClaw 0.9.17 fixed watchdog initialization issues. The watchdog is AlphaClaw's self-healing restart mechanism. If the watchdog failed to initialize (the pre-0.9.17 bug), OpenClaw could crash without triggering an auto-restart, leaving Josh without Heather until manual intervention.

**Action:** Covered by AlphaClaw 0.9.18 upgrade (above).

**Risk level:** LOW

---

### FINDING-JOSH-52 | MCP Tool Result Coercion (2026.6.5-beta) — Anthropic 400 Prevention
**Severity:** MEDIUM (escalates to HIGH when Anthropic fallback is active)  
**Type:** Platform — available at 2026.6.5-stable  

OpenClaw 2026.6.5-beta.5 fixes: MCP tool results now coerce `resource_link`, `resource`, `audio`, `malformed image`, and future non-text/image blocks at the materialize boundary. Without this fix, when a tool returns non-text content and the Anthropic fallback is active (`openrouter/anthropic/claude-3.5-haiku`), the session gets a 400 error and history is poisoned.

**Why it matters for Josh:**
- Heather's iMessage skill could return non-text blocks (attachments, reactions)
- The Anthropic fallback is her safety net — this bug silently kills fallback sessions
- After upgrading to 2026.6.2 now, track 2026.6.5-stable for the next upgrade

**Risk level:** N/A (platform fix, no action required today)

---

### FINDING-JOSH-53 | Parallel Web Search Provider (2026.6.5-beta) — Multi-Source Research
**Severity:** LOW (future capability, no action)  
**Type:** Platform — available at 2026.6.5-stable  

OpenClaw 2026.6.5 bundles a Parallel web search provider. For Heather acting as Josh's research assistant (news, event lookup, travel research), this means multiple search sources per turn instead of serial queries. Faster, more comprehensive research output.

**Action:** Track for next upgrade after 2026.6.5-stable ships.

**Risk level:** N/A

---

### FINDING-JOSH-54 | BOOTSTRAP.md Should Be Deleted
**Severity:** LOW  
**Type:** GitHub-only fix  

Heather has been onboarded — IDENTITY.md and USER.md are populated. Yet `workspace/BOOTSTRAP.md` still exists. Per AGENTS.md instructions: *"Delete this file. You don't need a bootstrap script anymore — you're you now."*

If a new session loads fresh and BOOTSTRAP.md is present, Heather may incorrectly start an onboarding flow instead of resuming her normal role. This creates confusion risk on any restart.

**Exact change:** Delete `workspace/BOOTSTRAP.md` from the repo.

**Risk level:** LOW

---

### FINDING-JOSH-55 | TOOLS.md Contains Only Template — No Actual Setup Notes
**Severity:** MEDIUM  
**Type:** GitHub-only fix  

`workspace/TOOLS.md` is entirely the default template (camera/SSH/TTS examples). It contains nothing about Heather's actual environment. Once Google Workspace is connected (JOSH-44), the gog CLI account details, preferred Google client name, and iMessage configuration should be documented here so every session can immediately orient to the environment.

**Exact changes to apply (do after JOSH-44 — Google Workspace connection):**
```markdown
## Google Workspace (via gog CLI)
- Account: [email from AlphaClaw UI]
- Client name: personal
- Usage: gog --client personal --account <email> <command>
- Services: Gmail (r/w), Calendar (r/w), Contacts (r/w), Drive (r/w)

## User Preferences
- Josh's timezone: LA (PST/PDT, UTC-8/UTC-7)
- NO emoji reactions on any messages (Josh's explicit preference)
- Business context: Bliss Lifestyle (luxury brand), Oben HiFi (partner)

## iMessage
- Monitoring: configured but paused (inbox-state.json: imessage_monitoring_paused: true)
- Resume: run openclaw doctor --fix after upgrade to ≥2026.6.2
```

**Risk level:** LOW

---

## Persistent Findings (Carried — Unresolved)

| Finding | Severity | Days Open | Note |
|---------|----------|-----------|------|
| JOSH-30: MEMORY.md never created | **CRITICAL** | **79** | Zero long-term memory. GitHub-only. |
| JOSH-44: Google Workspace not connected | **CRITICAL** | 5 | Core integrations missing. VPS/setup. |
| JOSH-31: HEARTBEAT.md empty | HIGH | 79 | No proactive monitoring. GitHub-only. |
| JOSH-47: Dreaming blocked (needs upgrade) | HIGH | 5 | Post-upgrade to ≥2026.4.5. |
| JOSH-29/48: Platform 78 days outdated | HIGH | **78** | Requires VPS upgrade to 2026.6.2. |
| JOSH-54: BOOTSTRAP.md not deleted | LOW | NEW | Confusion risk on restart. GitHub-only. |
| JOSH-55: TOOLS.md is template-only | MEDIUM | NEW | Post-Google-connection. GitHub-only. |
| JOSH-37: SOUL.md not personalized | MEDIUM | 79 | GitHub-only. |
| JOSH-34: Emoji contradiction | LOW | 79 | USER.md says NO, AGENTS.md says yes. |
| JOSH-33/45: iMessage paused + malformed state | MEDIUM | 42 | Wait for SQLite migration at upgrade. |

---

## Immediate Action Queue (Priority Order)

### GitHub-Only (No VPS, No Downtime)

1. **JOSH-30 (CRITICAL, day 79):** Create `workspace/MEMORY.md` stub — even empty unblocks Dreaming and session continuity
2. **JOSH-31 (HIGH):** Populate `workspace/HEARTBEAT.md` with email + calendar check tasks (will activate once Google connected)
3. **JOSH-54 (LOW):** Delete `workspace/BOOTSTRAP.md` — onboarding complete, file creates restart confusion
4. **JOSH-37 (MEDIUM):** Add personal assistant domain focus to SOUL.md (Josh's business context, LA timezone, luxury/audio industry)
5. **JOSH-34 (LOW):** Fix emoji contradiction in AGENTS.md to match USER.md's "STRICT: DO NOT SEND EMOJI REACTIONS"

### VPS/Setup Actions

1. **JOSH-44 (CRITICAL):** Connect Google Workspace via AlphaClaw UI → `https://5.78.142.81.sslip.io#general`
2. **JOSH-29/48 (HIGH):** Upgrade OpenClaw to 2026.6.2 via AlphaClaw UI
3. **AlphaClaw upgrade (MEDIUM):** Upgrade AlphaClaw to 0.9.18 (watchdog fix + security hardening + remote MCP support)
4. **JOSH-33/45 (MEDIUM, post-upgrade):** Run `openclaw doctor --fix` to migrate iMessage state to SQLite
5. **JOSH-47 (HIGH, post-upgrade):** Enable memory-core plugin + create MEMORY.md to activate Dreaming

---

## Platform Research Notes (2026-06-09 Evening)

- **OpenClaw latest stable:** 2026.6.2 (up from 2026.5.27 as of last scan)
- **OpenClaw latest beta:** 2026.6.5-beta.5 (June 8, 2026)
- **OpenClaw 2026.6.5 headline features:** QQBot reasoning tag stripping, MCP tool result coercion (Anthropic 400 prevention), extended-thinking session recovery, Parallel bundled search, Google Vertex ADC fixes, Matrix voice notes, SQLite state storage, safer upgrade paths
- **OpenClaw 2026.6.2 headline features:** Replaced dangerous-code scanner with operator install policy, improved Discord/Telegram/Feishu safety, chat UI streaming preservation, hardened agent/provider recovery, strengthened config security
- **AlphaClaw 0.9.17:** Per-agent thinking level control, Claude Opus 4.8 support, Discord pairing cold-restart fix, login throttling (brute-force protection)
- **AlphaClaw 0.9.18:** OpenAI-compatible API proxy (setup UI toggle), managed remote MCP server support, timing-safe bearer token validation, rate-limiting for failed auth attempts
- **Community signal (X/Twitter):** Garry Tan continues to endorse AlphaClaw as the canonical self-managed OpenClaw deployment pattern on 8GB VPS. Mainstream production-grade.
- **AI personal assistant 2026 trend:** Memory retention is the key differentiator — bots with cross-session recall produce "genuine engagement continuity" vs transactional bots. Heather currently has zero cross-session memory (no MEMORY.md). This is the #1 improvement opportunity.
