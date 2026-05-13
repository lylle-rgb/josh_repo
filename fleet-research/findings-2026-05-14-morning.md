# Fleet Research — Josh / Heather Schwartz — Morning Scan

**Scan Date:** 2026-05-14 (Morning)  
**Agent:** AlphaClaw Apex Fleet Research Agent  
**Instance:** Josh — Discord bot Heather Schwartz (personal assistant: iMessage, email, calendar, contacts)  
**Previous findings:** `findings-2026-05-13-evening.md` (Day 26 Evening). All prior findings remain unresolved.

---

## Platform News (New Since May 13 Evening Scan)

### v2026.5.10 Stable — No New Release Overnight

**Current stable remains 2026.5.7.** No new stable released overnight. Beta series is at v2026.5.12-beta.3. The leapfrog to 5.12 betas signals the team considers 5.10 ready; stable release is expected before end of this week. Josh's version gap remains **13 releases** (2026.3.22 → 2026.5.7).

---

### memory-lancedb-pro — Enhanced Memory Plugin Discovered

Morning research surfaces a community-enhanced memory plugin: **memory-lancedb-pro** (CortexReach fork). It is a drop-in replacement for the bundled `memory-core` plugin that provides significantly better retrieval for long-running agents that accumulate months of context.

**Capabilities not in standard memory-core:**
- **Hybrid retrieval:** Vector similarity + BM25 keyword matching — not just semantic embedding distance
- **Cross-encoder reranking:** Re-scores retrieval candidates by actual relevance, not just proximity in embedding space
- **Multi-scope isolation:** Per-channel, per-session, and global memory scopes — keeps Discord conversations separate from email context
- **Management CLI:** `memory prune`, `memory list`, `memory export` — lets Heather self-audit what's in her memory

**Why it matters for Heather specifically:** A 24/7 personal assistant accumulates memories across iMessage threads, email chains, calendar patterns, and contact notes. Pure vector similarity can surface two contextually different emails with similar phrasing and rank them equally. Hybrid BM25 + vector correctly weights keyword matches — Josh's name, specific contact names, project codenames, subject lines — against semantic similarity. The result is more accurate pre-reply recall when drafting emails or reviewing calendar context.

**Recommended path:**
1. First, install and activate the standard `memory-core` plugin (Finding 35 — pending 27 days)
2. Let memory-core run for a few sessions to build an initial corpus from daily memory files
3. Evaluate upgrading to `memory-lancedb-pro` once the memory scaffold is confirmed working

**Do not skip step 1.** Upgrading before memory-core is even configured would start from a blank slate either way.

**Risk level:** MEDIUM for the upgrade — it replaces the memory backend entirely. LOW as a future consideration.

---

### X/Twitter Community Insight — Config Loop Failure Mode Is a Known Problem With a Known Fix

An X post from user @tipheret (Piotr Gnyp) documents exactly the failure mode Heather is exhibiting after 27 days:

> "My bot kept forgetting our conversations. Losing skills. Forgetting keys. Felt like we were stuck in the same config loop every single day. More fixing than using."

**The documented community resolution to this pattern:**
1. Bootstrap memory manually with a live Discord session — write a first `memory/YYYY-MM-DD.md` by hand
2. Add a memory checkpoint to `HEARTBEAT.md` so the agent writes daily notes automatically going forward
3. Only then activate plugins like memory-core — without a baseline corpus, plugins have nothing to index and recall is meaningless

**This directly validates Finding 36** (workspace/memory empty — root cause confirmed). The config loop that Heather is in — resetting context each session, losing prior decisions, not knowing Josh's preferences — persists indefinitely without one human-initiated memory bootstrap. No amount of plugin configuration or OpenClaw updates fixes a memory system with zero content.

**Recommended message to send Heather in Discord today (zero config, highest leverage action available):**
```
I want to fix your memory so you stop starting fresh every session. Right now:
1. Create memory/2026-05-14.md — write everything you know about Josh, your tools, and any open tasks
2. Update HEARTBEAT.md — add: "At the end of each active session, write a short summary note to memory/YYYY-MM-DD.md"
3. Tell me when done
```
**Risk level:** ZERO — this is a Discord message, not a config change. It is the single most impactful action available without technical access.

---

### v2026.5.4 Gateway Startup Performance — Josh Misses This Until Updated

v2026.5.4 shipped lazy-loading of plugins, runtime discovery, cron, schema, sessions, and model metadata at startup. OpenClaw now starts significantly faster and avoids loading all models and plugins eagerly on boot.

**Josh's VPS impact:** At 2026.3.22, every startup loads everything eagerly. After updating to 2026.5.7, cold-boot time drops — important for an agent that runs 24/7 and may restart after a crash. A faster cold-boot means Heather is responsive in Discord sooner after any restart. No action beyond the already-pending update.

---

## New Findings — Josh Instance (Day 27)

### Finding 38. memory-lancedb-pro — Superior Memory Path for Personal Assistant Scale
**Risk: MEDIUM (upgrade) | Days pending: NEW**

The community-enhanced `memory-lancedb-pro` plugin provides meaningfully better recall than bundled `memory-core` for long-lived personal assistants. For Heather's use case — months of iMessage history, ongoing email threads with specific contacts, recurring calendar patterns, and contact-specific preferences — hybrid BM25 + vector retrieval with cross-encoder reranking would surface the correct Josh-specific context where pure vector similarity might retrieve semantically similar but contextually wrong memories.

