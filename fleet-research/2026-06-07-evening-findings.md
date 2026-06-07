# Fleet Research — Josh (Heather) | 2026-06-07 Evening Scan

**Scan type:** Platform delta + web research + persistent gap review  
**Date:** 2026-06-07  
**Instance:** Josh Meyers — Heather Schwartz (personal assistant)  
**Repo:** lylle-rgb/josh_repo  
**Prior scan:** 2026-06-03 morning (4 days ago) — zero GitHub-only fixes applied  

---

## Platform Status

| Item | Current | Latest Stable | Gap |
|------|---------|--------------|-----|
| OpenClaw | 2026.3.22 | **2026.6.2** | **76 days** |
| AlphaClaw | Unknown | 0.9.16 | Check deployment |
| Primary model | google/gemini-3-flash-preview | — | — |

---

## 🚨 Key Development Since June 3: Upgrade Target Changed

The June 3 scan had 2026.6.1-beta.3 as the latest beta and 2026.5.27 as the stable target. Since then, **OpenClaw 2026.6.2 shipped as stable**. All features previously flagged as beta are now confirmed stable. Josh should upgrade directly to **2026.6.2**, not the previously recommended 2026.5.27.

---

## NEW Findings (June 7 Evening Delta)

### FINDING-JOSH-44 | OpenClaw 2026.6.2 Stable — All Beta Features Confirmed
**Severity:** HIGH  
**Status:** NEW — Platform milestone  

OpenClaw 2026.6.2 has shipped as stable. All four features flagged as beta findings (JOSH-40 through JOSH-43) on June 3 are now confirmed stable. The upgrade target changes from 2026.5.27 to 2026.6.2.

**What's confirmed stable in 2026.6.2:**
- **SQLite-backed iMessage state** (JOSH-40): Eliminates the malformed inbox-state.json issue. After upgrading, run `openclaw doctor --fix` — do NOT manually edit inbox-state.json before then.
- **Skill Workshop** (JOSH-41): Heather can now propose and manage skills from the Control UI at `https://5.78.142.81.sslip.io`. No VPS/SSH required for skill installs.
- **Interrupted tool call recovery** (JOSH-42): Multi-step tasks (read email → draft → send → confirm) survive network hiccups without requiring session restart.
- **iOS hosted push relay** (JOSH-43): Auto-activates on first iOS app connection — no config change needed.
- **MCP tool result coercion**: Future MCP-backed skills won't trigger Anthropic 400 errors on non-text/image content blocks.
- **Operator plugin install policy**: Safer plugin installation replaces the old dangerous-code scanner path.

**Exact changes to apply:**  
VPS required — upgrade to OpenClaw 2026.6.2. Post-upgrade: run `openclaw doctor --fix` for iMessage SQLite migration.

**Risk level:** LOW (routine upgrade; no config changes needed)

---

### FINDING-JOSH-45 | /dreaming Memory System — Missing 76 Days
**Severity:** HIGH  
**Status:** NEW — Feature availability  

OpenClaw introduced a `/dreaming` memory system (first shipped in 2026.4.5) with three phases: light, deep, and REM. This performs background memory consolidation — reviewing recent daily notes and distilling long-term patterns, similar to how human sleep consolidates memories.

**Why it matters for Josh/Heather:**
- Heather currently has no MEMORY.md and no daily notes (JOSH-30 still unresolved)
- Dreaming would automatically consolidate daily notes into long-term memory — exactly what Heather needs as a personal assistant who should recognize Josh's preferences and communication patterns over time
- Josh is on 2026.3.22 — dreaming became available in 2026.4.5, meaning Heather has been missing 60+ days of potential memory consolidation
- After upgrade + MEMORY.md creation (JOSH-30): dreaming activates automatically via the memory-core plugin

**Exact changes to apply (in order):**
1. Upgrade VPS to 2026.6.2 (VPS required)
2. Create `workspace/MEMORY.md` (GitHub-only — see soul-improvements.md)
3. Dreaming activates automatically post-upgrade — no config change needed for Josh's setup

**Risk level:** LOW (additive feature, no configuration risk)

---

### FINDING-JOSH-46 | Subagent Parallelism — Multi-Step Task Acceleration
**Severity:** INFO  
**Status:** NEW — Capability available in 2026.6.2  

OpenClaw 2026.6.2 supports subagent parallelism — dispatching multiple tool calls simultaneously within a session. For Heather, multi-step tasks that previously ran sequentially can now run in parallel.

