# Fleet Research — Josh (Heather Schwartz) | 2026-05-29 Morning Scan

**Scan type:** Morning (platform status + overnight release check + targeted research)
**Date:** 2026-05-29
**Instance:** Josh Meyers — Heather Schwartz (personal assistant)
**Repo:** lylle-rgb/josh_repo
**Previous scan:** 2026-05-28 evening

---

## Platform Status

| Item | Current | Latest | Gap |
|------|---------|--------|-----|
| OpenClaw | 2026.3.22 | **2026.5.27 stable** | **69 days behind — CRITICAL** |
| AlphaClaw | Unknown | 0.9.16 | Day 15 without new AlphaClaw release |
| Primary model | google/gemini-3-flash-preview | — | Active |
| Gemini 3.5 Flash | Not in Josh's config | Available on OpenRouter (May 19) | Fallback chain gap |

---

## Overnight Release Check — No New OpenClaw Release

No OpenClaw release overnight. **2026.5.27 remains the current stable** (released May 28 at 11:41).

The latest pre-release on npm as of this morning is `2026.5.27-beta.1` — the same security hardening package that graduated to stable yesterday. No 2026.5.28 or 2026.5.29 beta has appeared. Upgrade target unchanged: **2026.5.27**.

---

## New Findings (2026-05-29 Morning)

### FINDING-JOSH-71 | Gemini 3.5 Flash Now Available on OpenRouter — Fallback Chain Update
**Severity:** MEDIUM
**Status:** NEW — model released May 19, 2026

Google released **Gemini 3.5 Flash** on OpenRouter on May 19, 2026. It is newer than Gemini 3.1 Flash Lite (released May 7) and represents a further improvement in the Gemini Flash lineage.

**Why this matters for Josh's fallback chain:**

Josh's current fallback chain has a dead entry (`openrouter/anthropic/claude-3.5-haiku`). The cross-customer analysis (Day 40) recommended replacing it with Gemini 3.1 Flash Lite. Gemini 3.5 Flash is now available and worth adding as a primary fallback over 3.1 Flash Lite.

**Updated recommended fallback chain:**
```json
"fallbacks": [
  "openrouter/google/gemini-3-5-flash",
  "openrouter/google/gemini-3-1-flash-lite-preview",
  "openrouter/google/gemini-2.5-flash"
]
```

This replaces the dead `claude-3.5-haiku` entry and prioritizes the newest Gemini Flash model. All three are active on OpenRouter as of this morning.

**Pricing note:** Gemini 3.5 Flash ($0.30/1M in, $2.50/1M out on OpenRouter) is slightly more than 3.1 Flash Lite ($0.25/$1.50) but still well below Gemini Pro. For Heather's fallback needs (personal assistant workloads), the cost delta is negligible.

**Risk level:** LOW — GitHub-only edit, no restart required.

---

### FINDING-JOSH-72 | memory-lancedb-pro Community Plugin — Future Memory Option
**Severity:** INFO
**Status:** NEW — community plugin, document for when memory setup begins

A community-built plugin called `memory-lancedb-pro` (GitHub: CortexReach/memory-lancedb-pro) provides enhanced LanceDB-backed long-term memory for OpenClaw with:
- **Hybrid Retrieval** — Vector + BM25 full-text search combined
- **Cross-Encoder Reranking** — post-retrieval reranking for precision
- **Multi-Scope Isolation** — separate memory scopes per conversation type

This is an alternative (or complement) to OpenClaw's built-in `memory-core` plugin.

**Why this matters for Josh/Heather:** Josh currently has no memory system at all (JOSH-30: MEMORY.md missing, Day 69). When memory is set up, this plugin offers better retrieval quality than basic memory-core. For Heather's use case (personal assistant with semantic context like "Josh's work situation," "Bliss launch timeline," "Oben HiFi partnerships"), hybrid vector + BM25 is meaningfully better than vector-only.

**Dependency:** Josh needs to be on OpenClaw ≥ 2026.4.10 to use memory plugins. Josh is on 2026.3.22 — memory plugins require the upgrade first.

