---
name: "rea"
categories:
  - reversing
homepage: "https://rea.tools/"
origin: "hacktricks"
recipe: "upstream"
packaged-by: []
build-system: node
license: MIT
meta-checked: "2026-11-13"
github-repo: "morluto/rea"
github-match: "exact"
github-stars: 456660
gh-checked: "2026-11-13"
worth: 7.0
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-13"
---

# rea

> One MCP server + CLI that lets an agent reverse engineer anything — native binaries, Electron/JS apps, APKs, firmware, pcaps — returning findings with evidence.

## What it's for

REA ("Reverse Engineer Anything") connects an AI agent (or your terminal)
to a broad toolbox for inspecting software without source: native binaries
(pseudocode, assembly, strings, xrefs via Hopper/Ghidra/IDA/Kuna),
JavaScript/Electron and .NET applications, Android APKs (headless JADX),
Flutter/IL2CPP/PyInstaller artifacts, GPU code objects, firmware images
(binwalk/unblob), packet captures, SQLite databases and recorded process
behavior. The point is the workflow, not any single engine: you ask "how
does feature X in this app work?", the agent drives the right tools
through REA, and answers come back with evidence and stated limitations.
Static analysis runs locally; deep native analysis rides on an existing
Hopper/Ghidra/IDA/Kuna installation (setup can install Hopper with
approval), while JS, .NET and many format inspections are built in. It
does not replace a decompiler — it orchestrates them and records the
evidence trail.

*No corpus evidence — derived from upstream documentation.

## Typical use cases

### Register REA with your coding agent

One-time MCP setup.

```
npx rea-agents setup
```

Outcome: adds REA's MCP server and matching workflow instructions to the
agents you choose (Claude Code, Codex, Cursor, Gemini CLI, ...; existing
config is backed up) — after restarting the agent it can call the full
tool catalog, from `open_binary` to `inspect_apk_signature`.

### Statically analyze an Electron/JavaScript app

Map an app's internals without running it.

```
npx -y rea-agents@latest analyze-javascript-application /absolute/path/to/app --json
```

Outcome: walks the extracted app directory or ASAR and reports modules,
imports, source maps, routes, IPC channels, Electron main/preload
boundaries and native add-ons — each with file-level evidence; no native
analysis engine required.

### Install the standalone CLI

Use REA from the terminal without an agent.

```
npm install --global rea-agents
rea --help
```

Outcome: installs the `rea` command exposing the same workflows as the
MCP tools (`rea update` keeps it current; `rea --update` is the verified
alias). Native-binary commands need a configured provider — Hopper,
Ghidra, IDA, Kuna, or caller-supplied objdump/Rizin — selected per the
CLI guide.

### Ask an agent to reconstruct a feature

The intended interaction shape.

```
Understand how search works in the Notes app, show me the evidence, and build a similar feature for my project.
```

Outcome: the agent inspects the target (desktop install → candidate
binaries → pseudocode/strings/xrefs via the provider), reports how the
feature works with the underlying evidence and limitations, and writes an
implementation — the showcase case studies (DX-Ball sound-pan, Notion
clipboard bridge) reconstruct logic this way.

### Turn a recorded web session into an RL environment

Reuse captured traffic as an agent training env.

```
rea environment-build ./session.har ./my-env --name my-env --json
```

Outcome: converts a HAR file of recorded API requests/responses into a
runnable environment (tools built from the recording, changes allowed
only where the recording confirms them) exported with a start-up test —
run it with `agent-env run ./my-env` (requires AgentEnv + Docker).

### Audit an Electron release before shipping

Check what a packaged app exposes.

```
rea audit-release-exposure ./release
```

Outcome: inspects a built JavaScript/Electron release for exposed
modules, source maps and artifact boundaries — the pre-ship counterpart
to the application analysis, run against the packaged output.
