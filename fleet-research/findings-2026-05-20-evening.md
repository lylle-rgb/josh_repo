# Fleet Research — Josh / Heather Schwartz — Evening Scan

**Scan Date:** 2026-05-20 (Evening — Day 33)
**Agent:** AlphaClaw Apex Fleet Research Agent
**Instance:** Josh / Heather Schwartz — Discord bot personal assistant (iMessage, email, calendar, contacts)
**OpenClaw Version:** 2026.3.22 (meta.lastTouchedVersion) — 22+ stable releases behind 2026.5.19
**Previous Findings:** findings-2026-05-20-morning.md (Day 33 Morning, Findings 1–86)
**Cumulative Open Findings:** 93 (7 new this evening, 0 resolved)

---

## Platform News — New Since Morning Scan (May 20)

| Item | Detail |
|---|---|
| **2026.5.19 released** | OpenClaw 2026.5.19 shipped overnight. Josh's gap increases to 22+ stable releases. Key changes: agent fixes for clean bounded refactors, explicit plugin SDK/API deprecation paths, @openclaw/proxyline 0.3.3, Pi packages 0.75.1. Node.js 22.19 minimum now explicitly documented in changelog. |
| **Multi-model routing confirmed stable** | Discord bots can configure distinct models per request type: cheap model for basic chat, flagship model for complex tasks. Josh's config uses `gemini-3-flash-preview` for everything — no differentiation between a simple heartbeat ping and complex email composition. |
| **Token-efficient memory algorithm** | New single-pass hierarchical extraction + multi-signal retrieval memory algorithm in OpenClaw's memory-core ecosystem. Retrieval on temporal queries confirmed 29.6 points higher vs keyword search. Josh's Gemini embeddings would apply this automatically once memory-core is activated. |
| **Plugin SDK deprecation warnings in 2026.5.19** | 2026.5.19 adds explicit deprecation notification paths for plugin SDK usage patterns. Josh's hardcoded plugin load path (`/app/node_modules/@chrysb/alphaclaw/lib/plugin/usage-tracker`) may be flagged by the new deprecation subsystem post-upgrade. Not a breakage — a warning that the absolute path format may change in 2026.6.x. |
| **Mac app redesign** | Cleaner permissions/voice/skills/cron/exec/debug panes in the Mac control app. Relevant if Josh uses the Mac desktop app to manage Heather's config — UI navigation significantly improved in this version. |

---

## New Findings — Evening Scan (87–93)

---

### Finding 87 — OpenClaw 2026.5.19 Released Overnight: Josh Now 22+ Releases Behind (MEDIUM)

**Risk:** MEDIUM
**Days Pending:** 0 (new tonight)

**Description:**
OpenClaw 2026.5.19 shipped since the morning scan's reference baseline of 2026.5.18 as current stable. Josh remains on 2026.3.22. The gap has grown from 21+ to 22+ stable releases.

Key 2026.5.19 changes relevant to Josh:
- **Proxyline 0.3.3:** Stability improvements for the proxy layer Heather uses to communicate with external APIs (Gmail, Calendar)
- **Pi packages 0.75.1:** Updated internal packages — reduces memory footprint and improves startup reliability
- **Plugin SDK deprecation paths:** New structured warnings for plugin configurations heading toward deprecation (see Finding 88)
- **Node.js 22.19 minimum:** Now explicitly stated in the changelog — the pre-upgrade Node.js check (Finding 75) is even more important before upgrading

**Cumulative gap consequence:** 22 releases of accumulated fixes means the path from 2026.3.22 to 2026.5.19 involves multiple dependency resolutions, plugin compatibility checks, and config migrations. Upgrading via AlphaClaw Control UI handles most of this automatically, but the gap makes the pre-upgrade backup more important.

**Action:** Upgrade target is now 2026.5.19 (not 2026.5.18). Pre-upgrade checklist from soul-improvements-2026-05-19-evening.md remains valid with this version substituted.

