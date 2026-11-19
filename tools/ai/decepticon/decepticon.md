---
name: "decepticon"
categories:
  - ai
version: "1.5.4"
homepage: "https://decepticon.red"
origin: "kitploit"
recipe: "upstream"
packaged-by: []
build-system: python
license: Apache-2.0
meta-checked: "2026-11-09"
github-repo: "BitterSecurity/Decepticon"
github-stars: 6578
github-pushed: "2026-11-10"
github-archived: false
github-issues: 1
github-match: "exact"
gh-checked: "2026-11-09"
worth: 8.3
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 1998
gh-activity-checked: "2026-11-09"
---

# decepticon

> Autonomous red-team agent that plans and executes full attack chains — recon, exploitation, privesc, lateral movement, C2 — in an isolated Docker sandbox, under operator-approved targets and RoE.

*No corpus evidence — derived from upstream documentation.*

## What it's for

Running an end-to-end offensive engagement with LLM agents instead of a checklist scanner: 16 specialist agents organized by kill-chain phase (reconnaissance, exploitation, post-exploitation, vulnerability research, plus domain specialists for AD, cloud, smart contracts and reversing) work an OPPLAN toward objectives the way an adversary would — pivoting, adapting, chaining techniques. Everything executes inside a Kali sandbox on a dedicated operational network (`sandbox-net`), separate from the management plane; interactive tools (msfconsole, sliver-client, evil-winrm) run in persistent tmux sessions with prompt detection, so the agent can drive their interactive prompts. Every engagement starts from an operator-approved target and rules of engagement — it is authorization-gated by design, and it is a platform (Docker stack + CLI), not a single script. Similar agents live in [[pentestagent]], [[pentest-copilot]] and [[medusa-ai]].

## Typical use cases

### Onboard providers and model profiles

```
decepticon onboard
```

Outcome: interactive wizard collects your LLM provider, API key (or subscription OAuth: Claude Max/Pro, ChatGPT Pro/Plus, Gemini Advanced, ...) and a model profile — `eco` (per-agent tier mix, default), `max` (everything high-tier) or `test` (low-tier, for CI) — and builds the primary-to-fallback provider chain per agent tier.

### Start an engagement and drive it from the CLI

```
decepticon
```

Outcome: the core management plane (LiteLLM, PostgreSQL, Neo4j, LangGraph) and the sandbox come up, and you land in the terminal CLI in red mode. Type `/` to browse commands (`/status`, `/model`, `/sessions`, `/resume`), `/mode red` or `/mode blue` to switch between attack and investigation, and give the agent its approved target and objectives.

### Plan an attack before executing

```
/plan
```

Outcome: enters optional Plan mode — the planning discussion plus seven planning documents are produced alongside the read-only RoE before any execution; the execution orchestrator then owns the OPPLAN (Plan is not required for red engagements).

### Hand the platform to a coding agent over MCP

```
claude mcp add --scope user decepticon -- decepticon mcp serve
decepticon skill install
```

Outcome: Decepticon's MCP server is registered in Claude Code (or `codex mcp add decepticon -- decepticon mcp serve` for Codex) and the operator guide skill is installed into both agents — the engagement's agents and tools become callable from your coding-agent workflow.

### Bring up the dashboard and specialist workloads on demand

```
/web
```

Outcome: the web dashboard spawns from inside the CLI while the engagement keeps running; specialist workloads (BloodHound CE, Sliver C2, REA/Ghidra reversing) are likewise spawned by the orchestrator only when needed via `ops_start(...)` — keeping idle resource use flat across a long engagement.
