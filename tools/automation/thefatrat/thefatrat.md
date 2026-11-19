---
name: "thefatrat"
categories:
  - blackarch-automation
version: "813.b0586d0"
homepage: "https://github.com/Screetsec/TheFatRat"
packaged-by: [blackarch]
blackarch-builddate: "2022-02-20"
build-system: scripts
license: GPL3
meta-checked: "2026-11-09"
github-repo: "Screetsec/TheFatRat"
github-stars: 11581
github-pushed: "2024-03-17"
github-archived: false
github-issues: 152
github-match: "exact"
gh-checked: "2026-11-07"
worth: 4.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# thefatrat

> Menu-driven front-end that automates msfvenom/Metasploit backdoor creation and post-exploitation.

## What it's for

TheFatRat wraps msfvenom and Metasploit in an interactive menu that
compiles backdoors for Linux, Windows, macOS and Android, aims to make
them bypass common antivirus, and pairs them with local or remote
listeners. Beyond payload creation it bundles post-exploitation helpers:
embedding payloads into legitimate files (APKs, Office documents, deb
packages), a file pumper to change file sizes, and automatic AutoRun file
creation for USB/CD-ROM staging. Everything is driven through numbered
menus rather than flags. Realistic expectations: its AV-bypass claims age
quickly against current engines, and generating/using such backdoors
outside systems you are authorized to test is illegal.

## Typical use cases

### Generate a backdoor from the menu

```
./fatrat
```

Outcome: opens the numbered top-level menu; pick the target OS category
and payload format (executable, macro, APK, embedded-in-file variants) and
the tool drives msfvenom, compiles the artifact into its output directory
and offers to start the matching Metasploit handler. On packaged installs
the launcher is `thefatrat`.

### Diagnose a broken setup

```
./chk_tools
```

Outcome: checks that every dependency TheFatRat drives (msfvenom,
Metasploit, compilers, etc.) is present at a workable version and prints a
suggested fix for whatever it finds wrong — the first stop when `setup.sh`
or a generation step misbehaves.

### Update the tool and its modules

```
./update
```

Outcome: pulls the latest TheFatRat sources and module updates; re-run
`./setup.sh` afterwards to settle dependencies, as the README update
procedure prescribes.

*No corpus evidence — derived from upstream documentation.*
