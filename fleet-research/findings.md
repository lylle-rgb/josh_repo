# Fleet Research Findings — Josh / Heather Schwartz

**Scan date:** 2026-06-17 (morning) · Previous scan: 2026-06-16 morning
**Researcher:** AlphaClaw Fleet Agent
**Instance:** josh_repo (Heather Schwartz — personal assistant)
**Current version:** 2026.3.22
**Latest stable:** 2026.6.8 (released June 16, 2026 — NOW STABLE)
**Latest beta:** 2026.6.8 is the new stable; next beta track not yet tagged

> ✅ RESOLVED (June 17): workspace/SOUL.md — personalized with Josh's hard rules
> ✅ RESOLVED (June 17): workspace/AGENTS.md — personalized with emoji override at top
> ✅ RESOLVED (June 17): workspace/TOOLS.md — populated with AlphaClaw UI, Discord, iMessage, models
> ✅ RESOLVED (June 17): workspace/BOOTSTRAP.md — deleted (no longer burning context tokens)
> ✅ RESOLVED (June 17): memory/heartbeat-state.json — created
> ✅ RESOLVED (June 16): workspace/MEMORY.md — created and seeded
> ✅ RESOLVED (June 16): workspace/HEARTBEAT.md — populated with active monitoring schedule
> ✅ RESOLVED (June 16): gemini-2.5-flash → gemini-3.5-flash in openclaw.json
> ⛔ Still open: Google Workspace OAuth not connected — email/calendar inaccessible
> ⛔ Still open: OpenClaw 86+ days outdated (2026.3.22 vs 2026.6.8). Requires VPS upgrade.
> ⛔ Still open: Dreaming not enabled in openclaw.json
> ⛔ Still open: compaction/memoryFlush not configured in openclaw.json
> ⛔ Still open: Discord security open to all (groupPolicy: open)

---

## ⭐ Finding 19 — OpenClaw 2026.6.8 Now STABLE (NEW — June 17 Morning)

**Risk: INFO / Upgrade path now clear to 2026.6.8**

OpenClaw 2026.6.8 has graduated from beta to stable as of June 16–17, 2026. Previous stable target was 2026.6.6 — that target is now superseded. The new upgrade path for Josh is:

```
2026.3.22 → 2026.5.27 → 2026.6.2 → 2026.6.5 → 2026.6.6 → 2026.6.8
```

**Key new fixes in 2026.6.8 relevant to Heather:**

