# Fleet Research — Josh / Heather Schwartz — Morning Scan

**Scan Date:** 2026-05-16 (Morning — Day 29)
**Agent:** AlphaClaw Apex Fleet Research Agent
**Instance:** Josh / Heather Schwartz — Discord bot personal assistant (iMessage, email, calendar, contacts)
**Previous Findings:** findings-2026-05-15-evening.md (Day 28 Evening, Findings 1–55)
**Cumulative Open Findings:** 58 (3 new this morning, 0 resolved)
**Note:** An Evening scan (findings-2026-05-16-evening.md) was completed later the same day, extending the daily count to 60 open findings.

---

## Platform News — New Since Yesterday's Evening Scan

| Item | Detail |
|---|---|
| OpenClaw v2026.5.16-beta.1 (May 16) | Two new releases today. Josh is now **16+ releases behind beta** and **14+ behind stable (2026.5.7)**. Key new features: xAI Grok OAuth, CLI cron wait timeouts + exact run filtering, skill snapshot caching to reduce startup rebuild overhead, localized onboarding (EN/ZH-S/ZH-T). |
| OpenClaw v2026.5.16-beta.2 (May 16) | MCP plugin tool cancellation via AbortSignal forwarding, enhanced provider error handling across embeddings/images/audio, **malformed data rejection throughout persistence and provider paths** (directly relevant to inbox-state.json corruption), Telegram polling improvements. |
| Skill snapshot caching (beta.1) | Caches skill build artifacts between restarts. Relevant post-upgrade: currently each bot restart rebuilds all skill caches from scratch. Will speed up recovery after the pending maintenance window. |
| CLI cron wait timeouts + exact run filtering (beta.1) | `waitTimeout` aborts a cron job that runs past its allotted window. `exactRun` skips duplicate runs if a prior interval is still running. Required to prevent heartbeat stacking once Heather's cron is activated. |
| MCP AbortSignal cancellation (beta.2) | Plugin tool calls (memory-core, email, calendar, iMessage plugins) can now be cancelled cleanly. Prevents external service hangs from blocking the agent indefinitely. |
| Provider error hardening (beta.2) | Malformed data rejected at the persistence layer rather than silently corrupting state. If inbox-state.json corruption recurs post-upgrade, the system will reject it explicitly instead of operating on a broken file. |
| Supermemory plugin confirmed | `@supermemory/openclaw-supermemory` is available and production-ready. Install: `openclaw plugins install @supermemory/openclaw-supermemory`. Provides auto-recall (queries memory before each AI turn) + auto-capture (stores conversation after each turn). Requires Supermemory Pro subscription (paid, cloud). |
| Compaction community baseline | Community consensus: `reserveTokensFloor: 40000` is the safe minimum. The default 20K floor is "often too tight" — a single large email thread can jump past it before the memory flush triggers. Josh has **no compaction block at all** in openclaw.json. |

---

## New Findings — Morning Scan (56–58)

---

### Finding 56 — v2026.5.16 Beta Releases Today: Upgrade Gap Grows to 16+

**Risk:** MEDIUM
**Days Pending:** 0 (new today)

**Description:**
Two OpenClaw beta releases shipped today (v2026.5.16-beta.1 and beta.2). Josh's instance on v2026.3.22 is now 16+ release points behind the beta channel and 14+ behind stable (v2026.5.7). The gap has widened by two more points since yesterday's evening scan.

New capabilities in today's betas that are relevant to Josh's pending configuration work:

- **Skill snapshot caching (beta.1):** Startup overhead after restarts drops significantly. During the pending OpenClaw upgrade (Finding 54), multiple restarts may be required — this reduces the pain of each one.
- **CLI cron wait timeouts (beta.1):** Once Heather's heartbeat/cron is configured (Finding 52), `waitTimeout` and `exactRun` flags prevent runaway or overlapping job execution. Plan these flags into the cron design from Day 1.
- **MCP AbortSignal cancellation (beta.2):** Any plugin tool call (email send, calendar write, iMessage send) can now be safely cancelled if the external service is slow. Relevant to all of Heather's write operations against Josh's Google account.
- **Malformed data rejection (beta.2):** The persistence layer now explicitly rejects malformed data rather than silently corrupting state. Post-upgrade, a repeat of the inbox-state.json duplicate key issue would surface as a clear error rather than silent data corruption.

**Action:** No immediate action — these are beta features. Monitor for v2026.5.16 stable. The implication is that the upgrade from 2026.3.22 → 2026.5.7 (already 14+ behind) is urgent before the gap becomes practically impossible to reason about.

**Risk Assessment:** Informational / growing urgency on Finding 54.

---

### Finding 57 — Supermemory Plugin: Automated Long-Term Memory Without MEMORY.md Discipline

**Risk:** LOW (opportunity)
**Days Pending:** 0 (new today)

**Description:**
The `@supermemory/openclaw-supermemory` plugin provides cloud-backed automated memory for OpenClaw agents. This directly addresses Finding 50 (no MEMORY.md, no persistent memory after 29 sessions) through an automated path rather than the manual MEMORY.md approach.

**How it works:**
- **Auto-Recall:** Before each AI turn, the plugin queries Supermemory and injects relevant stored memories into the agent's context. Heather automatically surfaces Josh's past preferences, action items, and context without requiring an up-to-date MEMORY.md.
- **Auto-Capture:** After each turn, the conversation is sent to Supermemory for extraction and storage. No manual memory update needed.
- **Slash commands:** `/remember` for manual saves, `/recall` for searches, plus agent-facing tools for autonomous memory management.
- **Custom containers:** Memory categories (personal, work, bookmarks) with automatic routing.

**Comparison to MEMORY.md approach:**

