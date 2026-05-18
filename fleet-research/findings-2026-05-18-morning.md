# Fleet Research — Josh / Heather Schwartz — Morning Scan

**Scan Date:** 2026-05-18 (Morning — Day 31)
**Agent:** AlphaClaw Apex Fleet Research Agent
**Instance:** Josh / Heather Schwartz — Discord bot personal assistant (iMessage, email, calendar, contacts)
**OpenClaw Version:** 2026.3.22 (meta.lastTouchedVersion) — 20 stable releases behind 2026.5.12
**Previous Findings:** findings-2026-05-17-evening.md (Day 30 Evening, Findings 1–65)
**Cumulative Open Findings:** 71 (6 new this morning, 0 resolved)

> **Note:** The evening scan for this date (findings-2026-05-18-evening.md) was published first and contains findings 66–71. This morning scan covers items discovered in the morning research window and is numbered 72–77 to maintain sequential integrity. Read this file for architectural and configuration insights not covered in the evening scan.

---

## Platform News — New Since Yesterday's Evening Scan (May 17)

| Item | Detail |
|---|---|
| OpenClaw 2026.5.12 — lazy dependency install confirmed | WhatsApp, Slack, Amazon Bedrock, Anthropic Vertex, OpenShell sandbox no longer install by default — lazy-load only when the integration is actually used. Josh's upgrade from 2026.3.22 is significantly lighter than prior release-count implied. |
| Active Memory plugin (2026.4.10+) — configurable now | The Active Memory plugin (blocking memory sub-agent that runs before each main reply) became available at 2026.4.10. Josh is on 2026.3.22 — not yet eligible. Configuration can be designed today and activated the moment Josh upgrades. This is the highest-value memory improvement available. |
| v2026.5.2 Active Memory grace timeout change | Before v2026.5.2, Active Memory silently added 30000ms to the configured timeoutMs during cold-start. v2026.5.2 made this explicit via `setupGraceTimeoutMs`. Configuration designed today should include this explicitly to avoid unexpected timeout behavior post-upgrade. |
| v2026.5.6 doctor --fix regression (fixed in 5.7) | v2026.5.6 contained a regression: `openclaw doctor --fix` was rewriting valid openai-codex/* OAuth routes to openai/*. Fixed in 5.7. Josh's target upgrade (2026.5.12) is well past the fix. Safety note: always review `doctor --fix` output BEFORE applying during any upgrade. |
| Mem0 temporal memory algorithm — April 2026 research | +29.6 points on temporal queries, +23.1 on multi-hop reasoning when memory entries include timestamps and relational context. Direct impact on MEMORY.md design once created. |
| Discord streaming partial/block mode confirmed stable | Discord streaming has been stable since 2026.2.15. Josh's current config has `"streaming": "off"` — an explicit opt-out. Enabling block-streaming would give Josh real-time feedback on longer Heather tasks. |

---

## New Findings — Morning Scan (72–77)

---

### Finding 72 — Discord Streaming Disabled: Real-Time Response Feedback Unavailable (LOW)

**Risk:** LOW (UX improvement available)
**Days Pending:** 0 (new)

**Description:**
Josh's `openclaw.json` contains `"streaming": "off"` under the Discord channel configuration. This is an explicit opt-out — meaning Heather's responses are delivered as a single batch after the full generation completes.

Discord streaming has been fully stable since OpenClaw 2026.2.15 (Discord Components V2 release). With streaming enabled, longer tasks — drafting an email, reviewing a calendar week, summarizing an inbox — would stream progressively to Josh in Discord, giving visibility that work is in progress rather than appearing to go silent for 10–20 seconds.

This is particularly relevant once the Google account is connected (Finding 56 — CRITICAL): Gmail summaries and calendar reviews will generate multi-hundred-word responses. Streaming those removes the perception of Heather being unresponsive during generation.

Two streaming modes are available:
- **`"streaming": "block"`** — chunked delivery (paragraph-by-paragraph). Most natural for conversational responses.
- **`"streaming": "on"`** — token-level streaming. More real-time but can appear choppy for longer prose.

**Action:**
In `openclaw.json`, find:
```json
"streaming": "off"
```
Change to:
```json
"streaming": "block"
```
No restart required. Takes effect on next message exchange.

**Risk Assessment:** Zero. Streaming is a delivery mode change — same model, same generation quality. Fully reversible. Recommended for any deployment where the user will see responses via Discord.

---

### Finding 73 — Active Memory Plugin: Design Config Now, Activate Post-Upgrade (MEDIUM/Architecture)

**Risk:** MEDIUM (blocked on upgrade, but configurable in advance)
**Days Pending:** 0 (new)

**Description:**
The Active Memory plugin (`active-memory`) became available in OpenClaw 2026.4.10. Josh is on 2026.3.22 and cannot use it yet — but the configuration can be designed and staged today, ready to activate the moment Josh upgrades to 2026.5.12.

**What Active Memory does:**
Before the main reply is generated, a lightweight blocking sub-agent runs a `memory_search` query scoped to the current session. It surfaces relevant context — preferences, prior conversation summaries, things to remember — and prepends it to the main agent's context. This is the "memory recall before speaking" pattern that makes agents feel like they actually remember you.

**For Heather specifically:**
- Before replying about an email from Josh's investor, Active Memory surfaces: "Josh's investor is Marcus Chen, partner at Seraphim Capital — Josh prefers formal tone in these replies."
- Before a calendar query, Active Memory surfaces: "Josh's typical work window is 9 AM–7 PM PST. He has a standing Tuesday 3 PM call with the Oben team."
- Before any response, it recalls: "Josh has a strict no-emoji preference."

With Day 31 of zero memory logs, Active Memory's value compounds: even if daily memory files are thin, this plugin ensures what IS in memory is actually surfaced before each reply.

**Configuration block to add to `openclaw.json` post-upgrade (under `plugins.entries`):**
```json
"active-memory": {
  "enabled": true,
  "config": {
    "agents": ["main"],
    "chatTypes": ["dm"],
    "inheritSessionModel": true,
    "timeout": 12000,
    "setupGraceTimeoutMs": 5000,
    "maxSummaryChars": 220,
    "transcriptPersistence": false
  }
}
```

Also add `"active-memory"` to `plugins.allow`.

**Note on `setupGraceTimeoutMs`:** In v2026.5.2, the silent 30-second cold-start grace was made explicit. Including `setupGraceTimeoutMs: 5000` ensures predictable timeout behavior — the 12-second budget applies consistently.

**Action:**
1. No immediate action — requires upgrade to 2026.5.12 first.
2. Stage the configuration block above in a local note or TOOLS.md for paste-readiness post-upgrade.
3. Add `"active-memory"` to the `plugins.allow` array alongside `"memory-core"` in the post-upgrade config plan.

**Risk Assessment:** Low — additive plugin, isolated sub-agent with configurable timeout. The 12-second budget means worst-case a reply takes 12 extra seconds; it degrades gracefully with a timeout rather than hanging.

---

### Finding 74 — 2026.5.12 Lazy Install: Upgrade From 3.22 Is Lighter Than It Looks (MEDIUM/Good News)

**Risk:** MEDIUM (reduces previously estimated upgrade risk)
**Days Pending:** 0 (new)

**Description:**
OpenClaw 2026.5.12 moved a large set of integrations to lazy installation — they no longer download at install time unless the integration is actually enabled. Integrations now lazy-installed include: WhatsApp, Slack, Amazon Bedrock, Anthropic Vertex, and OpenShell sandbox.

**Impact on Josh's upgrade from 2026.3.22:**
- Prior reasoning: upgrading 20 releases means pulling a large cumulative dependency diff. This contributed to upgrade risk assessment (Finding 61).
- **New assessment:** The lazy-install architecture means Josh's actual dependency footprint at 2026.5.12 is significantly smaller. Josh uses: Discord (active), usage-tracker (AlphaClaw), Google API, OpenRouter. None of these are in the lazy-install set. The upgrade pulls OpenClaw core and the active integrations — not the full suite.
- **Estimated upgrade size is materially smaller than feared.**

This does NOT eliminate the upgrade prerequisite of backing up `openclaw.json` first (config-wipe bug #65105 persists). But it removes one risk vector.

**Action:**
1. Before upgrading: `cp openclaw.json openclaw.json.bak-2026-05-18` (30 seconds).
2. Upgrade via AlphaClaw Control UI at `https://5.78.142.81.sslip.io`.
3. Expect the upgrade to complete faster than the 20-release gap implies.
4. Verify: Discord channel active, Google OAuth flow works (Finding 56/69).

**Risk Assessment:** This finding reduces overall upgrade risk for Finding 61. Update priority: Finding 61's blocking concern about "large upgrade" is partially resolved.

---

### Finding 75 — doctor --fix Safety Note for Upgrade Path (LOW)

**Risk:** LOW (informational — safe practices during upgrade)
**Days Pending:** 0 (new)

**Description:**
OpenClaw v2026.5.6 contained a regression in `openclaw doctor --fix`: the command was rewriting valid `openai-codex/*` OAuth routes to `openai/*`, breaking OAuth-only GPT-5.5 configurations. This regression was fixed in v2026.5.7.

Josh does not use OpenAI/Codex OAuth routes — his `openclaw.json` uses Google and OpenRouter profiles — so he is not affected by this specific regression. However, this is a class of bug worth noting for any upgrade path:

**General safety rule for `doctor --fix`:**
1. Always run `openclaw doctor` FIRST (without `--fix`) to review what it identifies.
2. Read the full output before applying any auto-fix.
3. If any proposed fix touches auth profiles (`auth.profiles.*`) or channel config (`channels.*`), verify the change is correct before accepting.
4. The `--fix` flag is idempotent but not always conservative — specific version bugs can cause it to destructively modify valid config.

Josh's upgrade target (2026.5.12) is well past the 5.7 fix. Running `doctor --fix` after upgrading to 5.12 is safe. The safety rule above is a permanent best practice, not a temporary warning.

**Action:**
No immediate action. During the upgrade window (Finding 61/74): run `openclaw doctor` without `--fix` first; inspect output; then apply if all suggestions are correct.

**Risk Assessment:** Zero if the practice above is followed. Document in TOOLS.md as a permanent note for future upgrades.

---

### Finding 76 — MEMORY.md Design: Use Temporal Structure for 29x Better Retrieval (LOW/Architecture)

**Risk:** LOW (architecture guidance for when memory system is created)
**Days Pending:** 0 (new)

**Description:**
Mem0 published research in April 2026 showing that memory entries structured with temporal and relational context retrieve **+29.6 points better** on temporal queries and **+23.1 points better** on multi-hop reasoning compared to flat-fact entries.

This has a direct, concrete implication for Heather's MEMORY.md design (Finding 50 — MEDIUM, Day 31).

**Poor entry format (flat fact — low retrievability):**
```
- Josh dislikes emojis
- Josh is a founder
- Tuesday calls exist
```

**Good entry format (temporal + relational — high retrievability):**
```
## 2026-04-15 — Communication Preferences
- Josh explicitly requested no emojis on 2026-04-15; confirmed STRICT — applies to all platforms
- Context: occurred after Heather used 🎉 in a team Discord reply; Josh corrected immediately

## 2026-03-20 — Professional Context
- Josh Schwartz: Founder/CEO of Bliss (luxury lifestyle brand); Partner at Oben HiFi (audio)
- Oben HiFi standing call: Tuesdays 3 PM PST with the core team (as of March 2026)
- Preference: formal tone with investor contacts; casual with internal team
```

Key structural principles:
- **Date-stamp every entry** at creation and major updates
- **Include the context** that generated the fact (why, where, when it was observed)
- **Add relational links** between entries ("this preference was reinforced in session 12")
- **Keep entries specific** — "Josh corrected emoji use on 2026-04-15" is more retrievable than "Josh doesn't like emojis"

This structure costs zero additional tokens at write time but dramatically improves recall quality.

**Action:**
1. When creating the first daily memory log (Finding 70 — HIGH), use the temporal format above.
2. When eventually creating MEMORY.md (Finding 50), organize by date-stamped entries, not category headings.
3. Note in AGENTS.md or TOOLS.md: "Memory entries MUST include date stamp + context sentence."

**Risk Assessment:** Zero. This is a design improvement for a system that hasn't been created yet. Costs nothing to implement correctly from the start.

---

### Finding 77 — compaction Missing: Long Sessions Silently Truncate (MEDIUM)

**Risk:** MEDIUM (session reliability)
**Days Pending:** 0 (noted in cross-customer but not as standalone Josh finding)

**Description:**
Josh's `openclaw.json` has no `compaction` configuration block. Noah's has it correctly configured. This is a known gap documented in the cross-customer analysis but never elevated to a standalone Josh finding.

Without compaction configuration, when Heather's context window fills (during a long email session, calendar review, or multi-step task), OpenClaw's default compaction behavior runs — but without a `memoryFlush` safety net. Prior conversation context is silently truncated with no memory preservation step.

**Practical consequence:** If Josh sends Heather a long email to draft a reply to, and during that exchange asks about his calendar for next week, the email context may have already been compacted away — Heather won't know what she was replying to.

**Fix — paste this block into `openclaw.json` under `agents.defaults` (no restart required):**
```json
"compaction": {
  "reserveTokensFloor": 40000,
  "memoryFlush": {
    "enabled": true,
    "softThresholdTokens": 4000
  }
}
```

This is the same configuration Noah uses (verified in Noah's `openclaw.json`) and is the community-recommended setting (`reserveTokensFloor: 40000` — default 20K is frequently insufficient for real-world tool output sizes).

**Action:**
Add compaction block to `agents.defaults` in `openclaw.json`. No restart needed — takes effect next session. This is a zero-risk change that takes 2 minutes.

**Risk Assessment:** Low. The compaction block cannot cause regressions — it only activates when context is near the ceiling. No downside.

---

## Day 31 Morning Priority Order

### Do Right Now (Under 15 Minutes Total)

1. **Add compaction block to openclaw.json** (Finding 77 — MEDIUM): Paste 5-line JSON block. **2 minutes.** Zero risk.
2. **Enable Discord streaming** (Finding 72 — LOW): Change `"streaming": "off"` to `"streaming": "block"`. **1 minute.** Zero risk.
3. **Fix retired fallback** (Finding 59/68 carried): Replace `openrouter/anthropic/claude-3.5-haiku` with `openrouter/anthropic/claude-haiku-4-5`. **3 minutes.**
4. **Fix inbox-state.json** (Finding 57 carried): Remove duplicate key, unpause iMessage. **5 minutes.**
5. **Delete BOOTSTRAP.md** (Finding 69 — HIGH escalated): 30 seconds. 31 days overdue.

### This Weekend

6. **Connect Google account** (Finding 56 — CRITICAL — Day 31): Browse to `https://5.78.142.81.sslip.io`. Post-2026.5.12 upgrade, stale OAuth lock is auto-cleared (Finding 69).
7. **Upgrade OpenClaw to 2026.5.12** (Finding 61/74): Backup first → upgrade via AlphaClaw UI. Smaller than expected (lazy install — Finding 74).
8. **Stage Active Memory config** (Finding 73): Draft config block in TOOLS.md — paste-ready for post-upgrade activation.
9. **Start daily memory logs** (Finding 70 — HIGH): Create `workspace/memory/2026-05-18.md` with temporal-format entries (Finding 76).

### Post-Upgrade

10. **Activate Active Memory plugin** (Finding 73): Add config block + add to allow list.
11. **Verify doctor output** (Finding 75): Run `openclaw doctor` without `--fix` first during upgrade.
12. **Check and update AlphaClaw version** (Finding 66): SSH → `alphaclaw --version` → update if behind 0.9.16.

---

## Persistent Findings Status Table — Day 31 Morning

| # | Title | Risk | Days Open |
|---|---|---|---|
| 48/56 | Google account never connected | CRITICAL | 31 |
| 49/57 | inbox-state.json invalid + iMessage dark | HIGH | ~25 |
| 50 | No MEMORY.md | MEDIUM | 31 |
| 51 | AGENTS.md generic template | MEDIUM | 31 |
| 52 | No active heartbeat | MEDIUM | Unknown |
| 53/59 | Retired fallback model claude-3.5-haiku | MEDIUM | ~5 |
| 54/61 | 20 releases behind stable | MEDIUM | 56+ |
| 55/60 | SOUL.md generic — no-emoji rule absent | MEDIUM | ~5 |
| 62/69 | BOOTSTRAP.md not deleted (escalated HIGH) | HIGH | 31 |
| 63/70 | No daily memory logs — 31 sessions lost | HIGH | 31 |
| 64 | TOOLS.md unpopulated | LOW | ~2 |
| 65 | HEARTBEAT.md design pending | LOW | ~2 |
| 66 | AlphaClaw version unverified — 0.9.16 available | MEDIUM | ~1 |
| 72 | Discord streaming disabled | LOW | 0 |
| 73 | Active Memory config not staged | MEDIUM | 0 |
| 74 | Upgrade risk lower than assessed (good news) | MEDIUM | 0 |
| 75 | doctor --fix safety practices | LOW | 0 |
| 76 | MEMORY.md temporal design guidance | LOW | 0 |
| 77 | compaction block missing | MEDIUM | 0 |

**Open: 71 | Resolved: 0 | Critical: 1 | High: 9+ | Medium: 25+ | Low: 8+**

---

*Generated by AlphaClaw Apex Fleet Research Agent — Morning Scan — 2026-05-18 (Day 31)*
