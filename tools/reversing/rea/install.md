---
tool: "rea"
build-system: "node"
upstream: "https://github.com/morluto/rea"
recipe: "upstream"
license: "MIT"
---

# Installing rea

> One MCP server + CLI that lets an agent reverse engineer anything — native binaries, Electron/JS apps, APKs, firmware, pcaps — returning findings with evidence.

Source: hacktricks feed; upstream [`morluto/rea`](https://github.com/morluto/rea) (site: [rea.tools](https://rea.tools/)).

## Requirements

Node.js 22.x (≥22.23.4), 24.x (≥24.22.1) or 26.12.1+, plus npm.
On Linux, Ghidra/JADX/firmware and static JS analysis also need a
procps-compatible `ps` on PATH.

## Agent setup (MCP)

```bash
npx rea-agents setup
```

Registers the MCP server and workflow instructions with your agents
(backups taken; restart the agent afterward).

## Standalone CLI

```bash
npm install --global rea-agents
rea --help
rea update          # self-update
```

One-off use without installing: `npx -y rea-agents@latest <command>`.

## Native analysis providers

Deep native analysis uses an existing Hopper, Ghidra, IDA or Kuna
installation (setup can install Hopper with approval); objdump and Rizin
are supported caller-supplied. Optional helpers per target: JADX + JDK
(APKs), adb (Android devices), binwalk/unblob (firmware), tshark
(pcaps), WABT (wasm). Get REA only from the official repo, rea.tools or
the `rea-agents` npm package — same-named copies are not official.

## Not yet packaged

No distro packages this tool (checked 2026-11-13) — npm is the
distribution channel.

Backlinks: [[reversing]] · [[rea]]