- **iMessage reply-action handling** (PR #93137): Disabled reply-action handling for iMessage — fixes spurious reactions to iMessages. Directly relevant once iMessage bridge is restored.
- **iMessage split-send coalescing removed** (PR #93143): Client-side split-send coalescing is gone — iMessage sends are now simpler and more reliable.
- **iMessage NUL byte fix**: NUL bytes in sent-message echoes no longer corrupt the session.
- **Memory/state recovery**: Stronger recovery from context compaction events — state is preserved across resets.
- **Safer model routing**: Provider catalog routing hardened; fewer silent fallback failures.
- **Telegram rich rendering**: Tables, lists, expandable blockquotes, intentional line breaks (not relevant for Josh's Discord/iMessage setup but included in the same upgrade).
- **WhatsApp ACP bindings honored**: WhatsApp now respects configured ACP bindings.
- **Slack outbound hooks**: `message_sent` hooks now fire for Slack (bonus for any future Slack setup).
- **Usage footer enhanced**: `/usage` generates a full footer report after each AI reply with accurate limits.

**Haiku 4.5 upgrade now available:**
- Josh's fallback 2 is `openrouter/anthropic/claude-3.5-haiku`
- After upgrading to 2026.6.8, this can be changed to `openrouter/anthropic/claude-haiku-4-5`
- **Important:** The Haiku 4.5 catalog bug (incorrect profile migration to Sonnet) is fixed in 2026.6.8 — safe to upgrade model ID once on this version
- Haiku 4.5 offers improved reasoning and speed at similar cost

**Action:**
```bash
openclaw update   # run on VPS at 5.78.142.81
```
Staged upgrade: `2026.3.22 → 2026.5.27 → 2026.6.2 → 2026.6.5 → 2026.6.6 → 2026.6.8`

After reaching 2026.6.8, update `openclaw.json`:
```json
"fallbacks": [
  "openrouter/google/gemini-3.5-flash",
  "openrouter/anthropic/claude-haiku-4-5"
]
```

---

## ⭐ Finding 20 — Discord Security: Open to All (NEW — June 17 Morning)

**Risk: MEDIUM-HIGH**

Heather's Discord configuration uses `groupPolicy: open` and `dmPolicy: open` with `allowFrom: ["*"]`. Anyone who can reach Josh's Discord server can DM Heather and get responses — including access to responses that draw on Josh's personal context (calendar, contacts, Bliss business info).

Comparison: Noah's instance uses pairing + an allowlist, significantly limiting who can trigger the agent.

This creates a meaningful exposure surface: a bad actor who joins Josh's Discord could social-engineer Heather into revealing meeting times, business details, or contact information.

**Action (requires VPS, post-upgrade):**
In `openclaw.json` under `channels.discord`:
```json
"groupPolicy": "allowlist",
"dmPolicy": "allowlist",
"allowFrom": ["JOSH_DISCORD_USER_ID"],
"pairingEnabled": true
```
Replace `JOSH_DISCORD_USER_ID` with Josh's actual Discord user ID from the guild.

**Note:** Josh's guild ID is `1484448262290276464`. Only Josh's user ID should be in `allowFrom` for DMs. Group messages can stay looser if Josh has people he trusts in the server — but the current `"*"` wildcard should be replaced.

---

## ⭐ Finding 21 — MEMORY.md Size Monitoring (NEW — June 17 Morning)

**Risk: LOW (proactive hygiene)**

OpenClaw enforces a hard cap of **20,000 characters per individual file** loaded into context. Files exceeding this cap are silently truncated. The combined bootstrap file cap across all files is **150,000 characters**.

Heather's current `MEMORY.md` is 3,297 bytes — well within limits. However, as Heather learns more about Josh (new contacts, business context, lessons), MEMORY.md will grow. Left unchecked, it will eventually exceed 20,000 characters and start losing the oldest memories silently.

**Best practice (no action needed today, but Heather should be aware):**
- Keep MEMORY.md under 15,000 characters (~12,000 words) as a safety margin
- Move old, resolved issues to dated daily notes in `memory/YYYY-MM-DD.md`
- Dreaming (Finding 7) will help by auto-consolidating and pruning MEMORY.md nightly

**Immediate action:** None — MEMORY.md is currently healthy. This is a watch item.

---

## ⭐ Finding 22 — Dreaming Still Not Enabled (MEMORY.md Now Exists) (Escalated — June 17)

**Risk: HIGH**

Dreaming was first flagged June 12 (Finding 7). MEMORY.md was created June 16. Dreaming still has not been enabled in `openclaw.json`. Now that MEMORY.md exists and is seeded, Dreaming would begin consolidating daily session notes into long-term memory automatically each night at 3 AM.

Without Dreaming:
- Session context builds up in daily notes with no automated consolidation
- MEMORY.md stays static between fleet agent scans
- Heather cannot independently grow her long-term memory

**Action (requires VPS/openclaw.json edit):**
```json
"dreaming": {
  "enabled": true,
  "schedule": "0 3 * * *",
  "maxPromotion": 10,
  "minScore": 0.7
}
```
Add under `agents.defaults` in `openclaw.json`. The `0 3 * * *` schedule runs at 3 AM VPS time (adjust to 3 AM PST = 11 AM UTC if VPS is UTC).

---

## Previous Findings (June 16 Morning)

---

## Finding 1 — Version Outdated (86+ Days Behind)

**Risk: HIGH**

Heather is running OpenClaw `2026.3.22`. Current stable is now `2026.6.8` (as of June 16–17). That's an 86+ day gap spanning 12+ releases.

**Key fixes in the missed window directly relevant to Heather:**
- **Gateway restart wedge** (2026.6.6): Failed provider refresh could lock the gateway until manual restart — now self-recovers.
- **Native hook relay leak** (2026.6.6): Abandoned connections accumulated indefinitely on long-running agents — now bounded.
- **iMessage recovery** (2026.6.5): Private-API failures and send timeouts now explain themselves; split-send coalescing honors balloon metadata.
- **iMessage reply-action + NUL byte fixes** (2026.6.8): See Finding 19.
- **Parallel web search built-in** (2026.6.5): No separate setup required.
- **MCP tool result coercion** (2026.6.5): Non-text/image MCP blocks no longer poison session history with Anthropic 400 errors.
- **Meeting Notes** (2026.5.26): Real-time Discord voice call transcription.

**Action:**
```bash
openclaw update   # on VPS 5.78.142.81
```
Staged: `2026.3.22 → 2026.5.27 → 2026.6.2 → 2026.6.5 → 2026.6.6 → 2026.6.8`

---

## Finding 2 — Google Workspace Not Connected (Critical Gap)

**Risk: CRITICAL**

No Google accounts are connected via OAuth. Heather cannot access Gmail, Google Calendar, or Google Contacts. iMessage has been paused since ~April 27, 2026. Email and calendar have been offline for 80+ days.

**Action:**
1. Go to AlphaClaw UI: `https://5.78.142.81.sslip.io#general`
2. Under Google Workspace, provide OAuth client credentials from Google Cloud Console
3. Authorize Gmail, Google Calendar, Google Contacts (minimum)
4. Steps in `workspace/memory/onboarding-google.md`

**Alternative path:** See Finding 14 (Nylas CLI) if GCP OAuth is the blocker.

---

## Finding 3 — Concurrent Web Search Bug (Gemini-3-Flash)

**Risk: MEDIUM**

Known OpenClaw issue (#30675): when a subagent fires multiple parallel `web_search` calls, subsequent calls fail silently with `missing_gemini_api_key`. Resolved on upgrade to 2026.6.6+ (built-in parallel web search).

---

## Finding 4 — No Memory Protection Before Compaction

**Risk: HIGH**

`openclaw.json` has no compaction settings. Without `memoryFlush`, OpenClaw does not trigger a memory-write turn before compaction. Session context is silently lost at the context limit.

**Action:**
Add to `openclaw.json` under `agents.defaults`:
```json
"compaction": {
  "reserveTokensFloor": 40000,
  "memoryFlush": {
    "enabled": true,
    "softThresholdTokens": 4000
  }
},
"contextPruning": {
  "mode": "cache-ttl",
  "ttl": "6h"
}
```

---

## Finding 7 — Dreaming (Memory Consolidation) Not Enabled

**Risk: HIGH (escalated — see Finding 22)**

See Finding 22 above — MEMORY.md now exists since June 16, making Dreaming actionable immediately.

---

## Finding 10 — AGENTS.md Emoji Rule Contradiction

**Risk: ✅ RESOLVED — June 17, 2026**

Josh-Specific Hard Rules section added to top of `workspace/AGENTS.md`. The "React Like a Human!" section is now marked `⛔ SUSPENDED FOR THIS INSTANCE`. SOUL.md also updated with Josh's hard preferences.

---

## Finding 13 — Discord Streaming: Use "progress" Mode

**Risk: LOW**

`"progress"` mode batches tool-use turns and produces cleaner responses for Heather's use case.

**Action (post-upgrade to 2026.5.3+):**
```json
"channels": {
  "discord": {
    "streaming": "progress"
  }
}
```

---

## Finding 14 — Nylas CLI: Alternative Email/Calendar Integration

**Risk: MEDIUM**

If GCP OAuth is the blocker for Finding 2, Nylas CLI provides an alternative single-auth flow:
- 72+ commands across Gmail, Outlook, Exchange, Yahoo, iCloud, IMAP
- `openclaw skill install nylas-cli`
- Note: email transits a third-party API

---

## Finding 15 — NVIDIA SkillSpector (Post-Upgrade Passive Benefit)

**Risk: LOW**

OpenClaw 2026.6.1 shipped Skill Workshop with NVIDIA SkillSpector integration — skills scanned for prompt injection risks. Activates automatically on upgrade to 2026.6.6+.

---

## Summary Table (Updated June 17 Morning)

| Finding | Priority | Effort | Impact | Status |
|---|---|---|---|---|
| ~~gemini-2.5-flash deadline~~ | ~~CRITICAL~~ | ~~30 sec~~ | ~~Fallback chain~~ | ✅ FIXED 2026-06-16 |
| ~~MEMORY.md missing~~ | ~~CRITICAL~~ | ~~Low~~ | ~~Long-term memory~~ | ✅ CREATED 2026-06-16 |
| ~~HEARTBEAT.md empty~~ | ~~HIGH~~ | ~~5 min~~ | ~~Proactive monitoring~~ | ✅ POPULATED 2026-06-16 |
| ~~SOUL.md not personalized~~ | ~~HIGH~~ | ~~5 min~~ | ~~Identity + hard rules~~ | ✅ DONE 2026-06-17 |
| ~~AGENTS.md emoji contradiction~~ | ~~HIGH~~ | ~~5 min~~ | ~~Stops violating Josh's rule~~ | ✅ DONE 2026-06-17 |
| ~~TOOLS.md template only~~ | ~~LOW~~ | ~~Low~~ | ~~Fewer clarifying questions~~ | ✅ DONE 2026-06-17 |
| ~~BOOTSTRAP.md stale~~ | ~~LOW~~ | ~~30 sec~~ | ~~Context token waste~~ | ✅ DELETED 2026-06-17 |
| ~~heartbeat-state.json missing~~ | ~~LOW~~ | ~~Low~~ | ~~State tracking~~ | ✅ CREATED 2026-06-17 |
| 2. Connect Google Workspace | CRITICAL | Medium | Unlocks email/calendar | ⏳ Day 87 |
| 1. Upgrade to 2026.6.8 | HIGH | Low | All iMessage + gateway fixes | ⏳ Day 87 |
| 22. Enable Dreaming | HIGH | Low | Nightly memory consolidation | ⏳ New 06-17 |
| 4. Add compaction/memoryFlush | HIGH | Low | Memory safe on compaction | ⏳ Day 87 |
| 20. Discord security (open→allowlist) | MEDIUM-HIGH | Low | Protects Josh's personal context | 🆕 New 06-17 |
| 3. Concurrent search bug | MEDIUM | Low (update first) | Research reliability | ⏳ Unresolved |
| 14. Nylas CLI alternative path | MEDIUM | Low | Unblocks email if OAuth blocked | 06-15 |
| 19. 2026.6.8 now stable + Haiku 4.5 | INFO | None (post-upgrade) | New upgrade target | 🆕 New 06-17 |
| 13. Discord streaming → "progress" | LOW | Low | Cleaner responses | 06-15 |
| 15. NVIDIA SkillSpector post-upgrade | LOW | None | Passive skill security | 06-15 |
| 21. MEMORY.md size monitoring | LOW | Watch only | Proactive hygiene | 🆕 New 06-17 |

---

## Remaining Open Action List (June 17 Morning)

### Requires VPS access
1. **[CRITICAL]** Connect Google Workspace OAuth at `https://5.78.142.81.sslip.io#general`
2. **[HIGH]** Upgrade OpenClaw: `openclaw update` (staged: 2026.3.22 → 2026.5.27 → 2026.6.2 → 2026.6.5 → 2026.6.6 → 2026.6.8)
3. **[HIGH]** Add compaction/memoryFlush + contextPruning to `openclaw.json` (see Finding 4)
4. **[HIGH]** Enable Dreaming in `openclaw.json` (see Finding 22 / Finding 7)
5. **[MEDIUM-HIGH]** Tighten Discord allowFrom: replace `"*"` with Josh's Discord user ID (see Finding 20)
6. **[LOW]** Enable Discord streaming `"progress"` mode (see Finding 13)

### After upgrade to 2026.6.8
7. **[LOW]** Upgrade fallback 2 from `claude-3.5-haiku` → `claude-haiku-4-5` in `openclaw.json`

---

*Sources: [OpenClaw Releases](https://github.com/openclaw/openclaw/releases), [Releasebot OpenClaw](https://releasebot.io/updates/openclaw), [OpenClaw Memory docs](https://docs.openclaw.ai/concepts/memory), [OpenClaw Cron/Heartbeat docs](https://docs.openclaw.ai/automation), [VelvetShark Memory Guide](https://velvetshark.com/openclaw-memory-masterclass), [OpenClaw 2026.6.8 Analysis](https://www.xugj520.cn/en/archives/openclaw-2026-6-8-release-updates.html)*
