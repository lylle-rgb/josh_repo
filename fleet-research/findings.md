# Fleet Findings — Josh (Heather Schwartz) | Current State

**Last updated:** 2026-06-04 (Morning Scan)
**Instance:** Josh Meyers — Heather Schwartz (personal assistant)
**Repo:** lylle-rgb/josh_repo

> For full dated reports, see the dated files in this directory.
> Latest detailed scan: `2026-06-04-morning-findings.md`

---

## Platform Status (as of 2026-06-04)

| Item | Current | Latest Stable | Gap |
|------|---------|---------------|-----|
| OpenClaw | 2026.3.22 | **2026.6.1** | **74 days behind — CRITICAL** |
| AlphaClaw | Unknown | 0.9.16 | — |
| Primary model | google/gemini-3-flash-preview | — | Active (verify AIza key) |
| iMessage | Paused | Fix in 2026.6.1 + SQLite migration | 74+ days paused |
| Google Workspace | **NOT CONNECTED** | — | CRITICAL — core tools inaccessible |

---

## 🔴 Critical / High Priority (Active)

### JOSH-30 | MEMORY.md Never Created — Day 74+
**Severity:** CRITICAL | **Fix:** GitHub file create

Heather has run for 74 days with no long-term memory file. Each session, she wakes up with only USER.md and the current day's memory log. No curated wisdom about Josh has accumulated. This is the single highest-ROI fix — zero cost, immediate value.

**Fix:** Create `workspace/MEMORY.md` using template in `soul-improvements.md`.

---

### JOSH-31 | HEARTBEAT.md Empty — Day 74+
**Severity:** HIGH | **Fix:** GitHub file replace

Josh's HEARTBEAT.md contains only 3 comment lines. Zero tasks. No email monitoring. No calendar monitoring. Heather has never proactively checked Josh's inbox.

**Fix:** Replace `workspace/HEARTBEAT.md` with the template in `soul-improvements.md`.

---

### JOSH-33 | iMessage Paused — Fix in OpenClaw 2026.6.1 (SQLite Migration)
**Severity:** MEDIUM | **Fix:** Upgrade + `openclaw doctor --fix`

iMessage has been paused due to malformed JSON / source deduplication issues. The `inbox-state.json` file has a duplicate JSON key and `imessage_monitoring_paused: true` stuck. OpenClaw 2026.6.1 migrates iMessage state to SQLite — the malformed JSON is cleaned automatically.

**Fix:** Upgrade to 2026.6.1, run `openclaw doctor --fix` — iMessage resumes via clean SQLite state.

---

### JOSH-34 | Emoji Contradiction — AGENTS.md vs USER.md
**Severity:** MEDIUM | **Fix:** GitHub file edit

USER.md states: `STRICT: DO NOT SEND EMOJI REACTIONS TO MESSAGES.`
AGENTS.md (stock template) contains "React Like a Human!" section encouraging emoji reactions.

Direct contradiction. Every session, Heather receives conflicting instructions.

**Fix:** Add Josh override block at top of `workspace/AGENTS.md`. See `soul-improvements.md`.

---

### JOSH-37 | SOUL.md Never Personalized — Day 74+
**Severity:** MEDIUM | **Fix:** GitHub file replace

SOUL.md (SHA: 792306ac) is the stock template — identical to Noah's instance. No Josh-specific rules, no LA timezone, no Bliss/Oben context, no emoji prohibition.

**Fix:** Replace `workspace/SOUL.md` with Josh-specific version in `soul-improvements.md`.

---

### JOSH-39→66 | Upgrade Target: OpenClaw 2026.6.1 (Updated)
**Severity:** HIGH | **Fix:** VPS upgrade

Josh is 74 days behind. Current stable: **2026.6.1** (released June 3, 2026). Skip 2026.5.27 — upgrade directly to 2026.6.1.

What's in 2026.6.1 that 2026.3.22 lacks:
- **iMessage SQLite migration** — auto-cleans malformed inbox-state.json, resumes monitoring
- **Skill Workshop** — install skills from Control UI without CLI
- **SQLite-backed state** — inbound queues, plugin ledgers, session metadata more resilient
- **Memory QMD improvements** — serialized writes, hardened metadata, transcript path rewrites
- **Runtime recovery** — interrupted tool calls, stale bindings, compaction handoffs recover cleanly
- Security: group prompt isolation, Tailscale auth, command wrapper safety
- Discord: self-reply echo suppression, tightened wake matching, voice session follow
- Reaction approval flows (async approvals for calendar, email drafts, contact saves)

**Fix:** Upgrade via AlphaClaw to OpenClaw 2026.6.1. Run `openclaw doctor --fix` after upgrade.

---

