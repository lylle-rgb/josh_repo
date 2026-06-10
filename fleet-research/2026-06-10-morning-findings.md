# Fleet Research — Morning Scan Findings
**Instance:** Heather Schwartz (Josh — personal assistant)
**Scan date:** 2026-06-10 (morning)
**Scanner:** AlphaClaw Fleet Agent (automated morning scan)
**Previous scan:** 2026-06-09 morning (see `2026-06-09-morning-findings.md`)

---

## Summary

All prior open findings remain unresolved. This morning scan adds 4 new findings from web research.

Most urgent issues unchanged:
- **MEMORY.md missing — Day 80** (CRITICAL, GitHub-only)
- **HEARTBEAT.md empty — Day 80** (HIGH, GitHub-only)
- **Google Workspace not connected** (CRITICAL, VPS/setup)
- **OpenClaw 80 days behind stable (2026.6.2+)** (HIGH, VPS upgrade)

**Version note:** npm stable was **2026.6.2** as of June 9. Web research June 10 indicates **2026.6.5 may have shipped to npm stable** — the beta.5 track from June 8 appears to have graduated. Verify via `openclaw update --dry-run` on VPS before upgrading.

---

## NEW FINDINGS (Morning Scan — June 10)

### New Finding A — OpenClaw 2026.6.5 Possible npm Stable Graduation
**Severity:** HIGH
**What was found:** The 2026.6.5-beta.5 track (last tracked June 8) appears to have shipped. Multiple web sources reference "OpenClaw 2026.6.5" without beta qualifier. The npm stable target may have advanced from 2026.6.2 → 2026.6.5.

**Why it matters for Heather:** The 2026.6.5 release includes:
- **Bundled Parallel web search** — available automatically post-upgrade, no config needed. Heather can run multi-source searches without manual tool setup.
- **Extended thinking session recovery** — Anthropic extended-thinking sessions now recover after Gateway restart or prompt-cache expiry. Relevant when OpenRouter Anthropic fallback is fixed (JOSH-29 dead slug).
- **MCP tool result coercion** — non-text MCP responses handled cleanly. Prevents malformed image blocks from corrupting responses.
- **Channel safety hardening** — stronger Discord output safety, already noted in prior scan.

**Exact steps:** On VPS, run `openclaw update --dry-run` to check available version. If 2026.6.5 is available: `openclaw update`. Or use AlphaClaw UI → General tab → update button.

**Risk level:** MEDIUM — same upgrade risk as 2026.6.2; test after upgrading.

---