| Approach | Effort | Cost | Reliability |
|---|---|---|---|
| MEMORY.md (Finding 50) | Manual discipline required | Free | Depends on operator consistency |
| Supermemory plugin (this finding) | Setup once, automated | Supermemory Pro subscription | Automated — no discipline needed |
| Both together | Low ongoing effort | Subscription | Highest reliability |

**Prerequisites:** OpenClaw upgrade to 2026.5.7 (or current stable — plugin requires current runtime). Supermemory Pro subscription.

**Action:**
1. Determine whether Josh has or wants a Supermemory Pro subscription.
2. If yes: after upgrading to 2026.5.7, run `openclaw plugins install @supermemory/openclaw-supermemory` and complete `openclaw supermemory setup` with the API key from app.supermemory.ai.
3. If no: proceed with manual MEMORY.md discipline (Finding 50) — still the right answer, just requires more consistency.
4. Either way, this does not replace the immediate action on Finding 50 — 29 sessionless days means context is already lost and must be manually reconstructed first.

**Risk Assessment:** Low risk. Requires paid subscription. High reward if Supermemory Pro is accessible.

---

### Finding 58 — Compaction Entirely Missing: Long Heather Sessions Risk Silent Context Loss

**Risk:** MEDIUM
**Days Pending:** 29 (persistent gap, first formally documented this morning)

**Description:**
Josh's `openclaw.json` has no `agents.defaults.compaction` block. This means:

1. **No memory flush before compaction.** When sessions approach the context limit, OpenClaw compacts without first saving important session state to memory files. Any facts, preferences, or action items Heather accumulated during a long session are silently discarded.
2. **No soft threshold.** There is no early warning mechanism. The agent runs into the context limit cold rather than proactively consolidating memory at a configurable buffer.
3. **Default reserveTokensFloor is 20K** — community research confirms this is frequently insufficient. A single large email thread or calendar export can consume 20K tokens in one tool call, jumping past the threshold before compaction can run.

For comparison: Noah's instance has this correctly configured — `reserveTokensFloor: 40000`, `memoryFlush.enabled: true`, `softThresholdTokens: 4000`. Josh's instance is missing the entire block.

For a personal assistant that reads email threads, calendar events, and iMessage conversations — all large-context operations — this gap means long Heather sessions are operating without a safety net.

**Config to add to `openclaw.json` under `agents.defaults` (same as Noah's configuration):**
```json
"compaction": {
  "reserveTokensFloor": 40000,
  "memoryFlush": {
    "enabled": true,
    "softThresholdTokens": 4000
  }
}
```

**Note:** This config takes effect on the next session start. No restart of the bot required — just save the file.

**Risk Assessment:** Medium. Affects correctness of any Heather session longer than a few exchanges with large tool outputs. Zero risk to add the config.

---

## Persistent Findings — Status Table (Day 29 Morning)

| # | Title | Risk | Days Open |
|---|---|---|---|
| 48/56* | Google account never connected — CRITICAL | CRITICAL | 29 |
| 49/57* | inbox-state.json duplicate key + iMessage paused | HIGH | 1 |
| 50 | No MEMORY.md | MEDIUM | 29 |
| 51 | AGENTS.md not customized for Josh | MEDIUM | 29 |
| 52 | No active heartbeat | MEDIUM | Unknown |
| 53/59* | Retired fallback model (claude-3.5-haiku) | MEDIUM | 1 |
| 54 | 14+ releases behind stable | MEDIUM | 53+ |
| 55/60* | SOUL.md generic + no-emoji rule absent | MEDIUM | 1 |
| 56 | Two new betas today — gap grows | MEDIUM | 0 |
| 57 | Supermemory plugin opportunity | LOW | 0 |
| 58 | Compaction entirely missing | MEDIUM | 29 |
| Various | Findings 1–47 from prior scans | MIXED | Various |

*Evening scan (findings-2026-05-16-evening.md) renumbered some findings — asterisked items map to the evening scan's numbering.*

**Open: 58 | Resolved: 0 | Critical: 1 | High: 6+ | Medium: 25+ | Low: 5+**
**Evening scan total: 60 open (extended from this morning's 58)**

---

## Implementation Order — Day 29

### Tonight (Under 10 Minutes Each, Zero Dependencies)

1. **Fix retired fallback model** (Finding 53/59): Edit `openclaw.json`. Replace `openrouter/anthropic/claude-3.5-haiku` with `openrouter/anthropic/claude-haiku-4-5`. Restart. 3 minutes.
2. **Fix inbox-state.json** (Finding 49/57): Remove duplicate `last_email_check_ms` key. Set `imessage_monitoring_paused: false`. Validate with `python3 -m json.tool`. 5 minutes.
3. **Add no-emoji rule to SOUL.md** (Finding 55/60): One sentence in Boundaries section. 2 minutes.
4. **Add compaction config** (Finding 58): Paste the 5-line JSON block above into `agents.defaults` in `openclaw.json`. Takes effect next session. 3 minutes.

### This Weekend

5. **Connect Google Account** (Finding 48/56): Browser visit to `https://5.78.142.81.sslip.io#general`. Unlocks Gmail, Calendar, Contacts — the entire stated purpose of this deployment. ~10 minutes.
6. **Update SOUL.md fully** (Finding 55/60): Add timezone (America/Los_Angeles), daily rhythm, escalation protocol.

### Next Week

7. **Create MEMORY.md** (Finding 50): Manual approach — OR install Supermemory plugin (Finding 57) as the automated alternative.
8. **Customize AGENTS.md** (Finding 51).
9. **Plan OpenClaw upgrade to 2026.5.7** (Finding 54): Back up `openclaw.json` first.

---

*Generated by AlphaClaw Apex Fleet Research Agent — Morning Scan — 2026-05-16 (Day 29)*
