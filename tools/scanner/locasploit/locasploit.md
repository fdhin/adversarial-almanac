---
name: "locasploit"
categories:
  - blackarch-scanner
version: "117.fa48151"
homepage: "https://github.com/lightfaith/locasploit"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-25"
build-system: scripts
license: GPL2
meta-checked: "2026-11-09"
github-repo: "lightfaith/locasploit"
github-stars: 18
github-pushed: "2017-08-16"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.0
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# locasploit

> Metasploit-style console for local enumeration and exploitation on the host it runs on.

## What it's for

Locasploit is a Python framework for enumerating the machine you are already on: it ships modules — local information gathering, filesystem checks, known-vulnerable software — that you search, select and run from an interactive console with sessions, global parameters, background jobs and a per-session knowledge store (`analysis.db`). The same distribution builds small standalone clients (`iot`, `iotssh`, `iotlocal`, `iotdiff`) for headless targets. The focus is local privilege-escalation triage with a module API, not remote exploitation.

## Typical use cases

### Run the console

Enumerate interactively, module by module.

```
locasploit
```

Outcome: opens the framework console under a new analysis session; `help` lists every command, `list`/`search` filter the module set, `use <module>` selects one, then `show options` and `run` execute it — results are stored in the session database for later review.

### Replay a command script

Automate the same enumeration on many hosts.

```
locasploit -c commands.txt
```

Outcome: executes the console commands listed in the file (one per line, e.g. `use local/basic_info` followed by `run`) in order instead of the interactive prompt, then continues in the console loop.