### New Finding B — Gemini 3.1 Flash Lite: Speed + Cost Improvement for Heather
**Severity:** MEDIUM
**What was found:** `google/gemini-3.1-flash-lite-preview` is now available. Benchmarks: 363 tokens/second (45% faster than Gemini 2.5 Flash), priced at 1/8th the cost of Gemini 3 Pro. Gemini 3 Flash (Heather's current primary) scored 78% on SWE-bench, beating Gemini 3 Pro (76.2%).

**Why it matters for Heather:** Daily assistant tasks (email triage, calendar reads, quick Q&A) don't need the full weight of Gemini 3 Flash. Gemini 3.1 Flash Lite would:
- Respond ~45% faster on routine tasks
- Reduce token cost on high-frequency heartbeat checks
- Keep Gemini 3 Flash Preview as fallback for complex tasks

**Exact config change in openclaw.json** (additive, low risk):
```json
"model": {
  "primary": "google/gemini-3-flash-preview",
  "fallbacks": [
    "google/gemini-3.1-flash-lite-preview",
    "openrouter/google/gemini-2.5-flash",
    "openrouter/anthropic/claude-haiku-4-5-20251001"
  ]
}
```
Note: Also fixes JOSH-29 dead haiku slug — correct OpenRouter slug is `openrouter/anthropic/claude-haiku-4-5-20251001`.

**Risk level:** LOW — additive fallback change; primary model unchanged.

---

### New Finding C — Discord Progressive Streaming Config
**Severity:** MEDIUM
**What was found:** Josh's Discord has `"streaming": "off"` — Heather buffers the entire response before sending. OpenClaw supports `"streaming": "progress"` with blockStreamingCoalesce to send chunked responses without message spam. Community consensus: minChars 300 + idleMs 500 prevents both message-spam and long silent waits.

**Why it matters for Heather:** Personal assistant queries ("what's on my calendar today?", "any urgent emails?") currently require waiting for full response generation. Progressive streaming means Heather starts typing in Discord while still working — much more natural UX. Josh's USER.md preference data suggests he expects responsive, not bureaucratic, interactions.

**Exact config change in openclaw.json:**
```json
"channels": {
  "discord": {
    "streaming": "progress",
    "blockStreamingCoalesce": {
      "minChars": 300,
      "idleMs": 500
    },
    "chunkMode": "newline",
    "maxLinesPerMessage": 40
  }
}
```

**Risk level:** LOW — revert by setting `"streaming": "off"` if issues arise.

---

### New Finding D — Dead Fallback Slug Fix (JOSH-29 Confirmation)
**Severity:** MEDIUM
**What was found:** Confirmed via web research: the current OpenRouter model slug for Claude Haiku is `openrouter/anthropic/claude-haiku-4-5-20251001`. The existing `openclaw.json` has `openrouter/anthropic/claude-3.5-haiku` which is an outdated slug and will fail on routing. This is the "dead haiku slug" noted in cross-customer analysis.

**Why it matters:** When Gemini primary fails (API error, quota, downtime), the fallback chain hits the dead haiku slug and silently fails. Heather goes dark during provider outages.

**Exact fix in openclaw.json fallbacks array:**
- Remove: `"openrouter/anthropic/claude-3.5-haiku"`
- Add: `"openrouter/anthropic/claude-haiku-4-5-20251001"`

**Risk level:** LOW — fixes silent failure path.

---

## Open Findings (Carried Over — Full Detail in 2026-06-09-morning-findings.md)

| # | Severity | Finding | Days Open |
|---|---|---|---|
| JOSH-30 | **CRITICAL** | MEMORY.md never created | **80** |
| JOSH-44 | **CRITICAL** | Google Workspace not connected | 7 |
| JOSH-31 | HIGH | HEARTBEAT.md empty — no proactive monitoring | 80 |
| JOSH-47 | HIGH | Dreaming blocked (needs upgrade + MEMORY.md) | 7 |
| JOSH-29/48 | HIGH | Platform 80 days behind stable (2026.6.2+) | **80** |
| JOSH-55 | MEDIUM | TOOLS.md template-only | 2 |
| JOSH-37 | MEDIUM | SOUL.md not personalized | 80 |
| JOSH-33/45 | MEDIUM | iMessage paused + malformed state | 44 |
| JOSH-54 | LOW | BOOTSTRAP.md not deleted | 2 |
| JOSH-34 | LOW | Emoji contradiction in AGENTS.md vs USER.md | 80 |

---

## Research Sources

- [OpenClaw Releases · GitHub](https://github.com/openclaw/openclaw/releases)
- [OpenClaw CHANGELOG](https://github.com/openclaw/openclaw/blob/main/CHANGELOG.md)
- [OpenClaw Release Notes (Releasebot)](https://releasebot.io/updates/openclaw)
- [Gemini 3.1 Flash Lite on OpenRouter](https://openrouter.ai/google/gemini-3.1-flash-lite-preview)
- [Best Gemini Models for OpenClaw 2026 (haimaker.ai)](https://haimaker.ai/blog/best-gemini-models-for-openclaw/)
- [OpenClaw Discord Streaming & Chunking Docs](https://docs.openclaw.ai/concepts/streaming)
- [Discord Config Improvements (Tuncer Deniz, X)](https://x.com/tuncerdeniz/status/2025029106950353298)
- [OpenClaw Active Memory Docs](https://docs.openclaw.ai/concepts/active-memory)
- [OpenClaw 2026.6.5 Release Notes (Releasebot)](https://releasebot.io/updates/openclaw)
- [Luke The Dev — OpenClaw v2026.4.12 memory upgrade (X)](https://x.com/iamlukethedev/status/2043675662188199962)
