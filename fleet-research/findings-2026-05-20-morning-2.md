# Fleet Research — Josh / Heather Schwartz — Morning Scan 2

**Scan Date:** 2026-05-20 (Morning-2 — Day 33)
**Agent:** AlphaClaw Apex Fleet Research Agent
**Instance:** Josh / Heather Schwartz — Discord bot personal assistant (iMessage, email, calendar, contacts)
**OpenClaw Version:** 2026.3.22 (meta.lastTouchedVersion) — 21+ stable releases behind 2026.5.18
**Previous Findings:** findings-2026-05-20-morning.md (Day 33 Morning-1, Findings 1–86)
**Cumulative Open Findings:** 86 (7 new this scan, 0 resolved)

---

## Platform News — New Since Morning Scan 1

| Item | Detail |
|---|---|
| **defineToolPlugin + plugin CLI now fully documented** | `openclaw plugins build`, `validate`, and `init` commands confirmed in 2026.5.18. Scaffold, compile, and validate typed simple tool plugins with generated manifest metadata — no raw boilerplate. Directly relevant to Josh's future Google Workspace native tools integration (Finding 67). |
| **OPENCLAW_IMAGE_APT_PACKAGES is the new Docker build arg** | 2026.5.18 renames the Docker image build arg to `OPENCLAW_IMAGE_APT_PACKAGES` (runtime-neutral for Docker/Podman). `OPENCLAW_DOCKER_APT_PACKAGES` remains as a legacy fallback. Relevant for Josh's Hetzner VPS if the image is customized. |
| **Gateway startup latency overlap confirmed** | 2026.5.18 overlaps startup logging with plugin-service startup, reducing restart ready latency. Josh's 2026.3.22 lacks this — long restart times after config changes will improve meaningfully post-upgrade. |
| **OpenTelemetry now covers context assembly and memory pressure** | Previously: model calls, token usage, tool loops. Now also: context assembly timing and memory pressure events. Post-upgrade, Heather's high-context sessions (email + calendar + iMessage in one turn) are fully observable. |
| **Active Memory CLI commands documented** | `openclaw memory status --deep` shows plugin load state, index health, embedding provider. `openclaw memory index --force` rebuilds the semantic index. Available post-upgrade once memory-core is active. |
| **AlphaClaw 0.8.0: Chrome DevTools MCP confirmed** | AlphaClaw 0.8.0 added Chrome DevTools MCP: control your Mac via OpenClaw from any VPS using Chrome's DevTools Protocol. For Josh's BlueBubbles iMessage setup on Mac: Heather could interact with Mac desktop apps from the Hetzner VPS — enabling workflows blocked by the current iMessage pause (Finding 49/57). |
| **Python debugging skill bundled in 2026.5.18** | Supports pdb, breakpoint(), post-mortem inspection, and debugpy remote attach. Low relevance for a personal assistant today, but available post-upgrade at zero configuration cost. |

---

## New Findings — Morning Scan 2 (87–93)

---

### Finding 87 — defineToolPlugin CLI: Google Workspace Native Tools Now Have a Concrete Build Path (LOW/Opportunity)

**Risk:** LOW (opportunity — build tooling now documented)
**Days Pending:** 0 (new — confirmed in 2026.5.18 changelog)

**Description:**
OpenClaw 2026.5.18 ships `defineToolPlugin` with full CLI tooling:
- `openclaw plugins init` — scaffold a new typed tool plugin
- `openclaw plugins build` — compile and bundle with generated manifest metadata
- `openclaw plugins validate` — validate plugin structure before install

Previously (Finding 67): the path to Google Workspace as native agent tools was conceptual — `defineToolPlugin` existed but documentation was sparse and build tooling was manual. The 2026.5.18 CLI workflow makes this concrete and documented.

**For Heather specifically:** The Google Workspace integration Josh needs (Gmail, Calendar, Contacts) could be delivered as an OAuth-native plugin — authenticate Google via the OpenClaw OAuth flow, expose Gmail/Calendar/Contacts as native typed tools that Heather calls by name. The `openclaw plugins validate` step catches malformed manifests before they break the runtime.

