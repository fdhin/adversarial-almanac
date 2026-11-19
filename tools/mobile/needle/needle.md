---
name: "needle"
categories:
  - blackarch-mobile
version: "579.891b660"
homepage: "https://github.com/ReversecLabs/needle"
packaged-by: [blackarch, nixpkgs]
blackarch-builddate: "2024-12-25"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "ReversecLabs/needle"
github-stars: 1403
github-pushed: "2020-10-25"
github-archived: true
github-issues: 25
github-match: "exact"
gh-checked: "2026-11-09"
worth: 0.8
worth-basis: "historical (archived/gone upstream)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# needle

> MWR InfoSecurity's modular iOS security assessment console — archived, iOS 10 and below only; its successor is [[objection]].

## What it's for

Needle was MWR InfoSecurity's open-source modular framework for streamlining security assessments of iOS apps — the iOS counterpart to drozer, released at Black Hat USA 2016. A workstation console drives modules over SSH (Wi-Fi or USB) to a jailbroken device running the companion NeedleAgent app, covering data storage, inter-process communication, network communications, static code analysis, hooking and binary protections, with automatic issue detection into the bargain. It targets iOS 8–10: iOS 11's protections broke its approach and upstream decommissioned it, recommending [[objection]] instead. The repository has moved with its authors from mwrlabs to ReversecLabs (the old path redirects there), so the tool is still obtainable for an iOS ≤10 test rig.

## Typical use cases

### Start the interactive console

Open the module-driven shell against a jailbroken device with NeedleAgent listening.

```
python2 needle.py
```

Outcome: the Needle banner and a `[needle] >` prompt; `show options` lists the global settings (`IP`, `PORT`, `APP`, `AGENT_PORT`, `OUTPUT_FOLDER`, …), `set IP 127.0.0.1` with `set PORT 2222` switches to SSH-over-USB, and `search`, `use <module>` (e.g. `binary/info/metadata`) and `run` select and execute modules, saving artifacts under the output folder.

### Replay configuration from a resource file

Automate the global-option setup for repeat runs.

```
python2 needle.py -r config.txt
```

Outcome: Needle executes the `set`/`use` commands saved in `config.txt` at startup instead of waiting for interactive input — useful for pinning the target app, transport and module list per engagement.

### Run modules without the console

Script the framework headlessly for batch jobs or CI.

```
python2 needle-cli.py -g APP=com.example.app -m binary/info/metadata
```

Outcome: sets global options non-interactively (`-g KEY=Value`, repeatable), runs the named module(s) (`-m`, repeatable, options after `#` as in `device/agent_client#COMMAND=OS_VERSION`) and exits; collected artifacts and auto-detected issues land in the configured `OUTPUT_FOLDER` (`~/.needle/output` by default).

*No corpus evidence — derived from upstream documentation.*