**Risk Assessment:** MEDIUM. Cumulative gap risk. The upgrade is already recommended — this is a version bump on the target.

---

### Finding 88 — Plugin SDK Deprecation Paths: usage-tracker Hardcoded Load Path Risk (LOW)

**Risk:** LOW
**Days Pending:** 0 (new tonight — confirmed in 2026.5.19 release notes)

**Description:**
OpenClaw 2026.5.19 introduces explicit deprecation notification paths for plugin SDK usage. Josh's `openclaw.json` loads usage-tracker with a hardcoded filesystem path:

```json
"plugins": {
  "load": {
    "paths": [
      "/app/node_modules/@chrysb/alphaclaw/lib/plugin/usage-tracker"
    ]
  }
}
```

The new deprecation system in 2026.5.19 flags absolute filesystem paths in `plugins.load.paths` in favor of package-name resolution (without the `/lib/` prefix). This is a deprecation warning, not a breakage — the absolute path still works in 2026.5.19. However, it signals that 2026.6.x may remove absolute path support.

**Action (after upgrade to 2026.5.19):** Monitor for deprecation warning in OpenClaw logs after upgrade. If warning appears, update `plugins.load.paths` to use the package-name format per the 2026.5.19 deprecation guide.

**Risk Assessment:** LOW. Warning, not breakage. Worth tracking post-upgrade so the path doesn't break silently in 2026.6.x.

---

### Finding 89 — No Multi-Model Routing: Single Model for All Task Complexity Levels (LOW/Opportunity)

**Risk:** LOW (opportunity)
**Days Pending:** 0 (new tonight)

**Description:**
OpenClaw 2026.5.x supports multi-model routing — a cheaper model for lightweight requests and a flagship model for complex tasks within the same bot instance. Josh's current configuration uses `google/gemini-3-flash-preview` for everything.

