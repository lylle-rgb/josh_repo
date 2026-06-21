# Morning Scan — June 21, 2026

**Researcher:** AlphaClaw Fleet Agent  
**Time:** Morning, June 21, 2026  
**Previous scan:** June 21 Evening — 2026.6.9-stable confirmed shipped, upgrade window opened  
**Instance:** josh_repo (Heather Schwartz — personal assistant)

---

## Headline: Upgrade Window Confirmed Open. 3 New Findings. Heartbeat Null Day 6.

### Upgrade Status
2026.6.9-stable remains the npm `latest` stable tag. No new release appeared overnight. Upgrade window is OPEN — this is the highest-urgency action for Josh. Staged path unchanged: `2026.3.22 → 2026.5.27 → 2026.6.2 → 2026.6.5 → 2026.6.6 → 2026.6.9`. Verify first: `npm show openclaw@latest version` = `2026.6.9`.

---

## New Findings This Scan

### F30 — BRAVE_API_KEY Not Set: Web Search Disabled (MEDIUM-HIGH)

No Brave Search API key is configured. Heather cannot autonomously search the web for tasks like research, fact-checking, or news lookups. Independent benchmarks (AIMultiple, Feb 2026) rate Brave Search highest for agent use with the highest "Agent Score" (14.89). ~700,000 OpenClaw users use Brave Search as their primary web search tool.

**Impact for Heather:** Any request requiring current web information silently falls back to stale model knowledge or fails. This is a core capability gap for a personal assistant.

**Fix (two options):**  
Option A — openclaw.json env block:
```json
"env": { "BRAVE_API_KEY": "BSAxxxxxxxx" }
```
Option B — AlphaClaw UI → Envars tab (no VPS SSH needed for this one).

API key: https://api.search.brave.com/app/keys (free tier: 2,000 queries/month)

**Risk:** MEDIUM-HIGH. No data loss, but Heather cannot search the web until this is set.

---

### F31 — Same-Provider Fallback Chain: Single Google Failure Point (MEDIUM)

Josh's current model chain:
- **Primary:** `google/gemini-3-flash-preview`
- **Fallback 1:** `openrouter/google/gemini-3.5-flash`
- **Fallback 2:** `openrouter/anthropic/claude-haiku-4-5` (pending upgrade)

Fallback 1 is the same Google provider as Primary. If Google is rate-limited, has an outage, or deprecates both endpoints simultaneously, Fallback 1 provides no protection — only Fallback 2 saves the session. Community best practice: the first fallback should always be a **different provider**.

**Recommended fix (post-2026.6.9 upgrade, bundle with fallback 2 update):**
```json
"model": {
  "default": "google/gemini-3-flash-preview",
  "fallbacks": [
    "openrouter/anthropic/claude-haiku-4-5",
    "openrouter/google/gemini-3.5-flash"
  ]
}
```
This keeps two fallbacks but makes Fallback 1 cross-provider. If Google is down, Haiku 4.5 catches it.

**Risk:** MEDIUM. Josh currently has partial protection (Fallback 2 does cross-provider), but a Google-specific outage burns through Fallback 1 silently.

---

### F32 — iMessage SQLite Migration Will Auto-Fix inbox-state.json on Upgrade (POSITIVE)

POSITIVE finding. MEMORY.md already documents: "inbox-state.json has a malformed duplicate key — do NOT manually edit it (SQLite migration will handle it on upgrade)." This morning's research confirms the exact mechanism: OpenClaw 2026.6.1 introduced a storage schema migration that moves iMessage monitor state and inbound queues from JSON files to SQLite. The migration runs automatically on first startup after upgrading through 2026.6.1.

**Implication:** The malformed inbox-state.json that has been sitting untouched will be cleanly fixed as part of the staged upgrade (which passes through 2026.6.2, which requires ≥ 2026.6.1 migration to have run). After the upgrade, iMessage monitoring may partially resume automatically — or at minimum the corrupted state blocking it will be cleared.

Also: 2026.6.6 added a specific fix "verify SQLite auth migration before cleanup" which prevents migration cleanup from running before the migration succeeds — meaning the staged path through 2026.6.6 is even safer for this.

**No action required** — just confirming the upgrade path handles this automatically. The iMessage pause (Day 56) may partially or fully resolve after upgrade.

**Risk:** LOW (handled by upgrade, but it's good news).

---

## Standing Alerts (Unchanged from Evening Scan)

| Alert | Days | Priority |
|-------|------|----------|
| Google Workspace OAuth not connected | Day 91 | 🔴 CRITICAL |
| Heartbeat cron not deployed — all-null state | Day 6 | 🔴 HIGH |
| iMessage paused (inbox-state.json malformed) | Day 56 | 🔴 HIGH — auto-fix on upgrade |
| OpenClaw 2026.3.22 — upgrade window OPEN | Day 91 | 🔴 HIGH |
| Discord open to all (`allowFrom: ["*"]`) | Day 91 | 🟠 MEDIUM-HIGH |
| BRAVE_API_KEY not set (NEW — F30) | — | 🟠 MEDIUM-HIGH |
| Same-provider fallback chain gap (NEW — F31) | — | 🟡 MEDIUM |
| Noah session scope broken (`noah--repo` 404) | Day 10 | Fleet ops |

---

## No New OpenClaw Releases Overnight

Confirmed: `npm show openclaw@latest version` = `2026.6.9`. No 2026.6.10 or newer published. Upgrade target unchanged. 2026.6.9-beta.1 (June 19) is superseded by stable. 2026.6.8 remains a permanent skip.