### JOSH-44 | Google Workspace Not Connected — Personal Assistant Has No Core Integrations
**Severity:** CRITICAL | **Fix:** VPS setup via AlphaClaw UI

The bootstrap TOOLS.md (injected every session) explicitly shows: "No Google accounts are currently configured."

Heather's entire personal assistant value proposition depends on Gmail, Google Calendar, and Google Contacts — none of which are accessible. 70-80% of Heather's intended daily tasks are impossible.

**Fix:** Connect Google Workspace via AlphaClaw UI → General tab → Google Workspace section.
Authorize: Gmail (read+write), Calendar (read+write), Contacts (read+write) at minimum.

---

### JOSH-50 | Dead OpenRouter Fallback
**Severity:** MEDIUM | **Fix:** GitHub file edit

`openclaw.json` has `openrouter/anthropic/claude-3.5-haiku` as a fallback. OpenRouter is not configured, creating 30-second timeout risk on model failure.

**Fix:** Remove `openrouter/anthropic/claude-3.5-haiku` from `agents.defaults.model.fallbacks`.

---

### JOSH-55 | TOOLS.md Never Populated — Day 74+
**Severity:** MEDIUM | **Fix:** GitHub file replace

TOOLS.md (SHA: 917e2fa8) is the stock template — identical to Noah's. No environment data: no Google auth details, no Discord guild info, no iMessage status.

**Fix:** Replace `workspace/TOOLS.md` with environment-specific content. See `soul-improvements.md`.

---

### JOSH-63 | BOOTSTRAP.md Never Deleted — Day 74+
**Severity:** MEDIUM | **Fix:** GitHub file delete

`workspace/BOOTSTRAP.md` still exists. Per bootstrap instructions, it should be deleted when onboarding is complete. Its continued presence indicates the bootstrap was never fully finished.

**Fix:** Delete `workspace/BOOTSTRAP.md` via GitHub.

---

## New Findings (2026-06-04 Morning)

### JOSH-48 | Gemini OAuth Warning — Verify AIza Key
**Severity:** HIGH | **Fix:** VPS env var check

The Gemini OAuth path has documented 403 TOS violations. Josh uses `api_key` mode (correct) but verify `GOOGLE_API_KEY` starts with `AIza`, NOT `ya29` (OAuth access token). If it starts with `ya29`, replace with a key from Google AI Studio.

See: `2026-06-04-morning-findings.md` for full details.

---

### JOSH-50 | Skill Workshop Now Available (2026.6.1)
**Severity:** OPPORTUNITY

After upgrade to 2026.6.1, install these from Control UI → Skill Workshop:
1. **Memory Core** — top community recommendation, prevents cold-start memory drops
2. **Web Browsing** — autonomous research capability
3. **Gmail skill** — enhanced email integration (after Google Workspace connected)

---

## Info / Opportunity

- **JOSH-65:** Reaction approval flows — async approvals for calendar, email drafts, contact saves. Requires iMessage re-enabled post-upgrade.
- **JOSH-67:** Security group prompt isolation in 2026.6.1 — Discord group content properly sandboxed from system prompt.
- **JOSH-68:** Self-reply echo suppression + wake matching improvements.
- **JOSH-51:** Discord voice session follow (2026.5.28+) — Heather can follow Josh into voice channels for meeting notes.

---

## Immediate Action Checklist

**GitHub only (no VPS access required):**
- [ ] Create `workspace/MEMORY.md` (template in soul-improvements.md)
- [ ] Replace `workspace/HEARTBEAT.md` (template in soul-improvements.md)
- [ ] Replace `workspace/SOUL.md` (template in soul-improvements.md)
- [ ] Add Josh override to top of `workspace/AGENTS.md` (template in soul-improvements.md)
- [ ] Replace `workspace/TOOLS.md` (template in soul-improvements.md)
- [ ] Delete `workspace/BOOTSTRAP.md`
- [ ] Remove dead OpenRouter fallback from `openclaw.json`

**VPS access required:**
- [ ] Verify `GOOGLE_API_KEY` starts with `AIza` (not `ya29`)
- [ ] Connect Google Workspace via AlphaClaw UI → General tab
- [ ] Upgrade OpenClaw to **2026.6.1** (was 2026.5.27 — target updated)
- [ ] Run `openclaw doctor --fix` after upgrade (SQLite migration, iMessage resume)
- [ ] Post-upgrade: Install Memory Core + Web Browsing from Skill Workshop

---

## Finding History

Detailed dated scans available in this directory. Scan naming conventions:
- Old: `findings-YYYY-MM-DD-morning/evening.md`
- New: `YYYY-MM-DD-morning/evening-findings.md`

Scans run since: 2026-05-12. Most recent: 2026-06-04 morning.
