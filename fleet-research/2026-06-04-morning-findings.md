# Fleet Research — Josh (Heather Schwartz) | 2026-06-04 Morning Scan

**Scan type:** Web research + platform delta + persistent gap review
**Date:** 2026-06-04
**Instance:** Josh Meyers — Heather Schwartz (personal assistant, iMessage / email / calendar / contacts)
**Repo:** lylle-rgb/josh_repo
**Prior scan:** 2026-06-04 evening — see fleet-research/2026-06-04-evening-findings.md

---

## Platform Status

| Item | Current | **NEW** Stable Target | Beta Track | Gap |
|------|---------|----------------------|------------|-----|
| OpenClaw | 2026.3.22 | **2026.6.1** (was 2026.5.27) | 2026.6.2-beta.1 | **74 days — CRITICAL** |
| AlphaClaw | Unknown | 0.9.16 | — | Check deployment |
| Primary model | google/gemini-3-flash-preview | — | — | Preview tag (see below) |

> **Upgrade target change:** The stable target was previously 2026.5.27. As of June 3, 2026, **2026.6.1 is the current stable release**. Upgrade directly to 2026.6.1.

---

## NEW Findings (June 4 Morning)

### FINDING-JOSH-47 | Upgrade Target Is Now 2026.6.1 Stable — Not 2026.5.27
**Severity:** INFO (target change, no new action — still need the upgrade)
**Type:** Platform delta

OpenClaw **2026.6.1 graduated to stable** on June 3, 2026. All prior recommendations to upgrade to 2026.5.27 are superseded — upgrade directly to 2026.6.1.

What 2026.6.1 adds on top of 2026.5.27 (relevant to Josh):
- **Skill Workshop:** Full UI for proposing, reviewing, and deploying skills from inside Control UI. No CLI required. Josh can browse ClawHub and install skills with one click.
- **SQLite state for iMessage:** iMessage monitor state (including the paused/active flag and guid tracking) moves from fragile JSON files to SQLite-backed state. The malformed `inbox-state.json` (see JOSH-45) will be cleanly migrated — restores iMessage monitoring without manual repair.
- **Runtime recovery:** Cleaner recovery from interrupted tool calls, stale session bindings, compaction handoffs, and media delivery retries. Heather's sessions become more stable.
- **Memory QMD improvements:** Serialized writes per store, hardened envelope metadata, rewrites transcript paths on rollover. Memory survives concurrent gateway/CLI activity.

**Risk:** NONE (2026.6.1 is stable, not beta)
**Action:** Upgrade via AlphaClaw watchdog UI to 2026.6.1.

---

### FINDING-JOSH-48 | Gemini Authentication Warning — OAuth Path Unsafe
**Severity:** HIGH
**Type:** Configuration risk — Google/Gemini provider

The Gemini CLI OAuth path is documented as unsafe as of early 2026, with 403 TOS violations affecting multiple paid-tier subscribers. Google ran a system-wide unban on March 2, 2026, but the condition can recur.

**Josh's current auth config:**
```json
"auth": {
  "profiles": {
    "google:default": {
      "provider": "google",
      "mode": "api_key"
    }
  }
}
```

