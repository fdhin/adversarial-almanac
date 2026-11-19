---
name: "msfenum"
categories:
  - blackarch-automation
version: "36.6c6b77e"
homepage: "https://github.com/wez3/msfenum"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-25"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "wez3/msfenum"
github-stars: 102
github-pushed: "2022-10-11"
github-archived: false
github-issues: 1
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.4
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# msfenum

> Fan-out driver that runs a configured set of Metasploit auxiliary modules against a list of targets.

## What it's for

msfenum automates "low hanging fruit" scanning with Metasploit: it takes a file of targets and runs every auxiliary module listed in its per-module config files (`modules/`) against them in parallel threads — think open shares, browsable services, quick vulnerability checks. Which modules run and with what options is controlled by plain config files (one per module, plus a global `config` file whose `CHANGEME` values you must edit first — modules with unedited settings are skipped). It is a thin orchestrator over msfconsole via msfrpc-style interaction, not a scanner itself: the actual work is done by Metasploit auxiliary modules you already have.

## Typical use cases

### Run the configured auxiliary sweep over a target list

Basic invocation with default logging folder.

```
msfenum -t 10 targets.txt
```

Outcome: every configured auxiliary module is executed against every host in `targets.txt` with 10 parallel threads. Raw per-module Metasploit output lands in separate files under `logs/<unix-timestamp>/`, script events go to `msfenum.log`, and when the sweep finishes a summary of successful findings is printed for a quick overview.

### Run a sweep under a named project

Keep results of an engagement grouped.

```
msfenum -p engagement-one -t 20 targets.txt
```

Outcome: identical module execution, but all raw results are written under `logs/engagement-one/` instead of a timestamp directory, with 20 threads; run the same project name across days to accumulate comparable output per target set.

*No corpus evidence — derived from upstream documentation.*
