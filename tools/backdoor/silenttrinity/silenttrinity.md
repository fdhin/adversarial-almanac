---
name: "silenttrinity"
categories:
  - blackarch-backdoor
version: "293.08b1c61"
homepage: "https://github.com/byt3bl33d3r/SILENTTRINITY"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-13"
build-system: scripts
license: GPL3
meta-checked: "2026-11-09"
github-repo: "byt3bl33d3r/SILENTTRINITY"
github-stars: 2346
github-pushed: "2023-12-06"
github-archived: true
github-issues: 48
github-match: "exact"
gh-checked: "2026-11-07"
worth: 0.6
worth-basis: "historical (archived/gone upstream)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# silenttrinity

> Asynchronous, multiplayer C2/post-exploitation framework powered by Python 3 and .NET's DLR — BYOI (Bring Your Own Interpreter) restores PowerShell-style tradecraft without touching PowerShell.

## What it's for

SILENTTRINITY (byt3bl33d3r) is a C2/post-exploitation framework whose implant, Naga, embeds third-party .NET scripting languages (notably Boo) to compile and evaluate tasks at runtime — the BYOI concept — giving the flexibility of PowerShell-style attacks while using no PowerShell at all. The teamserver and client are Python 3.7/asyncio with websocket-based real-time updates; multiplayer is a first-class feature (multi-user collaboration, one client driving several teamservers), C2 traffic is ECDHE-encrypted, and listeners, stagers, modules and C2 channels are all modular. The upstream repository is archived (last push December 2023): it still runs, but treat it as a frozen historical framework rather than a maintained one.

Corpus evidence is tool-tag-only (OCR metadata from one engagement lists it among the C2 tools used; no command lines survive), so the examples below are verified against upstream documentation.

## Typical use cases

### Start a teamserver

Bring up the server that hosts listeners, stagers and modules.

```
python3 st teamserver ATTACKER_IP LongPassword123!
```

Outcome: the teamserver starts on the default port 5000; keep the password — every operator client authenticates with it.

### Connect an operator client over TLS websockets

Attach an operator console to a running teamserver.

```
python3 st client wss://operator:LongPassword123!@ATTACKER_IP:5000
```

Outcome: opens the ST console wired to the teamserver. The `wss://` scheme (two s's) is required — a plain `ws://` URL sends all client-to-teamserver traffic in cleartext.

### Connect interactively without arguments

Manage connections through the built-in menus instead of flags.

```
python3 st client
```

Outcome: drops into the CLI's top menu; from the `teamservers` menu, `connect wss://operator:LongPassword123!@ATTACKER_IP:5000` attaches to a teamserver — the natural workflow when one client supervises multiple teamservers, which the framework supports natively.
