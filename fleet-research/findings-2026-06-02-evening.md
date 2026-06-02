# Fleet Research: Josh (Heather) — Evening Findings
**Date:** 2026-06-02 Evening
**Agent:** Heather Schwartz — Personal Assistant (Discord bot)
**Scan type:** Web research + codebase audit

---

## OpenClaw Platform Updates

### Latest Stable: 2026.5.18 | Beta: 2026.5.28-beta.3 | Alpha: 2026.5.31-alpha.1

**Finding 1: Active Memory Plugin — Now Production-Ready**
- What: A dedicated memory sub-agent now runs *before* the main reply for eligible conversational sessions. It surfaces relevant long-term memories into context before Heather answers.
- Why it matters: Heather wakes up fresh each session. Active Memory closes the gap by automatically injecting relevant context from prior interactions — meaning she'll "remember" Josh's recurring contacts, preferences, and ongoing threads without Josh having to re-explain them.
- Active memory can now be scoped to specific chats (e.g., only in direct chat with Josh, not group channels).
- Timed-out recall now returns a bounded partial result instead of dropping context entirely — reduces "I almost remembered it" failures.
- Risk: LOW — additive feature, no breaking changes.
- Action: Enable the Active Memory plugin if not already active. Scope it to Josh's direct chat only, not group channels.

**Finding 2: People-Aware Memory (v2026.4.29)**
- What: Memory now understands people better — associates memories with named individuals rather than just raw text.
- Why it matters: Heather interacts with Josh's contacts through iMessage and email. People-aware memory means she'll be better at remembering "Jake from Oben HiFi" vs. "Jake the friend" distinctions over time.
- Risk: LOW.
- Action: No config change needed if Active Memory is enabled — it's built into the memory subsystem.

**Finding 3: Discord Heartbeat Detection Fixed + Channel Flow Reliability**
- What: Fixed a bug where Discord sessions could freeze and fail to recover after disconnection. Channel parsing and bot heartbeat detection optimized. Follow-up messages less likely to get lost during active runs.
- Why it matters: Heather runs in Discord. Any freeze/reconnect failures mean missed messages from Josh. This fix improves reliability of her primary communication surface.
- Risk: LOW — bug fix, no config changes needed. Requires AlphaClaw update.
- Action: Ensure AlphaClaw is on latest version to get this fix.

**Finding 4: Lower-Latency Replies (2026.5.26)**
- What: Gateway startup and model-listing hot paths now reuse cached channel catalogs — /models response time dropped from ~20s to ~5ms after warmup.
- Why it matters: Heather's responses to Josh feel faster. Calendar lookups, iMessage checks, and email replies will all feel snappier.
- Risk: NONE — automatic improvement.

**Finding 5: Meeting Notes + Discord Voice Runs**
- What: Meeting Notes feature now available; Discord voice session routing improved via Gateway relay.
- Why it matters: Heather could transcribe and summarize Josh's meetings if he runs voice sessions through OpenClaw's Android Talk Mode or Discord voice.
- Risk: LOW — opt-in feature.
- Action: Explore enabling Meeting Notes for Josh's calendar context.

---

## AlphaClaw Updates

**Finding 6: AlphaClaw 0.8.0 — Chrome DevTools MCP**
- What: AlphaClaw can now control Josh's Mac from any VPS using Chrome's DevTools MCP.
- Why it matters: Heather gains autonomous browsing capability — she could look up information, research contacts, or verify calendar links without manual help.
- Risk: MEDIUM — broad capability, should be configured with appropriate scope limits.
- Action: Evaluate enabling Chrome DevTools MCP. Define explicit scope rules in TOOLS.md before enabling.

**Finding 7: AlphaClaw Docker Self-Update Fix**
- What: Fixed EBUSY crash on self-update in Docker environments.
- Why it matters: If Josh's AlphaClaw instance runs in Docker, it previously failed to update itself. This is now fixed.
- Risk: NONE — bug fix.

**Finding 8: AlphaClaw Version Display Unified**
- What: UI now shows OpenClaw + AlphaClaw as one unified deployment version pair.
- Why it matters: Easier for the fleet operator to know exactly what version Heather is running.

---

## Codebase Audit — Critical Gaps Found

### GAP 1: MEMORY.md Does Not Exist — CRITICAL
- **Severity: HIGH**
- AGENTS.md explicitly instructs Heather to read `MEMORY.md` in every main session: *"If in MAIN SESSION (direct chat with your human): Also read MEMORY.md"*
- The file does not exist in the repo.
- Impact: Every time Heather has a direct chat with Josh, she's instructed to load a file that doesn't exist. She has no long-term curated memory to fall back on. She's been operating without this file for her entire life.
- Action: Create `workspace/MEMORY.md` immediately with an initial entry bootstrapped from what's known about Josh from USER.md and the fleet-research history.

### GAP 2: TOOLS.md Is an Empty Template — HIGH
- **Severity: HIGH**
- TOOLS.md contains only boilerplate placeholder content (example cameras, SSH, TTS voice). No actual tool specifics for Heather's setup are documented.
- Impact: Heather has no documented cheat sheet for her actual tools. She doesn't know her preferred TTS voice, her iMessage configuration, her email client setup, etc.
- Action: Fill TOOLS.md with Heather's actual tool specifics.

### GAP 3: Emoji Reaction Conflict — MEDIUM
- **Severity: MEDIUM**
- USER.md contains a hard directive: **"STRICT: DO NOT SEND EMOJI REACTIONS TO MESSAGES."**
- AGENTS.md has an entire section called "React Like a Human!" that *encourages* emoji reactions in Discord.
- These two rules directly contradict each other. USER.md (Josh's specific preference) must win, but AGENTS.md is likely overriding it in practice.
- Action: Add a clear override note to the "React Like a Human!" section in AGENTS.md, citing the user preference. Reinforce in SOUL.md as a hard behavioral rule.

### GAP 4: No Heartbeat State Tracking File
- **Severity: LOW**
- AGENTS.md references `memory/heartbeat-state.json` for tracking what was last checked. No evidence this file exists or is being maintained.
- Action: Recommend Heather create this file during next heartbeat if it doesn't exist.

---

## Community Insights (X/Twitter — June 2, 2026)

- AlphaClaw Apex confirmed active for multi-instance fleet management (relevant to this fleet operation)
- Community tip: Batch periodic checks into HEARTBEAT.md instead of spawning multiple cron jobs — reduces API token burn significantly
- OpenClaw 2026.5.26: Lower-latency replies and improved Discord voice confirmed by community as high-impact
- Garry Tan endorsing AlphaClaw on Railway/Render for easy 8GB VPS deployment

---

## Risk Summary

| Finding | Severity | Action Required |
|---------|----------|-----------------|
| MEMORY.md missing | HIGH | Create immediately |
| TOOLS.md empty | HIGH | Fill with Heather specifics |
| Emoji reaction conflict | MEDIUM | Reconcile in AGENTS.md + SOUL.md |
| Active Memory plugin | LOW | Enable + scope to direct chat |
| Chrome DevTools MCP | MEDIUM | Evaluate with scope limits |
| Heartbeat state file | LOW | Create during next heartbeat |
| AlphaClaw updates | LOW | Update to latest AlphaClaw |