**Why it matters for Josh:**
- **Morning briefing**: pull calendar events, check iMessage, and do a web search simultaneously
- **Research tasks**: parallel web searches instead of sequential lookups
- **Email triage**: simultaneously search inbox, check calendar, and look up a contact
- Estimated speedup: 2–4x for multi-source research tasks

**Exact changes to apply:**  
Available automatically post-upgrade — no config changes needed.

**Risk level:** LOW

---

### FINDING-JOSH-47 | Google Workspace Still Not Connected
**Severity:** MEDIUM  
**Status:** CONFIRMED persistent gap  

Josh's bootstrap TOOLS.md confirms: **"No Google accounts are currently configured."** Heather has no access to Gmail, Google Calendar, or Google Contacts — the core integration surface for a personal assistant.

**Why it matters:**
- Heather was set up as a personal assistant for iMessage, email, calendar, and contacts
- iMessage is currently paused (waiting for VPS upgrade)
- Without Google Workspace, Heather can only assist with web searches and in-session tasks — no proactive email checking or calendar awareness
- This is the single largest functional gap in Heather's current capabilities

**Exact changes to apply:**
1. Connect Google account in AlphaClaw Control UI → General tab → Google Workspace
2. After connection: AlphaClaw will update bootstrap TOOLS.md automatically
3. Manually update `workspace/TOOLS.md` with Gmail/Calendar preferences and command patterns

**Risk level:** LOW (additive — no existing functionality at risk)

---

## Persistent Findings (Carried — None Resolved)

| Finding | Severity | Days Open | Note |
|---------|----------|-----------|------|
| JOSH-30: MEMORY.md missing | **CRITICAL** | **77** | GitHub-only — zero risk — highest priority |
| JOSH-31: HEARTBEAT.md empty | HIGH | 77 | GitHub-only — zero proactive monitoring |
| JOSH-29/39: Platform 76 days outdated | HIGH | 76 | Requires VPS — target now 2026.6.2 |
| JOSH-45: Dreaming missing 76 days | HIGH | NEW | Available post-upgrade |
| JOSH-47: No Google Workspace | MEDIUM | NEW | Largest functional gap |
| JOSH-37: SOUL.md not personalized | MEDIUM | 77 | GitHub-only — generic template, no Heather context |
| JOSH-32: TOOLS.md stale | MEDIUM | 77 | GitHub-only — no actual setup documented |
| JOSH-33: iMessage paused 42 days | MEDIUM | 42 | Wait for upgrade to 2026.6.2 (SQLite migration) |
| JOSH-34: Emoji contradiction | LOW | 77 | AGENTS.md says use reactions; USER.md says never |

---

## Summary Table

| Finding | Severity | Type | Status |
|---------|----------|------|--------|
| JOSH-44: 2026.6.2 now stable | HIGH | Platform milestone | NEW — upgrade target updated |
| JOSH-45: /dreaming system missing | HIGH | Feature gap | NEW — available post-upgrade |
| JOSH-46: Subagent parallelism | INFO | Capability | NEW — available post-upgrade |
| JOSH-47: No Google Workspace | MEDIUM | Integration gap | CONFIRMED persistent |

---

## Platform Research Notes (2026-06-07)

- **OpenClaw latest stable:** 2026.6.2 (confirmed today — all features shipped; new upgrade target for Josh)
- **2026.6.2 key additions (since June 3 beta):** Safer plugin installs via operator policy, MCP tool coercion at materialize boundary, subagent parallelism, Discord delivery stability, broader iOS support, calmer Discord composer controls
- **Next expected release:** 2026.5.31 line has a Tailscale Serve service-name binding update pending promotion — mid-to-late June 2026
- **AlphaClaw 0.9.16:** Confirmed current. Includes OPENCLAW_STATE_DIR managed export — means dreaming/memory-core state persists across container restarts automatically after upgrade
- **Personal assistant AI trends (June 2026):** Best AI assistants in 2026 track preferences, conversations, and workflows across weeks. Heather's MEMORY.md gap (JOSH-30, Day 77) means she's not operating as a best-in-class assistant despite the platform supporting it
- **iMessage channel stability (2026.6.2):** OpenClaw 2026.6.2 changelog notes iMessage delivery is now steadier. Resuming after upgrade benefits from both the SQLite state management and improved channel delivery
- **Top GitHub-only priority unchanged:** JOSH-30 (create MEMORY.md) remains the single highest-leverage zero-risk action — 5 minutes of work, takes effect on next session restart. See soul-improvements.md for exact file content.
