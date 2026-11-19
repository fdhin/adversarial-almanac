---
name: "hackersh"
categories:
  - blackarch-automation
version: "0.2.0"
homepage: "http://www.hackersh.org/"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-25"
build-system: scripts
license: GPL
meta-checked: "2026-11-09"
github-repo: "ikotler/hackersh"
github-stars: 126
github-pushed: "2013-10-12"
github-archived: false
github-issues: 2
github-match: "search"
gh-checked: "2026-11-07"
worth: 2.5
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# hackersh

> A REPL whose Pythonect-style pipelines chain security-tool wrappers, passing results and metadata between stages.

## What it's for

Hackersh ("Hacker Shell", Itzik Kotler, 2013) is a command interpreter written in Python 2 on top of Pythonect. Instead of byte-pipelines, expressions chain tool wrappers with `->` and pass typed security metadata (URLs, hosts, ports) from one stage to the next: a URL flows into `nmap`, whose open ports flow into the next wrapper, and so on. It ships built-in components (`url`, `nmap`, `w3af`) plus wrappers for external tools such as nbtscan and amap, and extends by writing new components. It is abandoned Python 2 software — expect dependency friction on modern systems — but the pipeline model is still the clearest way to understand what it was for.

## Typical use cases

### Chain tools interactively in the REPL

```
hackersh
```

Then, at the prompt, evaluate a pipeline such as:

```
"http://target.example.com" -> url -> nmap -> w3af
```

Outcome: the string is parsed as a URL object, handed to the nmap wrapper which fingerprints the host, and every discovered web port is passed to w3af for web-audit — a complete black-box scan expressed as one line, with each stage fed by the previous one's output. Command history is persisted in `~/.hackersh_history`.

### Run a pipeline from a script file

```
hackersh recon.hs
```

Outcome: the file is executed non-interactively as a Hackersh script — useful for keeping repeatable recon pipelines in version control — and the last expression's value is printed when it completes.

### Debug a pipeline's plumbing

```
hackersh -v recon.hs
```

Outcome: logging verbosity increases with each additional `-v` (up to four), showing how expressions are translated to Pythonect and evaluated stage by stage; `hackersh -V` prints the version banner.

*No corpus evidence — derived from upstream documentation.*