**Implementation path (post-upgrade + Google account connected):**
1. Upgrade to 2026.5.18
2. Connect Google account (Finding 56 — still unresolved, Day 33)
3. Run `openclaw plugins init` on Hetzner VPS
4. Expose Gmail, Calendar, Contacts as typed tools in the scaffold
5. Run `openclaw plugins validate` before enabling in `plugins.entries`

**Why still LOW risk:** Josh hasn't connected the Google account yet (Day 33). The plugin CLI is meaningless until the foundational OAuth step (Finding 56) is complete. This finding documents the build path for when it matters.

**Risk Assessment:** LOW opportunity. Zero risk. Significantly concretizes the Google Workspace tooling path.

---

### Finding 88 — OPENCLAW_IMAGE_APT_PACKAGES: Docker Build Arg Rename for Hetzner VPS (LOW/Operational)

**Risk:** LOW (operational — Hetzner VPS Docker deployment awareness)
**Days Pending:** 0 (new — confirmed in 2026.5.18)

**Description:**
OpenClaw 2026.5.18 introduces `OPENCLAW_IMAGE_APT_PACKAGES` as the runtime-neutral Docker/Podman image build arg. `OPENCLAW_DOCKER_APT_PACKAGES` is kept as a legacy fallback — no breaking change.

For Josh's Hetzner VPS (5.78.142.81): if any custom Docker build scripts or AlphaClaw Compose overrides reference `OPENCLAW_DOCKER_APT_PACKAGES`, they will continue to work after the upgrade. Any new AlphaClaw documentation and tooling going forward will use the new name.

**Action:** No immediate action required. After upgrading to 2026.5.18, check if custom Docker build scripts reference the old variable name and update for future compatibility. Mention in TOOLS.md if relevant to Josh's Hetzner setup.

**Risk Assessment:** LOW. No breaking change. Pure awareness item.

---

### Finding 89 — Gateway Restart Latency: Post-Upgrade Improvement Bundled With 2026.5.18 (LOW/Operational)

**Risk:** LOW (quality-of-life improvement, bundled with upgrade)
**Days Pending:** 0 (new — confirmed in 2026.5.18)

**Description:**
OpenClaw 2026.5.18 overlaps startup logging with plugin-service startup, reducing the time from restart triggered to Gateway ready. Startup traces now attribute cost per phase (startup probe, config, runtime, resource count) for diagnostics.

For Josh: on 2026.3.22, every time `openclaw.json` is changed (adding contextPruning, fixing the fallback model, etc.), the Gateway needs a reload. Post-upgrade, config-change reloads will be faster. On a personal assistant that may restart mid-conversation when settings are adjusted, reduced restart latency means less dead time for Josh.

**Action:** Bundled with the 2026.5.18 upgrade (Finding 72). No separate action.

**Risk Assessment:** LOW. Passive quality-of-life improvement.

---

### Finding 90 — Full-Stack OpenTelemetry: Monitor Heather's Session Health Post-Upgrade (MEDIUM/Opportunity)

**Risk:** MEDIUM (opportunity — actionable post-upgrade)
**Days Pending:** 0 (new — confirmed this research pass)

**Description:**
OpenClaw 2026.5.18 extends OpenTelemetry coverage to context assembly and memory pressure — completing full-stack observability. For Josh's personal assistant pattern (email + calendar + iMessage in a single heartbeat session):

**Context assembly timing:** If a morning heartbeat takes 20+ seconds before first response, telemetry shows whether context loading is the bottleneck (large memory files, slow bootstrap) vs model call latency.

**Memory pressure events:** When sessions approach the compaction threshold (Josh currently has no compaction config — Finding 80), OpenTelemetry will surface this before it causes a silent reset. Post contextPruning (Finding 80 — 35m TTL), traces confirm the pruning is working as intended.

OpenClaw supports Prometheus, OpenTelemetry OTLP, and StatsD exports natively. Minimum setup for Hetzner VPS:

