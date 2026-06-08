# Fleet Research — Morning Scan Findings
**Instance:** Heather Schwartz (Josh — personal assistant)
**Scan date:** 2026-06-08 (morning)
**Scanner:** AlphaClaw Fleet Agent (automated morning scan)
**Previous scan:** 2026-06-08 evening (see `2026-06-08-evening-findings.md`)

---

## Summary

All 10 findings from the June 8 evening scan remain open. This morning scan adds 5 new findings from web research. Most urgent issues unchanged: MEMORY.md missing (Day 78), HEARTBEAT.md empty, OpenClaw **78 days behind npm stable (2026.5.28)**.

**Version note (corrected):** `openclaw update` via npm installs **2026.5.28** — the npm stable channel. The 2026.6.2 tag exists on GitHub but is not yet the npm stable. Both versions are significant improvements over Heather's current 2026.3.22.

---

## NEW FINDINGS (Morning Scan — June 8)

### New Finding A — iMessage BlueBubbles Private API Path Available (April 2026)
**Severity:** MEDIUM
**What was found:** OpenClaw shipped full BlueBubbles Private API integration in April 2026 (2026.4.x), providing a more reliable iMessage bridge than the AppleScript method Heather was using before iMessage was paused. Multi-account routing was also added in March 2026.

**Why it matters:** Heather's iMessage monitoring has been paused since ~April 30 (`imessage_monitoring_paused: true`). The original AppleScript bridge is fragile and a likely cause of the pause. BlueBubbles uses a dedicated Mac app + private API, which is significantly more stable and now the recommended iMessage path for all OpenClaw instances.

**Exact steps to implement:**
1. Upgrade Heather to 2026.5.28: `openclaw update && openclaw restart`
2. Run `openclaw doctor --fix` (handles SQLite migration)
3. Install BlueBubbles app on Josh's Mac
4. Re-configure iMessage via the BlueBubbles integration rather than the AppleScript bridge

**Risk level:** MEDIUM — requires Josh's Mac. Confirm with Josh before resuming iMessage.

---

### New Finding B — Gemini Native Search Grounding Underused
**Severity:** MEDIUM
**What was found:** Gemini 2.5 Flash and Gemini 3 Flash Preview (Heather's primary model) support built-in Google Search grounding natively, returning AI-synthesized answers backed by live search results and citations — no additional search skill or API key needed.

**Why it matters:** Heather has no web search configured (no Brave, Serper, or Firecrawl in `openclaw.json`). With native Gemini grounding, search could be available with zero additional cost or configuration complexity. The OpenClaw Google provider plugin may already expose this via model config.

**Exact investigation steps:**
1. After upgrading to 2026.5.28, run `/help search` inside a session to confirm if Google Search grounding is surfaced
2. If supported, check whether `googleSearchGrounding: true` is a valid model option in `openclaw.json` under `agents.defaults.models["google/gemini-3-flash-preview"]`

**Risk level:** LOW — informational investigation; no config change until confirmed supported

---

### New Finding C — Mandatory Memory Retrieval Rule Missing from AGENTS.md
**Severity:** MEDIUM
**What was found:** OpenClaw 2026 best practices recommend an explicit "search memory before acting" rule in AGENTS.md. Without it, agents skip documented context and guess at information that's already written down.

**Why it matters:** Especially important for Heather since she starts from zero memory every session. Once MEMORY.md is created (Finding 5 from evening scan), she needs an explicit rule to check it before responding to questions about Josh's preferences, past decisions, or ongoing projects.

**Exact change to apply:** Add to the Session Startup section of `workspace/AGENTS.md`:
```
## Memory Rule
**Search memory before acting.** Before answering questions about Josh's preferences, past conversations, or decisions — check MEMORY.md and today's memory/YYYY-MM-DD.md first. Never guess at information that might be written down. No mental notes.
```

**Risk level:** LOW — additive documentation change

---

### New Finding D — NVIDIA SkillSpector Context for Future Skill Installs (June 2026)
**Severity:** INFO
**What was found:** OpenClaw partnered with NVIDIA in June 2026 to add SkillSpector scanning to all ClawHub skill publications. Every ClawHub skill now ships with a Skill Card (verdict: Clean / Suspicious / Malicious) based on 64 vulnerability patterns including hidden instructions, prompt injection, trigger abuse, memory poisoning, and purpose-access mismatch.

**Why it matters:** Heather currently has no community skills installed. When skills are added in the future (Google Workspace CLI `gog`, voice TTS `sag`, etc.), verify each has a "Clean" Skill Card on ClawHub before installing. This is now the standard vetting protocol for all OpenClaw skill installs.

**Risk level:** LOW — informational; no immediate action required

---

### New Finding E — npm Stable is 2026.5.28 (Prior Scan Correction)
**Severity:** INFO
**What was found:** As of June 8, 2026: `openclaw update` installs **2026.5.28** from npm. Previous scans referenced 2026.6.2 as "current stable" — that tag exists on GitHub but is not yet promoted to the npm stable channel.

**Why it matters:** No change to the action plan — `openclaw update` is still the correct command. But the target version is 2026.5.28, not 2026.6.2. Notable 2026.5.28 security improvements: group prompt text kept out of system prompt, blocked unsafe command wrappers, rejected no-auth Tailscale exposure.

**Risk level:** NONE — informational correction

---

## Open Findings (Carried Over from 2026-06-08 Evening Scan)

All 10 prior findings remain open. Full detail in `2026-06-08-evening-findings.md`.

| # | Severity | Finding | Days Open |
|---|---|---|---|
| 1 | HIGH | OpenClaw 78 days behind npm stable (2026.5.28) | 78 |
| 2 | HIGH | Agent inactive ~5.5 weeks | — |
| 3 | MODERATE | BOOTSTRAP.md still exists post-onboarding | — |
| 4 | HIGH | HEARTBEAT.md empty — no proactive monitoring | — |
| 5 | **HIGH** | **MEMORY.md missing** | **78** |
| 6 | MODERATE | iMessage monitoring paused (see Finding A above for fix path) | — |
| 7 | MODERATE | SOUL.md generic template | — |
| 8 | MODERATE | TOOLS.md generic template | — |
| 9 | LOW | Duplicate JSON key in inbox-state.json | — |
| 10 | LOW | Bootstrap TOOLS.md says no Google configured | — |

---

## Research Sources

- [OpenClaw Releases · GitHub](https://github.com/openclaw/openclaw/releases)
- [OpenClaw CHANGELOG](https://github.com/openclaw/openclaw/blob/main/CHANGELOG.md)
- [OpenClaw Release Notes (Releasebot)](https://releasebot.io/updates/openclaw)
- [OpenClaw iMessage BlueBubbles 2026](https://openclawconsult.com/lab/openclaw-imessage)
- [OpenClaw Memory Masterclass (VelvetShark)](https://velvetshark.com/openclaw-memory-masterclass)
- [OpenClaw Web Search / Gemini Grounding](https://docs.openclaw.ai/tools/web)
- [NVIDIA SkillSpector Partnership](https://openclaw.ai/blog/openclaw-nvidia-skill-security)
- [OpenClaw 2026.6.1 Release Notes (SEN-X)](https://senx.ai/openclaw-news/2026-06-02-openclaw-news)
