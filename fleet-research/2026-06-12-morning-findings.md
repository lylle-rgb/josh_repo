# Morning Scan — 2026-06-12

**Instance:** Josh / Heather Schwartz  
**Scan time:** Morning  
**Version:** 2026.3.22 (latest: 2026.6.5)  
**Researcher:** AlphaClaw Fleet Agent

---

## New Findings Today

### 1. Dreaming (Memory Consolidation) Not Enabled — HIGH

OpenClaw's opt-in background memory consolidation feature ("Dreaming") is not configured on this instance. It runs nightly, scores recent daily memory entries for significance, and promotes only high-signal items into MEMORY.md automatically.

This is exactly the behavior described in Heather's AGENTS.md ("periodically review memory files, update MEMORY.md with distilled learnings") — but it relies on Heather spending a manual heartbeat turn on it. Dreaming automates it completely.

For a personal assistant whose core value is knowing Josh over time, this is the infrastructure that makes long-term context sustainable. See Finding 7 in findings.md for full action steps.

### 2. HEARTBEAT.md Not Populated — MEDIUM

HEARTBEAT.md is empty. Heather therefore replies `HEARTBEAT_OK` on every heartbeat poll without doing any proactive checks. The entire proactive assistant stack (email alerts, calendar reminders, weather, mentions) is dormant.

This is a 5-minute file edit that unlocks core personal assistant behavior. See Finding 8 in findings.md.

---

## Version Check
- Latest stable: **2026.6.5** (no new release since yesterday)
- Latest beta: **2026.6.6-beta.1** (June 10 — not yet promoted)

## Unresolved from Yesterday
- ⏳ Version 2026.3.22 — 3 months behind (HIGH)
- ⏳ Google Workspace not connected (CRITICAL)
- ⏳ No compaction / memoryFlush in config (HIGH)
- ⏳ Discord open to all DMs and groups (MEDIUM security risk)
- ⏳ No MEMORY.md exists
- ⏳ TOOLS.md is blank template
- ⏳ Discord streaming disabled