```json
"telemetry": {
  "enabled": true,
  "exporters": [{"type": "prometheus", "port": 9090}]
}
```

Scrape `/metrics` from Grafana, or use a free OTLP sink (Signoz, Uptrace) for hosted dashboards.

**Action (post-upgrade):** Add telemetry block to openclaw.json. Monitor context assembly time and memory pressure events during morning heartbeat sessions. Becomes most useful once memory-core and contextPruning are active — provides verification that configuration changes are working.

**Risk Assessment:** MEDIUM opportunity. Low friction to enable. High value for confirming memory and context configuration is working correctly.

---

### Finding 91 — Active Memory CLI Commands: Post-Upgrade Memory Management (LOW/Reference)

**Risk:** LOW (reference — post-upgrade only)
**Days Pending:** 0 (new — documented in Active Memory plugin docs)

**Description:**
Once memory-core and active-memory plugins are configured post-upgrade, the following CLI commands are available on the Hetzner VPS:

```bash
# Check memory plugin health, index status, embedding provider in use
openclaw memory status --deep

# Force-rebuild the semantic index (e.g., after adding memory log files)
openclaw memory index --force
```

**When these matter for Heather:**
- After the first memory logs are written (`workspace/memory/2026-05-20.md` etc.), `memory index --force` bootstraps the semantic index on those files
- `memory status --deep` confirms Gemini embeddings are auto-selected (using Josh's `google:default` key — Finding 84)
- If Heather gives unexpected memory responses, `--deep` status is the first diagnostic

**Action:** Document in TOOLS.md post-upgrade: "Memory management: `openclaw memory status --deep`, `openclaw memory index --force`"

**Risk Assessment:** LOW reference. No action needed until memory-core is active.

---

### Finding 92 — AlphaClaw Chrome DevTools MCP: Mac Control From Hetzner VPS (MEDIUM/Opportunity)

**Risk:** MEDIUM (opportunity — significant for iMessage integration path)
**Days Pending:** 0 (AlphaClaw 0.8.0 — confirmed from X/Twitter community research)

**Description:**
AlphaClaw 0.8.0 adds a Chrome DevTools MCP integration that lets OpenClaw control a Mac via Chrome's DevTools Protocol from a remote VPS. Source: [@chrysb on X](https://x.com/chrysb/status/2032943853012136120) — "control your mac via @openclaw from any VPS using Chrome's new DevTools MCP."

**For Josh's iMessage setup:**
Josh's iMessage integration uses BlueBubbles (a Mac-side app that exposes iMessage via API). Heather on the Hetzner VPS connects to BlueBubbles via its API. If the API goes down or BlueBubbles needs a GUI interaction (accepting macOS permissions, handling system prompts), Heather cannot act remotely.

With AlphaClaw Chrome DevTools MCP enabled:
- Heather could interact with Mac browser and Electron apps (like BlueBubbles) via DevTools automation from the VPS
- System prompts, permission dialogs, and GUI-required actions become automatable remotely
- Broader cross-device file and browser automation for deeper iMessage/Mac integration workflows

**Implementation requirements:**
1. Verify Josh's Mac has Chrome installed (likely)
2. AlphaClaw 0.8.0+ (target is 0.9.16 per Finding 66 — 0.8.0 is already past that)
3. Enable Chrome DevTools MCP in AlphaClaw control UI
4. Configure which URLs and apps Heather is permitted to interact with (security scoping — important for privacy)

**Risk Assessment:** MEDIUM opportunity. Addresses the remote Mac interaction gap in Josh's iMessage integration. Chrome DevTools MCP should be scoped tightly to prevent unintended access to other Mac applications.

---

### Finding 93 — Day 33 Morning-2 Escalation (CRITICAL/Summary)

**Risk:** CRITICAL
**Days Pending:** 33

**Description:**
Second research pass of Day 33. All critical findings from Morning Scan 1 remain open. Seven net-new findings documented (87–93). Zero implementations confirmed across 33 consecutive days.

**The 18-minute queue from Morning Scan 1 — unchanged, still Day 33:**
1. Delete BOOTSTRAP.md — 30 seconds
2. Fix retired fallback model (`claude-3.5-haiku` → `claude-haiku-4-5`) — 3 minutes
3. Start daily memory log (`workspace/memory/2026-05-20.md`) — 5 minutes
4. Add contextPruning 35m — 2 minutes

**Highest-value new findings from this pass:**
- **Finding 92 (AlphaClaw Chrome DevTools MCP):** Addresses the remote Mac interaction gap for iMessage. Most immediately relevant to the iMessage pause (Finding 49/57).
- **Finding 87 (defineToolPlugin CLI):** Concretizes the Google Workspace tooling path. Ready to act on once Finding 56 (Google account) is resolved.
- **Finding 90 (Full-stack OTel):** Provides a verification layer for confirming contextPruning and memory-core are functioning correctly post-upgrade.

**Risk Assessment:** CRITICAL — systemic, 33 days, zero implementations.

---

## Persistent Findings Status Table — Day 33 Morning-2

| # | Title | Risk | Days Open |
|---|---|---|---|
| 48/56 | Google account never connected | CRITICAL | 33 |
| 49/57 | inbox-state.json invalid + iMessage paused | HIGH | 6 |
| 50/78 | No MEMORY.md / no memory logs — 33 sessions | CRITICAL | 33 |
| 52 | No active heartbeat | MEDIUM | Unknown |
| 53/59 | Retired fallback model (claude-3.5-haiku) | MEDIUM | 6 |
| 54/61/72 | 21+ releases behind stable (2026.5.18) | MEDIUM | 58+ |
| 55/60 | SOUL.md no-emoji rule absent | MEDIUM | 6 |
| 62/69/77 | BOOTSTRAP.md not deleted — Day 33 | CRITICAL | 33 |
| 64 | TOOLS.md unpopulated | LOW | 3 |
| 66 | AlphaClaw 0.9.16 unverified | MEDIUM | 2 |
| 67 | defineToolPlugin — Google Workspace native tools | LOW | 2 |
| 68 | Grok OAuth now stable — social monitoring | LOW | 2 |
| 73 | Active Memory allowedChatIds scope | MEDIUM | 1 |
| 74 | ElevenLabs v3 / Gemini TTS now stable | LOW | 1 |
| 75 | Node.js 22.19 minimum — pre-upgrade check | MEDIUM | 1 |
| 76 | AlphaClaw OPENCLAW_STATE_DIR durable state | LOW | 1 |
| 79 | Cron --wait + Active Memory now stable | LOW | 1 |
| 80 | contextPruning absent — add 35m cache-ttl | MEDIUM | 0 (Morning-1) |
| 81 | Gemini-native TTS (no ElevenLabs needed) | LOW | 0 |
| 82 | File transfer plugin — iMessage attachments | LOW | 0 |
| 83 | Docker security hardening in 2026.5.18 | LOW | 0 |
| 84 | Gemini semantic memory auto-select | LOW | 0 |
| 85 | gog-cli missing from Josh vs Noah | LOW | 0 |
| 86 | Day 33 Morning-1 escalation | CRITICAL | 0 |
| 87 | defineToolPlugin CLI (plugins build/validate/init) | LOW | 0 |
| 88 | OPENCLAW_IMAGE_APT_PACKAGES Docker rename | LOW | 0 |
| 89 | Gateway restart latency improvement | LOW | 0 |
| 90 | Full-stack OTel (context assembly + memory pressure) | MEDIUM | 0 |
| 91 | Active Memory CLI (memory status --deep, index --force) | LOW | 0 |
| 92 | AlphaClaw Chrome DevTools MCP — Mac control from VPS | MEDIUM | 0 |
| 93 | Day 33 Morning-2 escalation | CRITICAL | 0 |

**Open: 93 | Resolved: 0 | Critical: 4 | High: 6+ | Medium: 14+ | Low: 14+**

---

*Generated by AlphaClaw Apex Fleet Research Agent — Morning Scan 2 — 2026-05-20 (Day 33)*
