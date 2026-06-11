# Fleet Research — Evening Scan Findings
**Instance:** Heather Schwartz (Josh — personal assistant)
**Scan date:** 2026-06-11 (evening)
**Scanner:** AlphaClaw Fleet Agent (automated evening scan)
**Previous scan:** 2026-06-10 morning (see `2026-06-10-morning-findings.md`)

---

## Summary

Day-count escalations from morning scan: MEMORY.md now **Day 81**, HEARTBEAT empty **Day 81**, platform behind **Day 81**, Google Workspace disconnected **Day 8**.

June 10 morning flagged 2026.6.5 as "possible" stable graduation. Tonight's research **confirms** 2026.6.5 is on npm stable. Morning's A-D findings (streaming, Gemini 3.1 Flash Lite, haiku slug fix) remain unresolved.

Tonight adds 4 new findings: group-chat context injection behavior change, Nylas CLI as email/calendar fallback path, Memory Wiki import for MEMORY.md bootstrap, and iMessage Mac-host constraint clarification.

---

## NEW FINDINGS (Evening Scan — June 11)

### New Finding E — 2026.6.5 Confirmed Stable (Escalation from JOSH-48 "Possible")
**Severity:** HIGH (escalation)
**What was found:** Multiple independent sources now reference "OpenClaw 2026.6.5" without beta qualifier. The June 8 beta.5 track has graduated to npm stable. Heather is now **81 days behind stable** on version 2026.3.22.

**Why it matters for Heather (complete feature list missed):**
- **Bundled Parallel web search** — available immediately post-upgrade with no config. Enables multi-source searches in a single tool call. Directly speeds up calendar/email context-gathering during heartbeat checks.
- **MCP tool result coercion** — non-text MCP blocks (resource_link, audio, malformed image) are coerced at the materialize boundary. Prevents Anthropic 400 errors and poisoned session history when Google Workspace tools return richer content objects.
- **Extended-thinking session recovery** — relevant when OpenRouter Anthropic fallback (Haiku slug fix, Finding D) is applied; sessions survive Gateway restart and prompt-cache expiry.
- **QQBot reasoning tag stripping** — internal model `<thinking>` tags are stripped before output reaches Discord. Heather's Discord responses will be cleaner.
- **YYYY.M.PATCH versioning** — floor pin at 2026.6.5; prior tags compatible. Upgrade path is `openclaw update` on VPS.

**Exact steps:** VPS → `openclaw update --dry-run` to verify 2026.6.5 availability → `openclaw update`. Alternatively: AlphaClaw UI → General tab → update button.

**Risk level:** MEDIUM — standard upgrade; test after applying.

---

### New Finding F — Group-Chat Context Now Injected Every Turn (v2026.2.15+)
**Severity:** MEDIUM
**What was found:** As of OpenClaw v2026.2.15, group chat context is injected on **every turn**, not just the first message. Prior behavior meant Heather's context window was only primed on session start; subsequent messages in a Discord thread lacked re-injection. This is already present in 2026.6.5.

**Why it matters for Heather:** Josh's Discord guild (1484448262290276464) is a live group context. Under the old behavior, Heather's memory of the thread degraded after the first turn. With every-turn context injection:
- SOUL.md, USER.md, and AGENTS.md instructions are re-applied on each reply
- The NO emoji reactions rule from USER.md re-enforces itself every turn (fewer drift incidents)
- Memory file context stays fresh across long Discord conversations

**Action:** No config change needed — this is platform behavior fixed at 2026.2.15. Heather has been missing this since install (2026.3.22 base). After upgrade to 2026.6.5, behavior will be in effect.

**Note for AGENTS.md:** Worth documenting this explicitly in AGENTS.md group-chat section so Heather knows to rely on it (see soul-improvements).

**Risk level:** LOW — informational; fixed by upgrade.

---

### New Finding G — Nylas CLI as Email/Calendar Fallback Path (Google Workspace Unblocked Alternative)
**Severity:** HIGH (new path)
**What was found:** Nylas CLI provides a purpose-built email/calendar layer with 72+ commands across Gmail, Outlook, Exchange, Yahoo, iCloud, and IMAP — all 6 major providers via a single auth flow. As an OpenClaw skill, it operates identically to gog-cli for email/calendar tasks and does not require Google Cloud Console OAuth credentials.

**Why it matters for Heather:** Google Workspace connection is blocked at Day 8 (JOSH-44). The blocker is OAuth credential setup in Google Cloud Console — a VPS-side manual step. If that remains blocked, Nylas CLI is a viable alternative path:
- Gmail access via Nylas token (simpler OAuth flow — no GCP project required)
- Google Calendar, Tasks, Contacts via same token
- No impact on existing gog-cli skill — both can coexist

**Recommended approach:**
1. First attempt: Connect Google Workspace via AlphaClaw UI → General → Google Workspace (gog-cli path)
2. If blocked >3 more days: Install Nylas CLI skill as fallback: `openclaw skills install nylas-cli`
3. Document active path in TOOLS.md once connected

**Nylas docs:** https://cli.nylas.com/guides/nylas-openclaw-personal-assistant

**Risk level:** LOW — additive option; does not replace gog-cli, just provides an alternative.

---

### New Finding H — Memory Wiki Import: Fast MEMORY.md Bootstrap (No Manual Write Required)
**Severity:** MEDIUM
**What was found:** OpenClaw introduced a Memory Wiki import feature (confirmed in 2026.2.15+ notes) that allows importing ChatGPT or other AI conversation records directly into the memory system. New UI tabs: "Imported Insights" and "Memory Palace" allow migrating interaction history from other AI platforms into OpenClaw's file-based memory system.

