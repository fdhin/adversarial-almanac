---
name: "pasv-agrsv"
categories:
  - blackarch-automation
version: "57.6bb54f7"
homepage: "https://github.com/isaudits/pasv-agrsv"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-25"
build-system: scripts
license: GPL3
meta-checked: "2026-11-09"
github-repo: "isaudits/pasv-agrsv"
github-stars: 38
github-pushed: "2018-11-03"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# pasv-agrsv

> Passive recon orchestrator for a domain: runs the recon tools and OSINT site queries listed in its config, regex-extracts emails/IPs/DNS names from everything they emit, then recursively works those findings — all stored in a SQLite project database.

## What it's for

Kicking off external reconnaissance against a domain with one command while staying passive: it executes the tools and OSINT website queries defined in a config file against the TLD, harvests emails, IP addresses and DNS names from their output via regex, then runs a second wave of tooling against each discovered IP and hostname. Everything — domains, emails, IPs, DNS names, full tool run history and output — is persisted in a SQLite database per project, so results accumulate instead of scrolling away. An opt-in aggressive mode in the config adds non-passive actions on discovered hosts (website screenshots via cutycapt, spidering). By design it touches the target only passively in default mode; it does not scan ports or exploit anything unless you deliberately flip aggressive on. Legacy status: a 2015-era Python 2 script (python-elixir/pyPdf dependencies, tools like webshag configured in its default config) — expect to prune its config for modern use, since some referenced tools and sites have not survived.

*No corpus evidence — derived from upstream documentation.*

## Typical use cases

### Start a passive recon project for one domain

```
pasv-agrsv example.com
```

Outcome: creates a new project for the domain (prompting for/deriving the project name and output directory), immediately runs the scripted passive tasks from the active config, and stores all discovered emails, IPs and DNS names in the project's SQLite database — check the tool-run history table to see exactly which queries produced each finding.

### Run interactively with multiple domains

```
pasv-agrsv
```

Outcome: with no argument the script runs in interactive mode: choose the project name and output directory, add as many TLDs as you like, then launch — useful when the engagement scope is a handful of related domains that should share one database.

### Swap in a custom config

```
pasv-agrsv -c /path/to/mytools.cfg example.com
```

Outcome: uses your config file instead of the default (`default.cfg`, created by copying the bundled `default.example` on first launch) — edit it to add or remove recon tools, OSINT sites and extraction regexes, and to enable aggressive mode for screenshots/spidering of discovered hosts. Multiple configs let you keep a fast passive profile and a deeper one.