**The inefficiency:** A HEARTBEAT_OK ping (checking if there's anything to do) costs the same model as composing a careful email on Josh's behalf. Gemini 3 Flash Preview is used for both, when Gemini 2.5 Flash would handle heartbeats at lower token cost.

**Post-upgrade routing opportunity (2026.5.x):** Route HEARTBEAT.md checks and simple queries to `google/gemini-2.5-flash` (lower cost) while routing email composition, complex calendar management, and iMessage responses to `google/gemini-3-flash-preview` (higher quality).

**Also note:** The fallback `openrouter/anthropic/claude-3.5-haiku` is a retired model (Finding 59 — still unresolved). This needs updating to `openrouter/anthropic/claude-haiku-4-5` regardless of routing changes.

**Risk Assessment:** LOW/opportunity. Current setup works. Routing would reduce token costs for heartbeat-heavy workloads — relevant once HEARTBEAT.md is actually populated (Finding 90).

---

### Finding 90 — HEARTBEAT.md Empty for 33 Days: Primary Proactivity Mechanism Completely Inactive (HIGH)

**Risk:** HIGH
**Days Pending:** 33

**Description:**
Josh's `workspace/HEARTBEAT.md` contains only the factory default comment — unchanged since deployment. **Every heartbeat Heather receives results in HEARTBEAT_OK with no action performed.** AGENTS.md describes a rich heartbeat system: email checks, calendar lookups, social monitoring, proactive outreach. None of it is active.

**What Heather should be doing but isn't:**
- Checking Josh's Gmail for urgent messages 2–3x daily (once Google account is connected)
- Alerting Josh to calendar events coming up within 2 hours
- Monitoring @blisslifestyleofficial and @obenhifi for social mentions (once Grok OAuth configured)
- Proactively reaching out if it's been >8 hours since last contact

**The consequence:** For 33 days, Josh has had a personal assistant capable of proactive monitoring but zero proactive monitoring has occurred. Heather is purely reactive — she only helps when Josh asks.

**Minimal HEARTBEAT.md to add right now (no Google connection required to initialize):**

See soul-improvements-2026-05-20-evening.md for the paste-ready replacement.

**Risk Assessment:** HIGH. The heartbeat is the proactivity mechanism. Without it populated, Heather is entirely reactive — a major capability gap for a personal assistant whose entire value proposition is proactive help.

---

### Finding 91 — TOOLS.md Empty for 33 Days: Operational Infrastructure Undocumented (MEDIUM)

**Risk:** MEDIUM
**Days Pending:** 33

**Description:**
Josh's `workspace/TOOLS.md` still contains only the example template with no actual configuration documented.

**What should be in TOOLS.md but isn't:**

| Category | Should Contain |
|---|---|
| Server / SSH | Hetzner VPS IP: 5.78.142.81, SSH alias, port |
| Discord | Guild ID: 1484448262290276464 |
| Google (when connected) | Workspace email, Calendar IDs |
| iMessage / BlueBubbles | Status: paused |
| TTS | Not configured — Gemini TTS available post-upgrade |
| OpenClaw | Version: 2026.3.22, upgrade target: 2026.5.19 |
| Plugins | usage-tracker (AlphaClaw), discord — both active |

**Why this matters:** If Heather needs to SSH to the VPS, reference the Discord guild, or check BlueBubbles config during a support session — she has to ask Josh or guess. TOOLS.md should be her operational reference. For 33 days, it's been empty.

Paste-ready replacement is in soul-improvements-2026-05-20-evening.md.

**Risk Assessment:** MEDIUM. Easy fix in 3 minutes. Heather should know her own infrastructure.

---

### Finding 92 — AGENTS.md vs USER.md Emoji Rule Contradiction: STRICT Rule Not Reinforced in SOUL.md (MEDIUM)

**Risk:** MEDIUM
**Days Pending:** 33

**Description:**
A direct contradiction exists in Josh's workspace:

**USER.md** (Josh's explicit instruction, marked STRICT):
> "STRICT: DO NOT SEND EMOJI REACTIONS TO MESSAGES."

**AGENTS.md** (default template guidance):
> "React Like a Human! On platforms that support reactions (Discord, Slack), use emoji reactions naturally: React when you appreciate something but don't need to reply (👍, ❤️, 🙌)..."

These are opposing instructions. SOUL.md has no mention of this rule at all.

**The risk:** A session that weights AGENTS.md guidance over the USER.md note — particularly one that skims USER.md rather than reading carefully — could violate Josh's STRICT rule. The rule needs to live in SOUL.md (core identity), not only in USER.md (user data).

**Fix:** Add to SOUL.md under Boundaries:
```markdown
## Josh's Absolute Rules

- **NO EMOJI REACTIONS — EVER.** Josh has explicitly prohibited sending emoji reactions on Discord. STRICT. Non-negotiable. Use words or stay silent. Never use 👍, ❤️, 🙌, ✅, or any other reaction.
- This overrides the "React Like a Human" guidance in AGENTS.md for Josh specifically.
```

**Risk Assessment:** MEDIUM. Rule exists in USER.md. But SOUL.md is identity — absolute constraints belong there.

---

### Finding 93 — Day 33 Evening Escalation: Critical Findings Still Unresolved After Full Day (CRITICAL)

**Risk:** CRITICAL
**Days Pending:** 33 (core findings) / 0 (this summary)

**Description:**
Day 33 is complete. Evening scan confirms: no critical findings have been resolved since the morning scan, and none from the prior 32 days.

The three core CRITICAL findings remain open:
1. **Google account not connected** (Finding 48/56 — Day 33) — Heather has zero email, calendar, or contacts capability
2. **BOOTSTRAP.md not deleted** (Finding 62/69/77 — Day 33) — Agent re-introduces itself at every session start
3. **No memory logs** (Finding 50/78 — Day 33) — 33 sessions of context permanently lost

Tonight's new HIGH findings:
- **HEARTBEAT.md empty for 33 days** (Finding 90) — proactivity completely absent
- **TOOLS.md empty for 33 days** (Finding 91) — no operational config documented

Tonight's new MEDIUM:
- **AGENTS.md vs USER.md emoji rule conflict** (Finding 92) — STRICT rule at risk

**Day 33 evening minimum implementation queue (~25 minutes total):**
1. Delete BOOTSTRAP.md — 30 sec
2. Add no-emoji rule to SOUL.md — 2 min
3. Replace HEARTBEAT.md with active template — 2 min
4. Replace TOOLS.md with actual config — 3 min
5. Add contextPruning + fix fallback model in openclaw.json — 3 min
6. Create workspace/memory/2026-05-20.md — 5 min
7. Connect Google account — 10 min

**Risk Assessment:** CRITICAL. Same 3 CRITICAL findings from Day 1 remain open after 33 days.

---

## Day 33 Evening: Full Persistent Findings Status Table

| # | Title | Risk | Days Open |
|---|---|---|---|
| 48/56 | Google account never connected | CRITICAL | 33 |
| 50/78 | No MEMORY.md / no memory logs — 33 sessions | CRITICAL | 33 |
| 52 | No active heartbeat task | MEDIUM | Unknown |
| 53/59 | Retired fallback model (claude-3.5-haiku) | MEDIUM | 7 |
| 54/61/72/87 | 22+ releases behind stable (2026.5.19) | MEDIUM | 58+ |
| 55/60 | SOUL.md no-emoji rule absent | MEDIUM | 7 |
| 62/69/77 | BOOTSTRAP.md not deleted — CRITICAL Day 33 | CRITICAL | 33 |
| 64 | TOOLS.md unpopulated | LOW | 4 |
| 66 | AlphaClaw 0.9.16 unverified | MEDIUM | 3 |
| 67 | defineToolPlugin — Google Workspace native tools | LOW | 3 |
| 68 | Grok OAuth now stable — social monitoring | LOW | 3 |
| 73 | Active Memory allowedChatIds — MEMORY.md security | MEDIUM | 2 |
| 74 | ElevenLabs v3 / Gemini TTS now stable | LOW | 2 |
| 75 | Node.js 22.19 minimum — pre-upgrade check | MEDIUM | 2 |
| 76 | AlphaClaw OPENCLAW_STATE_DIR durable state | LOW | 2 |
| 79 | Cron --wait + Active Memory now stable | LOW | 2 |
| 80 | contextPruning absent — add 35m cache-ttl | MEDIUM | 1 |
| 81 | Gemini-native TTS path (no ElevenLabs needed) | LOW | 1 |
| 82 | File transfer plugin — iMessage attachments | LOW | 1 |
| 83 | Docker security hardening in 2026.5.18/19 | LOW | 1 |
| 84 | Gemini semantic memory auto-select | LOW | 1 |
| 85 | gog-cli missing from Josh vs Noah | LOW | 1 |
| 86 | Day 33 morning critical escalation summary | CRITICAL | 1 |
| 87 | OpenClaw 2026.5.19 released — Josh now 22+ behind | MEDIUM | 0 |
| 88 | Plugin SDK deprecation path — usage-tracker risk | LOW | 0 |
| 89 | No multi-model routing (heartbeat vs complex task) | LOW | 0 |
| 90 | HEARTBEAT.md empty 33 days — proactivity absent | HIGH | 0 |
| 91 | TOOLS.md empty 33 days — no operational config | MEDIUM | 0 |
| 92 | AGENTS.md/USER.md emoji rule contradiction | MEDIUM | 0 |
| 93 | Day 33 evening escalation — critical unchanged | CRITICAL | 0 |

**Open: 93 | Resolved: 0 | Critical: 4 | High: 7+ | Medium: 15+ | Low: 12+**

---

*Generated by AlphaClaw Apex Fleet Research Agent — Evening Scan — 2026-05-20 (Day 33)*
