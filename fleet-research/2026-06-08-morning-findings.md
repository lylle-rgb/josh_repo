# Fleet Research — Morning Scan (Archive)
**Instance:** Heather Schwartz (Josh — personal assistant)
**Scan date:** 2026-06-08 (morning)
**Scanner:** AlphaClaw Fleet Agent

_This is the morning scan archive. See `findings.md` for the current state with all open findings._

---

## New Findings This Morning

### A — iMessage BlueBubbles Private API Path Available (April 2026)
**Severity:** MEDIUM

OpenClaw shipped full BlueBubbles Private API integration in April 2026. Heather's iMessage monitoring has been paused (`imessage_monitoring_paused: true`). The old AppleScript bridge is fragile — BlueBubbles is now the recommended replacement.

**Steps:** Upgrade to 2026.5.28 → `openclaw doctor --fix` → install BlueBubbles on Josh's Mac → reconfigure iMessage via BlueBubbles integration.

---

### B — Gemini Native Search Grounding Underused
**Severity:** MEDIUM

Gemini 2.5 Flash and Gemini 3 Flash Preview (Heather's primary model) support built-in Google Search grounding: live search with citations, no additional API key. Heather has no web search configured. Investigate `googleSearchGrounding: true` as model option post-upgrade.

---

### C — Mandatory Memory Retrieval Rule Missing from AGENTS.md
**Severity:** MEDIUM

OpenClaw 2026 best practices: add "search memory before acting" to AGENTS.md. Add to Session Startup section:
```
**Search memory before acting.** Check MEMORY.md and today's memory file before answering questions about Josh's preferences or past decisions. No mental notes.
```

---

### D — NVIDIA SkillSpector Context for Future Skill Installs (June 2026)
**Severity:** INFO

Every ClawHub skill now ships with a SkillSpector Skill Card (Clean / Suspicious / Malicious) — 64 vulnerability checks including hidden instructions, prompt injection, and memory poisoning. Verify "Clean" verdict before installing any future skill (gog, sag, etc.).

---

### E — npm Stable is 2026.5.28 (Prior Scan Correction)
**Severity:** INFO

`openclaw update` installs **2026.5.28** from npm. Prior scans referenced 2026.6.2 as "current stable" — that tag is on GitHub but not yet in the npm channel. No change to action plan: `openclaw update` is still correct, target is 2026.5.28.

---

## Research Sources

- [OpenClaw Releases · GitHub](https://github.com/openclaw/openclaw/releases)
- [OpenClaw iMessage BlueBubbles 2026](https://openclawconsult.com/lab/openclaw-imessage)
- [OpenClaw Memory Masterclass](https://velvetshark.com/openclaw-memory-masterclass)
- [OpenClaw Web Search / Gemini Grounding](https://docs.openclaw.ai/tools/web)
- [NVIDIA SkillSpector Partnership](https://openclaw.ai/blog/openclaw-nvidia-skill-security)
- [OpenClaw 2026.6.1 Release Notes](https://senx.ai/openclaw-news/2026-06-02-openclaw-news)
- [OpenClaw Release Notes (Releasebot)](https://releasebot.io/updates/openclaw)
