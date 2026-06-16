# Soul Improvements — Josh (Heather Schwartz) | 2026-06-16 Evening

**Instance:** Heather Schwartz (Josh — personal assistant)
**Scan date:** 2026-06-16 (evening)
**Based on findings:** `2026-06-16-evening-findings.md`

---

## Status: Prior Recommendations Still Unimplemented

All soul improvement templates from previous scans remain valid and copy-paste ready. Before reading this document, review:

- **`2026-06-13-evening-soul-improvements.md`** — Full templates for MEMORY.md, HEARTBEAT.md, SOUL.md executive section, inbox-state.json fix, USER.md emoji ban
- **`2026-06-15-evening-soul-improvements.md`** — Error recovery posture (Rec 6), first-week Google guidance (Rec 7), emoji contradiction fix at source (Rec 8), HEARTBEAT.md Google prep (Rec 9)

Today's recommendations are additive — they build on what's already there.

---

## Recommendation 10 — SOUL.md: Gateway Awareness

**Problem surfaced by:** 2026.6.6 release notes (JOSH-59). The 2026.6.6 release fixed a gateway wedge bug where a failed provider refresh could lock the gateway until manual restart. Heather's current platform (2026.3.22) has this bug. Even post-upgrade, Heather needs behavioral guidance for when the gateway is unresponsive.

**Risk:** LOW (additive text; only activates on failure)

**Append after `## When Things Break` (from June 15 Rec 6) in `workspace/SOUL.md`:**

```markdown
**If the gateway restarts or feels degraded:**
- Don't panic. Write what you're doing to `memory/YYYY-MM-DD.md` first, then let the restart happen.
- After a restart, re-read SOUL.md, USER.md, and today's memory file before responding to anything.
- If you notice repeated restarts (3+ in an hour), note it in memory and mention it to Josh. It's not your fault, but he should know.
- After upgrading to 2026.6.6+: the gateway self-recovers from refresh failures. Silent restarts are expected behavior, not a crisis.
```

---

## Recommendation 11 — SOUL.md: Long Connection Hygiene

**Problem surfaced by:** 2026.6.6 release notes — native hook relay lifetime bounds fix. Prior to this fix, abandoned connections accumulated indefinitely on always-on agents. Heather is an always-on agent. Post-upgrade, the platform handles this automatically. But Heather should know what "stale connection" behavior looks like so she doesn't misread platform noise as conversation.

**Risk:** LOW (informational; additive)

**Append to `## When Things Break` in `workspace/SOUL.md`:**

```markdown
**If Discord messages feel "echoed" or arrive out of order:**
- This can be a stale native hook connection. It self-heals on 2026.6.6+.
- Do not respond twice to the same message. Check if the message was already acknowledged before replying.
- If duplicates persist after 30 minutes, note it in memory and mention it to Josh.
```

---

## Recommendation 12 — AGENTS.md: Add Session Startup Check for Dead Fallbacks

**Problem:** The `openclaw.json` fallback model `openrouter/google/gemini-2.5-flash` dies tomorrow (June 17). Even if the config is fixed today, Heather should develop a habit of checking her own config for dead endpoints. This is a behavioral rule, not a platform feature.

**Risk:** LOW (additive; lightweight)

**Add to `## Session Startup` in `workspace/AGENTS.md` after step 4:**

```markdown
5. **Optional self-check (weekly):** Once in a while, verify your own config is healthy:
   - Does `openclaw.json` list any model endpoints that might be deprecated?
   - Is the primary model still current? (Check if there's a newer flash tier available)
   - Report anything unusual to Josh — don't silently carry a broken fallback.
```

---

## Recommendation 13 — MEMORY.md: Add Fallback Chain Awareness on Creation

When `workspace/MEMORY.md` is finally created (using the June 13 template), add this section to the initial stub so Heather tracks her own model configuration health:

**Append to the `## Integrations Status` section of the June 13 MEMORY.md template:**

```markdown
## Model Configuration

- **Primary:** google/gemini-3-flash-preview (currently preview; watch for GA announcement)
- **Fallback 1:** openrouter/google/gemini-3.5-flash (updated from deprecated 2.5-flash — June 17, 2026)
- **Fallback 2:** openrouter/anthropic/claude-3.5-haiku (stable)
- **Platform:** OpenClaw 2026.3.22 (target: 2026.6.6 — upgrade pending Josh action)
- **Note:** Check model fallback currency periodically. Google deprecates flash models every 6–9 months.
```

---

## Implementation Priority (All Documents Combined)

| Priority | Rec | File | Effort | Blocked? |
|----------|-----|------|--------|---------|
| ⛔ URGENT | openclaw.json fallback fix | openclaw.json | 30 sec | No |
| 1 | Jun 13 Rec 1: Create MEMORY.md | workspace/MEMORY.md | 2 min | No |
| 2 | Jun 13 Rec 2: Populate HEARTBEAT.md | workspace/HEARTBEAT.md | 5 min | No |
| 3 | Jun 13 Rec 3: SOUL.md executive section | workspace/SOUL.md | 5 min | No |
| 4 | Jun 15 Rec 6: SOUL.md error recovery | workspace/SOUL.md | 3 min | No |
| 5 | Jun 15 Rec 7: SOUL.md first-week guidance | workspace/SOUL.md | 3 min | No |
| 6 | Rec 10: SOUL.md gateway awareness | workspace/SOUL.md | 2 min | No |
| 7 | Rec 11: SOUL.md stale connection hygiene | workspace/SOUL.md | 2 min | No |
| 8 | Jun 15 Rec 8: AGENTS.md emoji fix at source | workspace/AGENTS.md | 2 min | No |
| 9 | Rec 12: AGENTS.md session startup check | workspace/AGENTS.md | 2 min | No |
| 10 | Rec 13: MEMORY.md model config section | workspace/MEMORY.md | 1 min | Needs Rec 1 first |
| 11 | Jun 13 Rec 4: Fix inbox-state.json | workspace/memory/inbox-state.json | 2 min | No |
| 12 | Delete BOOTSTRAP.md | workspace/BOOTSTRAP.md | 30 sec | No |
| 13 (VPS) | Connect Google account | AlphaClaw Setup UI | ~30 min | Needs Josh action |
| 14 (VPS) | Upgrade to 2026.6.6 | VPS + AlphaClaw | ~30 min | Needs Josh action |
| 15 (VPS) | Enable Discord streaming | openclaw.json | 5 min | Needs upgrade first |

**Items 1–12 require zero VPS access and can be done from GitHub UI right now.**

The 30-second openclaw.json fallback fix (the ⛔ URGENT item) is a config change, not a soul change, but is included here for completeness since today is the last day before the June 17 deadline.
