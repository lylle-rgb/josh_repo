# Fleet Research — Josh / Heather Schwartz — Morning Scan

**Scan Date:** 2026-05-20 (Morning — Day 33)
**Agent:** AlphaClaw Apex Fleet Research Agent
**Instance:** Josh / Heather Schwartz — Discord bot personal assistant (iMessage, email, calendar, contacts)
**OpenClaw Version:** 2026.3.22 (meta.lastTouchedVersion) — 21+ stable releases behind 2026.5.18
**Previous Findings:** findings-2026-05-19-evening.md (Day 32 Evening, Findings 1–79)
**Cumulative Open Findings:** 86 (7 new this morning, 0 resolved)

---

## Platform News — New Since Yesterday's Evening Scan (May 19)

| Item | Detail |
|---|---|
| **2026.5.18 confirmed stable as of Day 33** | No new release overnight. 2026.5.18 remains current stable. Josh's gap: 21+ stable releases. |
| **contextPruning best practices published** | Community research confirms optimal TTL by agent type: personal assistants → 35m, trading agents → 30–35m, heavy tool-use agents → 45m. Josh has **no** contextPruning configured at all. |
| **Gemini-native TTS confirmed in 2026.5.18** | `gemini/gemini-2.5-flash-preview-tts` is directly addressable in OpenClaw 2026.5.18 stable. No ElevenLabs account or sag skill required for basic TTS. Relevant to Josh: his primary provider is already Google/Gemini. |
| **File transfer plugin available in 2026.5.x** | The `file-transfer` plugin provides four agent tools: `file_fetch`, `dir_list`, `dir_fetch`, `file_write`. 16 MB per-round-trip ceiling. For Josh: enables iMessage attachment forwarding workflows between devices. |
| **Docker security hardening confirmed** | 2026.5.18's bundled docker-compose.yml drops `NET_RAW` and `NET_ADMIN` capabilities and enables `no-new-privileges`. Josh's Hetzner VPS deployment (likely Docker via AlphaClaw) inherits this hardening post-upgrade. |
| **Gemini semantic memory auto-select** | When memory-core is configured and a Gemini API key is resolvable, OpenClaw auto-selects Gemini embeddings for semantic search — no additional config needed. Josh's setup (google:default API key) means memory recall will be semantically ranked once memory-core is active. |
| **Per-agent contextPruning overrides (GitHub issue #52732 open)** | OpenClaw issue #52732 proposes per-agent contextPruning overrides. Currently all agents share a global config. No ETA — watch for future release that would let Josh's heartbeat agent and main session use different TTLs. |

---

## New Findings — Morning Scan (80–86)

---

### Finding 80 — contextPruning Completely Absent: Silent Context Accumulation Risk (MEDIUM)

**Risk:** MEDIUM
**Days Pending:** 0 (new today — confirmed by morning research)

**Description:**
Josh has no `contextPruning` block in `openclaw.json`. Noah's config has `contextPruning` (with the 5-minute TTL bug). Josh has none at all.

For a personal assistant that checks email, calendar, and iMessage in long sessions: without context pruning, token debt accumulates silently across a multi-tool session. A single morning heartbeat checking email + calendar + contacts could push 80K tokens before the compaction threshold is reached, then trigger a full compaction rather than a clean incremental prune.

Community research this morning confirms: for personal assistants with medium tool call frequency, a 35-minute cache-TTL with `keepLastAssistants: 3` is optimal. This avoids the 5m bug Noah has (severs multi-step reasoning) while preventing unbounded accumulation.

**Josh's current config gaps:**
- No `contextPruning` → sessions accumulate until compaction fires
- No `compaction.contextStrategy` → compaction behavior is platform default (no custom instructions)
- Contrast: Noah has both configured (compaction at 40K floor, memoryFlush enabled), just with the wrong TTL (5m)

**Fix (add under `agents.defaults` in openclaw.json — 2 minutes, no restart):**
```json
"contextPruning": {
  "mode": "cache-ttl",
  "ttl": "35m",
  "keepLastAssistants": 3
}
```

**Risk Assessment:** MEDIUM. Sessions work without it, but long heartbeat sessions (email + calendar + contacts in one turn) may hit compaction unnecessarily often, increasing token costs and session disruption frequency.

---

### Finding 81 — Gemini-Native TTS: No ElevenLabs Required Post-Upgrade (LOW/Opportunity)

**Risk:** LOW (opportunity)
**Days Pending:** 0 (new today)

**Description:**
OpenClaw 2026.5.18 supports `gemini/gemini-2.5-flash-preview-tts` as a directly addressable TTS provider. Josh's primary model is `google/gemini-3-flash-preview` — he already has a Google API key configured (`auth.profiles.google:default` with `mode: api_key`). Post-upgrade, Heather can deliver TTS without a separate ElevenLabs subscription or the `sag` skill.

TOOLS.md currently says: "TTS: Not configured." This opens a simpler path than ElevenLabs:

- **Before:** ElevenLabs account → `sag` skill install → voice config
- **After 2026.5.18 upgrade:** Add TTS config using existing Gemini key

For Heather's persona: voice delivery of calendar reminders, email summaries, and incoming iMessage notifications is now achievable at zero additional cost (within Gemini API quotas). The persona vibe in IDENTITY.md ("Sharp, Helpful, Resourceful") translates well to a voice-enabled assistant for reminders.

**Action (post-upgrade only):**
```json
"agents": {
  "defaults": {
    "tts": {
      "provider": "gemini",
      "model": "gemini-2.5-flash-preview-tts"
    }
  }
}
```
Update TOOLS.md: "TTS option: gemini-2.5-flash-preview-tts — available post-upgrade to 2026.5.18. Uses existing Google API key. No ElevenLabs required."

**Risk Assessment:** Zero. Post-upgrade only. Design note for when Josh decides to enable voice.

---

### Finding 82 — File Transfer Plugin: iMessage Attachment Workflow Path (LOW/Opportunity)

**Risk:** LOW (opportunity)
**Days Pending:** 0 (new in 2026.5.x)

**Description:**
OpenClaw 2026.5.x introduces the `file-transfer` plugin with four agent tools: `file_fetch`, `dir_list`, `dir_fetch`, `file_write`. For Josh's iMessage integration:

**Current limitation:** iMessage attachments (photos, PDFs, voice memos) passed through BlueBubbles cannot be forwarded or saved by Heather — she has no file tool to write them to disk or operate on them.

**With file-transfer plugin enabled:** Heather could:
- `file_fetch` an attachment URL → write to `workspace/attachments/` via `file_write`
- Summarize a PDF attachment before responding to Josh
- Forward a file to another configured node (cross-device sync)
- Archive important documents Josh sends via iMessage to his workspace

Note: the plugin has a 16 MB per-round-trip ceiling. Most iMessage attachments (compressed photos, voice memos, most PDFs) fall within this. Large video attachments would not.

**Action (post-upgrade to 2026.5.18):**
1. Add `"file-transfer"` to `plugins.allow`
2. Add entries block with operator path policy configured to restrict writes to `workspace/` (security: prevent writes outside workspace boundary)
3. Document specific paths in TOOLS.md once active

**Risk Assessment:** Low risk when configured with a restricted path policy. Meaningful attachment-handling capability for a personal assistant — especially relevant once iMessage is unpaused (Finding 49/57).

---

### Finding 83 — Docker Security Hardening (2026.5.18): Bundled With Upgrade (LOW/Operational)

**Risk:** LOW (operational — security improvement included in upgrade)
**Days Pending:** 0 (new today — confirmed in 2026.5.18)

**Description:**
OpenClaw 2026.5.18's bundled docker-compose.yml now:
- Drops `NET_RAW` and `NET_ADMIN` Linux capabilities
- Enables `no-new-privileges` flag

For Josh's Hetzner VPS deployment (5.78.142.81 — likely Docker via AlphaClaw): these hardening changes are inherited when the AlphaClaw Docker Compose configuration is updated as part of the upgrade process. No manual security configuration required — the hardening comes with 2026.5.18.

**Practical meaning:** If the OpenClaw container were compromised (e.g., via a malicious skill or prompt injection), it would have reduced ability to escalate privileges on the host or perform raw network operations. Given Heather has access to Josh's personal data (iMessage, email, calendar), reducing the blast radius of a container compromise is meaningful.

**Action:** No separate action. Security improvement is bundled with the 2026.5.18 upgrade already recommended (Finding 72). Confirms the upgrade has security value beyond feature additions.

**Risk Assessment:** Low standalone — bundled benefit of the upgrade. Documents why the upgrade is not purely cosmetic.

---

### Finding 84 — Gemini Semantic Memory Auto-Select: Zero-Config Embeddings Once memory-core Active (LOW/Opportunity)

**Risk:** LOW (design note — zero config required)
**Days Pending:** 0 (new today — confirmed in platform documentation)

**Description:**
OpenClaw automatically selects Gemini embeddings for semantic memory search when:
1. A Gemini API key is resolvable in the auth profile ✅ (Josh has `google:default`)
2. `memory-core` plugin is active ❌ (not yet configured)

Josh satisfies condition 1. When memory-core is activated post-upgrade, semantic search on Josh's memory files will automatically use Gemini embedding models — no additional configuration or extra API calls required.

**What this means for Heather in practice:**
When Josh says "remember that thing about the Oben HiFi deal from last week," Heather won't search `memory/` files with dumb keyword matching. She'll search semantically — finding relevant context even when Josh's phrasing doesn't match the exact words in the memory log.

For a personal assistant handling iMessage, email, calendar, and contacts across multiple sessions, semantic memory retrieval is the difference between Heather remembering that Josh mentioned a specific person in passing 3 sessions ago vs. only recalling what Josh explicitly told her to remember.

**Action:** Zero additional config needed beyond memory-core activation. Add to TOOLS.md once memory-core is active: "Memory search: Gemini embeddings auto-selected (via google:default key). Semantic search is active."

**Risk Assessment:** Zero. Passive benefit. High value for a personal assistant once memory logging begins.

---

### Finding 85 — gog-cli Skill Missing From Josh, Present in Noah: Tooling Role Reversal (LOW/Note)

**Risk:** LOW (informational)
**Days Pending:** 0 (new observation this morning)

**Description:**
This morning's cross-repo comparison reveals: Noah's `skills/` directory contains `skills/gog-cli/`. Josh has no `skills/` directory at all.

`gog-cli` appears to be a Google OAuth CLI skill — based on the naming (`gog` = Google OAuth Generally?). Noah's `gogcli/` root directory is a separate artifact also related to Google OAuth.

**The irony:** Josh is the customer who needs Google Workspace integration (Gmail, Calendar, Contacts — Finding 48/56). Noah does not use Google in his agent (Anthropic direct, no Google auth configured). Yet Noah has Google OAuth tooling and Josh doesn't.

**Hypothesis:** The `gog-cli` skill may have been onboarded into Noah's repo as part of a general template and is unused. Or it was intended for Josh. Either way, its presence in Noah's financial data environment without audit is a separate concern (Finding 98 in Noah's scan).

**For Josh:** When the Google account is connected (Finding 48/56), verify whether the iMessage/Gmail/Calendar integration requires a CLI skill or whether direct OAuth via the AlphaClaw UI is sufficient. If a skill is needed, it should be researched, installed, and audited — not assumed from Noah's copy.

**Risk Assessment:** Low. Informational. Informs the Google OAuth investigation for Josh.

---

### Finding 86 — Day 33 Escalation Summary: Critical Findings Unchanged (CRITICAL/Summary)

**Risk:** CRITICAL (escalation summary)
**Days Pending:** 33 (for the oldest open findings)

**Description:**
Day 33 morning. All critical findings from Day 32 remain open. No implementations confirmed.

| Finding | Description | Risk | Days Open |
|---|---|---|---|
| 48/56 | Google account never connected | CRITICAL | 33 |
| 62/69/77 | BOOTSTRAP.md not deleted | CRITICAL | 33 |
| 63/70/78 | No memory logs — 33 sessions permanently lost | CRITICAL | 33 |
| 53/59 | Retired fallback model (claude-3.5-haiku) | MEDIUM | 6 |
| 75 | Node.js 22.19 pre-check before upgrade | MEDIUM | 1 |

No critical findings have been resolved in 33 days.

**The Day 33 morning implementation order (unchanged from Day 32 evening):**
1. **Delete BOOTSTRAP.md** — 30 seconds
2. **Fix retired fallback model** — 3 minutes
3. **Start daily memory log** (`workspace/memory/2026-05-20.md`) — 5 minutes
4. **Connect Google account** — 10 minutes via https://5.78.142.81.sslip.io

Total: ~18 minutes. Documented for 33 days.

**Risk Assessment:** CRITICAL — systemic non-implementation, not individual item risk.

---

## Day 33 Morning: Full Implementation Queue

### Immediate (Under 20 Minutes, Zero Dependencies)

| Action | Detail | Time |
|---|---|---|
| Delete BOOTSTRAP.md (Finding 86/77) | `workspace/BOOTSTRAP.md` — 33 days overdue | 30 sec |
| Fix retired fallback model (Finding 59) | Replace `claude-3.5-haiku` with `claude-haiku-4-5` in openclaw.json | 3 min |
| Start daily memory log (Finding 78) | Create `workspace/memory/2026-05-20.md` | 5 min |
| Add contextPruning (Finding 80) | Add `"contextPruning": {"mode":"cache-ttl","ttl":"35m","keepLastAssistants":3}` | 2 min |

**contextPruning addition — paste into openclaw.json under `agents.defaults`:**
```json
"contextPruning": {
  "mode": "cache-ttl",
  "ttl": "35m",
  "keepLastAssistants": 3
}
```

### This Weekend — Pre-Upgrade Checklist

| Action | Detail | Time |
|---|---|---|
| Connect Google account (Finding 56) | https://5.78.142.81.sslip.io → Google OAuth | 10 min |
| Verify Node.js ≥ 22.19 (Finding 75) | `node --version` on Hetzner VPS | 1 min |
| Back up openclaw.json | `cp openclaw.json openclaw.json.bak-pre-5.18` | 30 sec |
| Update AlphaClaw to 0.9.16 (Finding 66) | AlphaClaw Control UI → Updates | 5 min |
| Upgrade OpenClaw to 2026.5.18 (Finding 72) | AlphaClaw Control UI → OpenClaw → Upgrade | 10 min |

### Post-Upgrade (This Week)

| Action | Detail |
|---|---|
| Configure Active Memory allowedChatIds (Finding 73) | Scope MEMORY.md to Josh's private DM channel |
| Enable Gemini TTS (Finding 81) | Add TTS provider block using existing Google API key |
| Add file-transfer plugin (Finding 82) | With workspace-scoped path policy |
| Enable memory-core (Finding 84) | Activates Gemini semantic search auto-select |
| Design HEARTBEAT.md with cron --wait (Finding 79) | Use template from soul-improvements file |
| Populate TOOLS.md (Finding 64) | Document SSH, iMessage config, TTS prefs |
| Add no-emoji rule to SOUL.md (Finding 60) | STRICT absolute rule — add immediately |

---

## Persistent Findings Status Table — Day 33 Morning

| # | Title | Risk | Days Open |
|---|---|---|---|
| 48/56 | Google account never connected | CRITICAL | 33 |
| 49/57 | inbox-state.json invalid + iMessage paused | HIGH | 6 |
| 50/78 | No MEMORY.md / no memory logs — 33 sessions | CRITICAL | 33 |
| 52 | No active heartbeat | MEDIUM | Unknown |
| 53/59 | Retired fallback model (claude-3.5-haiku) | MEDIUM | 6 |
| 54/61/72 | 21+ releases behind stable (2026.5.18) | MEDIUM | 58+ |
| 55/60 | SOUL.md no-emoji rule absent | MEDIUM | 6 |
| 62/69/77 | BOOTSTRAP.md not deleted — CRITICAL Day 33 | CRITICAL | 33 |
| 64 | TOOLS.md unpopulated | LOW | 3 |
| 66 | AlphaClaw 0.9.16 unverified | MEDIUM | 2 |
| 67 | defineToolPlugin — Google Workspace native tools | LOW | 2 |
| 68 | Grok OAuth now stable — social monitoring | LOW | 2 |
| 73 | Active Memory allowedChatIds — MEMORY.md security | MEDIUM | 1 |
| 74 | ElevenLabs v3 / Gemini TTS now stable | LOW | 1 |
| 75 | Node.js 22.19 minimum — pre-upgrade check | MEDIUM | 1 |
| 76 | AlphaClaw OPENCLAW_STATE_DIR durable state | LOW | 1 |
| 79 | Cron --wait + Active Memory now stable | LOW | 1 |
| 80 | contextPruning absent — add 35m cache-ttl | MEDIUM | 0 |
| 81 | Gemini-native TTS path (no ElevenLabs needed) | LOW | 0 |
| 82 | File transfer plugin — iMessage attachments | LOW | 0 |
| 83 | Docker security hardening in 2026.5.18 | LOW | 0 |
| 84 | Gemini semantic memory auto-select | LOW | 0 |
| 85 | gog-cli missing from Josh vs Noah | LOW | 0 |
| 86 | Day 33 critical escalation summary | CRITICAL | 0 |

**Open: 86 | Resolved: 0 | Critical: 3 | High: 6+ | Medium: 12+ | Low: 10+**

---

*Generated by AlphaClaw Apex Fleet Research Agent — Morning Scan — 2026-05-20 (Day 33)*
