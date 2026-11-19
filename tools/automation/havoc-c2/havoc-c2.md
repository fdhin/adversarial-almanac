---
name: "havoc-c2"
categories:
  - blackarch-automation
version: "856.fb67b76"
homepage: "https://havocframework.com/"
packaged-by: [blackarch]
blackarch-builddate: "2024-11-07"
build-system: go
license: GPL3
meta-checked: "2026-11-09"
github-repo: "HavocFramework/Havoc"
github-stars: 8500
github-pushed: "2025-12-18"
github-archived: true
github-issues: 132
github-match: "exact"
gh-checked: "2026-11-09"
worth: 0.6
worth-basis: "historical (archived/gone upstream)"
worth-checked: "2026-11-09"
github-commits-year: 2
gh-activity-checked: "2026-11-09"
---

# havoc-c2

> Modern, malleable post-exploitation C2 framework: Go teamserver, Qt client, and the Demon agent.

## What it's for

Havoc (by C5pider/Cracked5pider, upstream repo HavocFramework/Havoc) is a command-and-control framework with three parts: a Go teamserver (listeners, multiplayer, payload generation as exe/shellcode/dll, customizable C2 profiles, external C2), a Qt C++ client UI, and Demon — a C/ASM implant with sleep obfuscation (Ekko/Zilean/FOLIAGE), indirect syscalls, AMSI/ETW patching, SMB support, and a token vault. It is deliberately modular rather than evasion-focused: the design intent is that operators add modules or agents to suit their target. Usable via its Python API and third-party modules. On BlackArch the framework is installed under `/opt/Havoc` and launched through the `havoc-c2` wrapper (the `./havoc` commands below).

## Typical use cases

### Start the teamserver with a profile

```
./havoc server --profile profiles/havoc.yaotl -v
```

Outcome: the teamserver reads the Yaotl configuration (bind host and port, operator accounts, listener definitions) and starts serving operators and implants; `-v` timestamps every message. It can bind privileged ports without sudo if needed.

### Quick start with the default profile

```
./havoc server -d
```

Outcome: the server boots with built-in default settings — handy for a lab smoke test before writing a real profile; `--debug` adds verbose diagnostics and `--debug-dev` compiles agents with debug output enabled.

### Launch the operator client

```
./havoc client
```

Outcome: the Qt client opens a connection dialog; entering the teamserver host, port, and the user/password from the Yaotl profile lands you in the UI with session tables, the event viewer, and interact tabs.

*No corpus evidence — derived from upstream documentation.*