**Action:** File for post-upgrade. When setting up memory, evaluate memory-lancedb-pro as a more capable alternative to the built-in memory-core.

**Risk level:** INFO — future reference only, no action until after upgrade.

---

### FINDING-JOSH-73 | Active Memory Plugin — NOT Available for Josh (Version Gated)
**Severity:** INFO
**Status:** NEW — version gate confirmed

OpenClaw's **Active Memory plugin** (introduced in 2026.4.10) inserts a dedicated memory sub-agent before the main reply, automatically pulling relevant preferences and history into each conversation. It is one of the most impactful recent features for personal assistants.

**Josh cannot use it yet.** Josh is on 2026.3.22. The plugin requires ≥ 2026.4.10.

Noah's instance (2026.4.15) is eligible and could use Active Memory now.

**Why this matters:** Once Josh upgrades to 2026.5.27, Active Memory will be available. For Heather's role as a personal assistant (vs. Noah's trading agent), Active Memory is particularly high-value — it ensures every DM starts with Heather already knowing Josh's current context, preferences, and open threads. No manual "remind Heather of X" required.

**Recommended post-upgrade Active Memory config for Josh:**
```json
"plugins": {
  "entries": {
    "active-memory": {
      "enabled": true,
      "config": {
        "agents": ["main"],
        "allowedChatTypes": ["direct"],
        "modelFallback": "google/gemini-3-flash-preview",
        "queryMode": "recent",
        "promptStyle": "balanced",
        "timeoutMs": 15000,
        "maxSummaryChars": 220,
        "persistTranscripts": false
      }
    }
  }
}
```

**Risk level:** INFO — document now, apply post-upgrade.

---

## Persistent Findings (Top Priority, Unchanged from Evening)

| Finding | Severity | Status | Day # |
|---------|----------|--------|-------|
| JOSH-30: MEMORY.md never created | CRITICAL | PERSISTENT | **69+** |
| JOSH-31: HEARTBEAT.md empty | HIGH | CONFIRMED EMPTY | **69+** |
| JOSH-33: iMessage paused | MEDIUM | Fix on upgrade | 39+ |
| JOSH-34: Emoji contradiction AGENTS vs USER | MEDIUM | ACTIVE | 15 |
| JOSH-37: SOUL.md stock template | MEDIUM | PERSISTENT | **69+** |
| JOSH-39→66: Upgrade target 2026.5.27 | HIGH | UPGRADE NOW | 69 |
| JOSH-50: Dead OpenRouter fallback | MEDIUM | PERSISTENT | 28 |
| JOSH-55: TOOLS.md blank | MEDIUM | PERSISTENT | **69+** |
| JOSH-56: AGENTS.md not customized | MEDIUM | PERSISTENT | 10 |
| JOSH-63: BOOTSTRAP.md not deleted | MEDIUM | PERSISTENT | 2 |

---

## Platform Research Notes (2026-05-29 Morning)

- **OpenClaw overnight:** No new release. 2026.5.27 is current stable, 2026.5.27-beta.1 is the latest pre-release.
- **Gemini 3.5 Flash:** Active on OpenRouter as of May 19, 2026. Newer than Gemini 3.1 Flash Lite.
- **Active Memory plugin:** Available ≥ 2026.4.10. Josh needs upgrade first. Noah is already eligible.
- **AlphaClaw:** 0.9.16, Day 15 without new release. No community tips overnight.
- **memory-lancedb-pro:** Community plugin with hybrid retrieval — strong option for post-upgrade memory setup.
- **FadeMem (academic):** Biologically-inspired memory decay paper, 45% storage reduction. Not yet in OpenClaw. Watch for integration.

---

## Morning Action Summary

**Today's new actionable item (one GitHub edit):**
- Update Josh's `openclaw.json` fallback chain: replace dead `claude-3.5-haiku` with `gemini-3-5-flash` + `gemini-3-1-flash-lite-preview` + `gemini-2.5-flash` (JOSH-71 — supersedes JOSH-50 fix)

**All prior zero-downtime backlog items remain unresolved** (see findings.md checklist).
