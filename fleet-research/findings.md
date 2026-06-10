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
- **Bundled Parallel web search** — available automatically post-upgrade, no config needed
- **Extended thinking session recovery** — Anthropic sessions survive Gateway restart (relevant for OpenRouter fallback fix)
- **MCP tool result coercion** — prevents malformed image blocks corrupting responses
- **Channel safety hardening** — stronger Discord output safety

**Exact steps:** On VPS, run `openclaw update --dry-run` to check available version. If 2026.6.5 available: `openclaw update`. Or AlphaClaw UI → General tab.

**Risk level:** MEDIUM

---

### New Finding B — Gemini 3.1 Flash Lite: Speed + Cost Improvement for Heather
**Severity:** MEDIUM
**What was found:** `google/gemini-3.1-flash-lite-preview` — 363 tokens/sec (45% faster), 1/8th cost of Gemini 3 Pro. Strong candidate for heartbeat/routine tasks.

**Exact config change (additive):**
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

**Risk level:** LOW

---

### New Finding C — Discord Progressive Streaming Config
**Severity:** MEDIUM
**What was found:** `"streaming": "off"` delays all responses. Progressive streaming with coalescing gives more natural UX.

**Exact config change:**
```json
"channels": {
  "discord": {
    "streaming": "progress",
    "blockStreamingCoalesce": { "minChars": 300, "idleMs": 500 },
    "chunkMode": "newline",
    "maxLinesPerMessage": 40
  }
}
```

**Risk level:** LOW

---

### New Finding D — Dead Fallback Slug Fix (JOSH-29 Confirmation)
**Severity:** MEDIUM
**What was found:** Correct OpenRouter Haiku slug confirmed: `openrouter/anthropic/claude-haiku-4-5-20251001`. Current config has outdated `openrouter/anthropic/claude-3.5-haiku`.

**Fix:** Replace dead slug in fallbacks array.

**Risk level:** LOW

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
- [OpenClaw Release Notes (Releasebot)](https://releasebot.io/updates/openclaw)
- [Gemini 3.1 Flash Lite on OpenRouter](https://openrouter.ai/google/gemini-3.1-flash-lite-preview)
- [Best Gemini Models for OpenClaw 2026 (haimaker.ai)](https://haimaker.ai/blog/best-gemini-models-for-openclaw/)
- [OpenClaw Discord Streaming & Chunking Docs](https://docs.openclaw.ai/concepts/streaming)
- [Discord Config Improvements (X — Tuncer Deniz)](https://x.com/tuncerdeniz/status/2025029106950353298)
- [OpenClaw 2026.6.5 Release Notes (Releasebot)](https://releasebot.io/updates/openclaw)