**Concrete example:** Josh asks Heather to follow up on an email to a specific contact. Standard memory-core might surface any email-related memory with similar phrasing. memory-lancedb-pro's BM25 layer would weight the contact name as a keyword, retrieving the specific prior thread.

**Implementation path:** memory-core first (Finding 35, pending 27 days) → initial corpus built from daily memory files → then evaluate upgrade.
**Risk level:** MEDIUM for upgrade. Don't attempt before memory-core baseline is working.

---

### Finding 39. X/Twitter Community Confirms Config Loop Is Addressable — Now
**Risk: INFO | Days pending: NEW**

Community intelligence confirms: the stateless, config-loop failure mode Heather is exhibiting has a documented cause (no memory bootstrap) and a documented fix (manual memory initialization via a live session). This is not a version gap issue, not a plugin configuration issue, and not waiting on 2026.5.7.

**The critical insight:** The highest-leverage single action available to Josh right now requires no technical access and takes under 2 minutes — send the memory bootstrap message above in Discord. Everything else on the priority list (plugin configs, updates, HEARTBEAT.md) delivers more value once there is memory content to work with.

**Risk level:** ZERO to attempt. HIGH cost of continued delay — Day 28 without memory will be Day 28 of cold-start sessions.

---

### Finding 40. workspace/reports/ Missing — No Summary Archive for Personal Assistant
**Risk: LOW | Days pending: NEW**

Noah's instance has a `workspace/reports/` directory for storing research output. Josh's workspace has no equivalent. For a personal assistant managing email, calendar, iMessage, and contacts, a `reports/` directory would serve as a durable reference archive:

- Email digest summaries — what was reported, when
- Contact research notes — background on people Josh meets or emails
- Calendar prep briefs — pre-meeting context docs
- iMessage thread summaries — important conversation logs for reference

Without this, Heather has nowhere to save reference documents that persist across sessions. Everything stays in memory files (which don't exist yet) or evaporates at session end.

**Zero-config fix (one Discord message):**
```
Create a workspace/reports/ directory. When you write summaries, digests, or research
notes for me, save them there as YYYY-MM-DD-topic.md files for future reference.
```
**Risk level:** LOW — additive directory. No config change.

---

## Persistent High-Priority Items — Day 27 Summary

**Version gap: 2026.3.22 → 2026.5.7 = 13 releases, 84 days. 2026.5.10 stable expected this week.**  
**iMessage monitoring: 19 days dark.**  
**No daily memory files: 27 days.**  
**HEARTBEAT.md: effectively empty — 27 days.**  
**Zero implementations across 27 days of documented research.**

| Finding | Severity | Days Pending | Status |
|---|---|---|---|
| OpenClaw 13 releases outdated | HIGH | 27 | ⬜ Pending |
| memory-core plugin missing entirely | HIGH | 2 | ⬜ Pending |
| workspace/memory empty — no daily logs | HIGH | 27 confirmed | ⬜ Pending |
| iMessage monitoring dark (~April 26) | HIGH | 19 | ⬜ Pending |
| HEARTBEAT.md effectively empty | HIGH | 27 | ⬜ Pending |
| Pre-compaction flush not configured | MEDIUM | 2 | ⬜ Pending |
| MEMORY.md never created | MEDIUM | 27 | ⬜ Pending |
| SOUL.md never evolved | MEDIUM | 27 | ⬜ Pending |
| No-emoji rule not in SOUL.md | MEDIUM | 27 | ⬜ Pending |
| Bootstrap TOOLS.md stale (56 days) | MEDIUM | 27 | ⬜ Pending |
| inbox-state.json malformed + stale | MEDIUM | 27 | ⬜ Pending |
| Retired claude-3.5-haiku fallback | LOW | 9 | ⬜ Pending |
| Discord streaming off | LOW | 27 | ⬜ Pending |
| IDENTITY.md avatar blank | LOW | 2 | ⬜ Pending |
| threadBindings — multi-agent Discord | MEDIUM | 2 | ⬜ Post-update |
| Retry-aware cron — silent fail protection | MEDIUM | 2 | ⬜ Post-heartbeat |
| workspace/reports/ missing | LOW | NEW | ⬜ Pending |
| memory-lancedb-pro upgrade path | LOW | NEW | ⬜ Post-memory-core |
| Config loop bootstrap — do today | INFO | NEW | ⬜ Zero-config action |
| v2026.5.10 stable — monitor | OPPORTUNITY | monitoring | ⬜ Expected this week |
| /context map — token visibility | OPPORTUNITY | 2 | ⬜ Post-5.10 |
| A2A 20-turn sub-agent delegation | OPPORTUNITY | 2 | ⬜ Post-5.10 |
| Per-agent tool overrides (Discord) | OPPORTUNITY | 2 | ⬜ Post-5.10 |

**Updated implementation order:**
1. **TODAY — Zero config:** Send memory bootstrap message to Heather in Discord (Finding 39)
2. Update OpenClaw to 2026.5.7 — unlocks all 5.x features + admin scope for memory-core
3. Add memory-core to `plugins.allow` + `plugins.entries` + compaction config
4. Populate HEARTBEAT.md with email/calendar/iMessage check schedule
5. Tell Heather to create `workspace/reports/` directory
6. Add cron retry config when cron is active
7. Enable threadBindings
8. Fix stale claude-3.5-haiku fallback → `openrouter/anthropic/claude-haiku-4-5`
9. Monitor for 2026.5.10 stable → evaluate memory-lancedb-pro upgrade

---
*Generated by AlphaClaw Apex Fleet Research Agent — Morning Scan — 2026-05-14*
