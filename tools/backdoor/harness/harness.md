---
name: "harness"
categories:
  - blackarch-backdoor
version: "19.ed2a6aa"
homepage: "https://github.com/Rich5/Harness"
packaged-by: [blackarch, aur]
blackarch-builddate: "2026-01-12"
build-system: scripts
license: MIT
meta-checked: "2026-11-09"
github-repo: "Rich5/Harness"
github-stars: 78
github-pushed: "2016-01-21"
github-archived: false
github-issues: 1
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.5
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# harness

> Remote interactive PowerShell delivered to virtually any TCP socket: unmanaged and reflective payloads driven from a small Python asyncio console.

*No corpus evidence — derived from upstream documentation.*

## What it's for

Giving an operator the feel of the native powershell.exe on a Windows target without dropping the real binary. Harness payloads implement a remote PowerShell CLI over TCP or SSL — multiline command processing like the genuine shell, remote importing of PowerShell modules (base64-encoded, via a supporting handler), an unmanaged build that helps bypass application whitelisting, and a reflective DLL build (ReflectiveHarness) for injection into other processes. The handler side is a Python 3.4+ asyncio console that loads a module for each combination of transport (TCP/SSL), architecture (x86/x64) and format (exe/dll). It is a 2015-era project, so expect old tooling assumptions; it provides the channel and the shell, while initial execution on the target is up to you.

## Typical use cases

### Start the handler console and load modules

```
python3 harness.py
```

Outcome: drops you at the `H> ` prompt. `show modules` lists the bundled handler and payload modules; `load handlers/PSHandler` brings up the PowerShell session handler, `show options`/`set` configure it (e.g. its SSL option), and `show sessions` plus `session` attach to target callbacks. Payload modules such as `payloads/TCP/x86/exe/Harness_x86` wrap the binaries you deploy to call the handler back.
