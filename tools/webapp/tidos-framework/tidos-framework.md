---
name: "tidos-framework"
categories:
  - blackarch-webapp
version: "v2.0.beta2.r22.g4098187"
homepage: "https://github.com/0xInfection/TIDoS-Framework"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-17"
build-system: scripts
license: GPL-3.0-or-later
meta-checked: "2026-11-09"
github-repo: "0xInfection/TIDoS-Framework"
github-stars: 1870
github-pushed: "2023-04-19"
github-archived: false
github-issues: 16
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.7
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# tidos-framework

> Metasploit-style console and GUI framework covering web recon through vulnerability analysis.

## What it's for

TIDoS (0xInfection) is an offensive web-application penetration testing framework organized
in Metasploit-like phases: Reconnaissance (passive and active, ~50 modules), Scanning &
Enumeration, Vulnerability Analysis, an Exploits Castle, and Auxiliaries — around 108
modules in total, with an `Auto-Awesome` module per phase that automates everything. It
offers two interfaces: a Qt5 graphical interface (`tidv2`) and a console (`tidconsole`).
You supply the domain; modules handle the rest, optionally routing attacks through Tor.
Development stopped in 2023, so module data (breach lookups, dork results, CVE checks)
reflects that era.

## Typical use cases

### Start the console interface

Open the Metasploit-style console for module-driven testing.

```
sudo tidconsole
```

Outcome: the TIDoS console starts (run as root for scanning modules that need raw sockets); from here targets and modules are managed interactively.

### Start the graphical interface

Use the Qt5 GUI instead of the console.

```
tidv2
```

Outcome: opens the Qt5 interface exposing the same phases and modules.

### Drive a scan from the console

Add a target, pick a phase, and load a module.

```
vicadd target.example.com
list recon
find osint
load recon/osint
tor
```

Outcome: `vicadd` registers the target (`viclist` shows all, `vicdel` removes), `list` enumerates the modules of a phase/category, `find` searches modules by keyword, `load` enters a module (module commands `info` and `opts` describe it, `set` configures options, `leave` returns), and `tor` pipes the module's traffic through the Tor network. `q` terminates the session.

No corpus evidence — derived from upstream documentation.
