---
name: "hexstrike-ai"
categories:
  - misc
version: "0.0~git20250919.3326704-0parrot1"
homepage: "https://github.com/0x4m4/hexstrike-ai"
origin: "parrot"
recipe: "parrot-repo"
packaged-by: [parrot]
build-system: python
meta-checked: "2026-11-09"
github-repo: "0x4m4/hexstrike-ai"
github-stars: 13412
github-pushed: "2026-08-03"
github-archived: false
github-issues: 132
github-match: "exact"
gh-checked: "2026-11-07"
worth: 6.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 3
gh-activity-checked: "2026-11-09"
---

# hexstrike-ai

> MCP server that lets AI clients plan and run offensive-security tooling as callable tools.

*No corpus evidence — derived from upstream documentation.*

## What it's for

HexStrike AI (by 0x4m4) is an AI-powered cybersecurity automation platform shipped as an MCP (Model Context Protocol) server. MCP-compatible AI clients — Claude Desktop, Cursor, VS Code Copilot, Roo Code — connect to it and gain dozens of tools that wrap common security utilities (nmap, masscan, rustscan, ffuf, sqlmap, hashcat, ghidra, radare2, trivy, prowler and many more), plus an intelligence layer that analyzes a target, selects suitable tools and parameters, executes them and adapts to results. It is an orchestration layer, not a toolkit: the underlying utilities must be installed separately. The server also exposes plain REST endpoints (health, telemetry, target analysis, tool selection, process management) so parts of it are scriptable without any AI client.

## Typical use cases

### Start the MCP server

Run the server an AI client will connect to.

```
python3 hexstrike_server.py
```

Outcome: the server starts and registers its MCP tool set; an AI client configured with this launch command (stdio transport) can immediately call the security tools.

### Run with a custom port or debug logging

Adjust the HTTP API listener or get verbose logs while wiring a client up.

```
python3 hexstrike_server.py --port 8888 --debug
```

Outcome: the server comes up with its HTTP API on the chosen port and emits debug-level logs for tool registration and every request — the combination to reach for when an integration misbehaves.

### Check server health and tool availability

Verify the stack is up before handing it to an AI client.

```
curl http://localhost:8888/health
```

Outcome: a health response reporting server state and which of the backing security tools are actually installed — the quick way to see that required tools are missing before an agent trips over them.

### Probe the intelligence layer directly over REST

Exercise the target-analysis engine without an MCP client.

```
curl -X POST http://localhost:8888/api/intelligence/analyze-target \
  -H "Content-Type: application/json" \
  -d '{"target": "example.com", "analysis_type": "comprehensive"}'
```

Outcome: returns an AI-generated analysis of the target with recommended tools and testing strategy — the same decision layer the MCP agents consult to pick their next move.