**Why it matters for Heather:** MEMORY.md is Day 81 missing. The recommended fix (fleet-research/2026-06-09-evening-soul-improvements.md, Recommendation 2) is to manually create a stub. However, if Josh has prior ChatGPT or AI assistant conversation history related to Bliss Lifestyle, Oben HiFi, or personal context, importing it could:
- Bootstrap MEMORY.md with actual context instead of a blank stub
- Seed Heather's long-term memory with real preference and context data
- Accelerate the "Heather learns who Josh is" timeline significantly

**Action:** Josh can access via AlphaClaw UI → OpenClaw dashboard → Memory → Import. Alternatively, Heather can trigger via CLI once MEMORY.md stub exists.

**Note:** This doesn't replace the stub — Heather should create the MEMORY.md stub first (JOSH-30 fix), then import enriches it.

**Risk level:** LOW — additive; no risk to existing state.

---

### New Finding I — iMessage Mac-Host Constraint (VPS Likely Incompatible)
**Severity:** HIGH (clarification of known issue JOSH-33/45)
**What was found:** iMessage integration uses Apple's AppleScript bridge — a macOS-only automation framework. It works via the native Messages app on macOS. There is **no equivalent on Linux or Windows**. Apple does not provide a third-party iMessage API.

**Why it matters for Heather:** Josh's instance is hosted on a VPS (IP: 5.78.142.81.sslip.io). This is almost certainly a Linux server. If so:
- `inbox-state.json` shows `"imessage_monitoring_paused": true` — likely paused because iMessage literally cannot work on Linux
- The pause is not a temporary issue but a fundamental architecture constraint
- iMessage will never work on the current VPS host without a macOS bridge machine

**Recommended action (two paths):**
1. **Keep VPS + add Mac bridge:** Josh would need a Mac running OpenClaw's iMessage channel connector, forwarding to the VPS gateway. This is possible but adds complexity.
2. **Prioritize other channels:** Focus Heather's communication work on Discord (already working) and email/calendar (once Google Workspace connected). iMessage is a nice-to-have, not a blocker for the core personal assistant use case.

**Document in TOOLS.md:** Note that iMessage is Mac-only and the current VPS cannot support it natively.

**Risk level:** MEDIUM — clarifies a long-open issue (Day 44); no new action required immediately, but expectation management matters.

---

## Open Findings (Updated Day Counts)

| # | Severity | Finding | Days Open |
|---|---|---|---|
| JOSH-30 | **CRITICAL** | MEMORY.md never created | **81** |
| JOSH-44 | **CRITICAL** | Google Workspace not connected | 8 |
| JOSH-31 | HIGH | HEARTBEAT.md empty — no proactive monitoring | 81 |
| JOSH-47 | HIGH | Dreaming blocked (needs upgrade + MEMORY.md) | 8 |
| JOSH-48 | HIGH | Platform 81 days behind stable (2026.6.5 confirmed) | **81** |
| JOSH-55 | MEDIUM | TOOLS.md template-only | 3 |
| JOSH-37 | MEDIUM | SOUL.md not personalized for personal assistant domain | 81 |
| JOSH-33/45 | MEDIUM | iMessage paused (now clarified: VPS/Mac architecture issue) | 44 |
| JOSH-34 | LOW | Emoji contradiction in AGENTS.md vs USER.md | 81 |
| JOSH-54 | LOW | BOOTSTRAP.md not deleted | 3 |
| June10-A | HIGH | 2026.6.5 confirmed stable — upgrade pending | 1 |
| June10-B | MEDIUM | Gemini 3.1 Flash Lite fallback not added | 1 |
| June10-C | MEDIUM | Discord streaming still `"off"` | 1 |
| June10-D | MEDIUM | Dead haiku slug (`claude-3.5-haiku`) still in fallbacks | 1 |

---

## Research Sources

- [OpenClaw 2026.6.5 Release Notes (Releasebot)](https://releasebot.io/updates/openclaw)
- [OpenClaw Changelog](https://openclawai.io/changelog/)
- [OpenClaw v2026.2.15: Discord Components V2, Nested Sub-Agents & More](https://openclaws.io/blog/openclaw-v2026-2-15-release/)
- [OpenClaw Discord memory and persistent brain setup (LumaDock)](https://lumadock.com/tutorials/openclaw-discord-memory-brain)
- [How OpenClaw Remembers Everything (Manthan)](https://manthanguptaa.in/posts/clawdbot_memory/)
- [Connect OpenClaw to Nylas for Email and Calendar](https://cli.nylas.com/guides/nylas-openclaw-personal-assistant)
- [How to Use OpenClaw to Automate Email and Calendar Management 2026](https://www.thedigitalmagazine.net/tools-automation/how-to-use-openclaw-to-automate-email-and-calendar-management-in-2026/)
- [OpenClaw iMessage Integration: How It Works (2026 Guide)](https://openclawconsult.com/lab/openclaw-imessage)
- [The Ultimate Guide to OpenClaw iMessage Integration 2026](https://skywork.ai/skypage/en/openclaw-imessage-integration/2036706359462723584)
- [OpenClaw Releases Most Powerful Memory Update (36kr)](https://eu.36kr.com/en/p/3715160552878469)
- [10 OpenClaw Updates: Dreaming, Active Memory April 2026 (Rahul Goyal)](https://rahulgoyal.co/justdraft/openclaw-update-dreaming-memory-video-april-2026/)
- [7 Proactive OpenClaw Agent Workflows 2026 (xCloud)](https://xcloud.host/proactive-openclaw-agent-workflows/)
- [The 3 Superpowers of OpenClaw: Hooks, Cron, Heartbeat (Kryll)](https://blog.kryll.io/openclaw-hooks-cron-heartbeat-ai-agent-automation/)
