---
name: "mcp-gamehacking"
categories:
  - ai
version: "git (2026-11-02)"
homepage: "https://github.com/1llu50ry/mcp-gamehacking"
origin: "kitploit"
recipe: "upstream"
packaged-by: []
build-system: node
meta-checked: "2026-11-09"
github-repo: "1llu50ry/mcp-gamehacking"
github-stars: 561
github-pushed: "2026-11-08"
github-archived: false
github-match: "exact"
gh-checked: "2026-11-09"
github-commits-year: 65
gh-activity-checked: "2026-11-09"
worth: 7.0
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
license: see repository
github-issues: 8
---

# mcp-gamehacking

> mcp-reverse — an MCP server that turns Claude, Codex or Cursor into a reverse-engineering team: 180+ RE/debug tools, 56 backends, 4,200+ skill archives and bundled emulators behind a Model Context Protocol endpoint.

*No corpus evidence — derived from upstream documentation.*

## What it's for

Giving an LLM client hands for reverse engineering. Instead of pasting disassembly into a chat window, you run this MCP server and your agent directly drives real RE backends — debuggers, disassemblers, Frida dynamic analysis, x86-64 and ARM64 emulators, bindiff, ROP tooling — plus a curated knowledge base of thousands of skill archives with source code. Game hacking is the marquee use case (the name says so), but the toolchain is general binary RE. Multi-agent meshes let several agents divide the work. It is the infrastructure layer; the judgment still comes from the driving model.

## Typical use cases

### Wire the server into an MCP client

```
git clone https://github.com/1llu50ry/mcp-reverse && cd mcp-reverse
npm install && npx tsc
```

Then add to the client's MCP config (stdio mode):

```
{ "mcp-reverse": { "command": "node", "args": ["/path/to/dist/index.js"] } }
```

Outcome: after restarting the client, the RE toolset appears as callable tools — the agent can enumerate processes, attach debuggers, run emulated code and pull skill references on demand. Run `npm test` after building to confirm the tool surface is intact before wiring it in.
