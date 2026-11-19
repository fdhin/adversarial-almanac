---
name: "frida-c2-mcp"
categories:
  - backdoor
version: "git (2026-03-18)"
homepage: "https://github.com/s4dp4nd4/frida-c2-mcp"
origin: "kitploit"
recipe: "upstream"
packaged-by: []
build-system: python
meta-checked: "2026-11-09"
github-repo: "s4dp4nd4/frida-c2-mcp"
github-stars: 70
github-pushed: "2026-03-18"
github-archived: false
github-match: "exact"
gh-checked: "2026-11-09"
github-commits-year: 8
gh-activity-checked: "2026-11-09"
worth: 5.7
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
license: see repository
github-issues: 0
---

# frida-c2-mcp

> MCP server over streamable HTTP that runs Frida entirely on a rooted Android or jailbroken iOS device — any MCP client on the network gets process control and script injection with zero local Frida tooling.

*No corpus evidence — derived from upstream documentation.*

## What it's for

Letting an AI agent (or a human with an MCP client) drive dynamic instrumentation on a phone without a USB cable or a local Frida install. The server runs on-device (Termux on Android, rootless jailbreak on iOS), bundles Frida scripts on the fly (esbuild on Android, frida-compile with the ObjC bridge on iOS), and exposes tools for listing apps and processes, starting and terminating apps, attaching sessions, and executing Frida JavaScript — one-shot or persistent hooks. The intended loop is agent-driven mobile pentesting: spawn the target, hook root/jailbreak detection or SSL pinning, read results, iterate; several phones can each run a server and be orchestrated from one agent. Despite the name it is an instrumentation bridge, not a post-exploitation implant framework — and it ships with no authentication or encryption, so it belongs on an isolated lab network only.

## Typical use cases

### Register the on-device server with Gemini CLI

```
gemini mcp add --transport http frida-c2-mcp http://198.51.100.10:6767/mcp
```

Outcome: adds the phone's MCP endpoint (substitute the device's LAN address) as a remote HTTP server; after the client restarts, the agent can enumerate apps and processes, start the target, attach a Frida session and inject scripts through ordinary MCP tool calls.

### Point any JSON-configured MCP client at the device

```
{ "mcpServers": { "frida-c2-mcp": { "type": "http", "url": "http://198.51.100.10:6767/mcp" } } }
```

Outcome: clients that read the standard `mcpServers` config (Claude Code, 5ire and similar) gain the same on-device Frida toolset after a restart, without any Frida packages installed client-side.