Josh is using `api_key` mode — this is the **safe path**. No action required on auth mode. However, verify the key in use is a standalone `AIza...` key from [AI Studio](https://aistudio.google.com), NOT an OAuth refresh token. If the key starts with `ya29.`, it is an OAuth access token and should be replaced.

**Risk:** MEDIUM (if using OAuth token, 403 errors will cut off Heather's model access entirely)
**Action:** Verify the `GOOGLE_API_KEY` environment variable on VPS starts with `AIza`. If not, generate a new API key from AI Studio and update the VPS.

---

### FINDING-JOSH-49 | Gemini thinkingLevel — Configuration Footgun
**Severity:** LOW
**Type:** Model configuration note

OpenClaw maps Gemini 3.x models to `thinkingLevel` (not `thinkingBudget`). If Josh or the fleet manager ever adds model-specific config blocks for Gemini 3 models, use `thinkingLevel: "low"` / `"medium"` / `"high"` instead of a `thinkingBudget` integer.

Currently Josh's `openclaw.json` has no custom thinking config — only model references. No action required now, but note for future config changes.

**Risk:** LOW
**Action:** Document in TOOLS.md after workspace is populated.

---

### FINDING-JOSH-50 | Skill Workshop — Install Skills from Control UI (No CLI Required)
**Severity:** OPPORTUNITY
**Type:** New capability (2026.6.1)

The Skill Workshop is the headline feature of 2026.6.1. Skills can now be proposed, scanned, reviewed, and deployed entirely from the Control UI at `https://5.78.142.81.sslip.io`.

**Top community-recommended skills for Josh's use case:**
1. **Memory Core** — Prevents cold-start failure where Heather forgets everything between heartbeat cycles. Community consensus: install this first. Josh currently has no memory plugin active.
2. **Web Browsing** — Enables autonomous web research. Heather can search, browse, and synthesize web results without manual tool calls. Useful for research tasks Josh assigns.
3. **Gmail / Google Workspace** — Direct Gmail integration beyond gog-cli. (Note: depends on Google Workspace being connected first — see JOSH-44.)

**Risk:** LOW (Skill Cards + SkillSpector scanning now runs at install time — built-in safety)
**Action (post-upgrade):** Open Control UI → Skill Workshop → Install Memory Core, then Web Browsing.

---

### FINDING-JOSH-51 | Discord Voice Session Follow — Meeting Notes Opportunity
**Severity:** OPPORTUNITY
**Type:** New capability (available since 2026.5.28)

Discord voice sessions can now follow configured users into voice channels, with allowed-channel checks and multi-user handoff.

**Why this matters for Josh:** Josh is a founder/CEO running Bliss and Oben HiFi. He likely has Discord calls with collaborators. Heather could follow Josh into a voice channel to:
- Take live notes
- Summarize the discussion afterward
- Capture action items

This is opt-in and requires no breaking config changes.

**Risk:** LOW
**Action (post-upgrade):** Evaluate with Josh whether voice channel follow is wanted. If yes, enable in `channels.discord` config.

---

### FINDING-JOSH-52 | OpenClaw 2026.6.2-beta.1 — Operator Plugin Install Policy
**Severity:** INFO (beta track)
**Type:** Platform tracking

2026.6.2-beta.1 (released June 3, 2026) replaces the old "dangerous-code scanner" plugin install path with an **operator install policy**. Skills now install via a declared policy rather than runtime code scanning — more predictable, safer, and clearer error messages.

**Relevance:** When Josh upgrades to 2026.6.1 and then later to 2026.6.2, any skill installs will use the new policy flow. No action needed now — awareness only.

---

## Persistent Critical Gaps (Unchanged Since Prior Scans)

These remain unresolved. All are GitHub-only fixes — no VPS access required.

| ID | Issue | Severity | Fix Location |
|----|-------|----------|--------------|
| JOSH-30 | MEMORY.md never created — Day 74+ | CRITICAL | GitHub file create |
| JOSH-31 | HEARTBEAT.md empty — no email/calendar monitoring | HIGH | GitHub file replace |
| JOSH-34 | AGENTS.md emoji contradiction vs USER.md strict prohibition | MEDIUM | GitHub file edit |
| JOSH-37 | SOUL.md is stock template — never personalized | MEDIUM | GitHub file replace |
| JOSH-44 | Google Workspace not connected — email/calendar/contacts inaccessible | CRITICAL | VPS setup |
| JOSH-45 | inbox-state.json has duplicate JSON key (malformed) | LOW | Auto-fix via 2026.6.1 SQLite migration |
| JOSH-55 | TOOLS.md is stock template — no environment docs | MEDIUM | GitHub file replace |
| JOSH-63 | BOOTSTRAP.md never deleted — onboarding incomplete | MEDIUM | GitHub file delete |
| JOSH-50 | Dead OpenRouter fallback (claude-3.5-haiku) — 30s timeout risk | MEDIUM | GitHub file edit |

---

## Immediate Action Checklist

**GitHub only (no VPS access required):**
- [ ] Create `workspace/MEMORY.md` (template in soul-improvements.md)
- [ ] Replace `workspace/HEARTBEAT.md` with active monitoring tasks
- [ ] Replace `workspace/SOUL.md` with Josh-specific version
- [ ] Add Josh override block to top of `workspace/AGENTS.md` (emoji prohibition, LA timezone)
- [ ] Replace `workspace/TOOLS.md` with environment-specific notes
- [ ] Delete `workspace/BOOTSTRAP.md`
- [ ] Remove dead OpenRouter fallback from `openclaw.json`

**VPS access required:**
- [ ] Verify `GOOGLE_API_KEY` is AIza-prefixed (not ya29 OAuth token)
- [ ] Connect Google Workspace via AlphaClaw UI → General tab
- [ ] Upgrade OpenClaw to **2026.6.1** (target updated from 2026.5.27)
- [ ] Run `openclaw doctor --fix` after upgrade (SQLite state migration, iMessage resume)
- [ ] Install Memory Core skill from Skill Workshop post-upgrade

---

*Scan completed: 2026-06-04 morning by AlphaClaw Fleet Research daemon.*
