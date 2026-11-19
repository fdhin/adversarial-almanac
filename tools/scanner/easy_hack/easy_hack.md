---
name: "easy_hack"
categories:
  - scanner
version: "git (2025-06-03)"
homepage: "https://github.com/sabri-zaki/easy_hack"
origin: "kitploit"
recipe: "upstream"
packaged-by: []
build-system: python
meta-checked: "2026-11-09"
github-repo: "sabri-zaki/easy_hack"
github-stars: 2529
github-pushed: "2025-06-03"
github-archived: false
github-match: "exact"
gh-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
worth: 4.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
license: see repository
github-issues: 51
---

# easy_hack

> Numbered-menu bash launcher for Termux that wires common pentest tools — payload generation, nmap, hydra, web scans — behind one Android keyboard workflow.

*No corpus evidence — derived from upstream documentation.*

## What it's for

Giving a Termux (Android terminal) user one menu instead of a dozen manual tool invocations. EasY_HaCk is a bash script presenting a numbered console menu whose entries install and/or drive established tools: msfvenom payload generation, Metasploit and BeEF setup, nmap network scanning, web-attack helpers, password cracking, and a local web server, with supporting utilities (nmap, hydra, tor, php, ruby, python) declared in its installer and bundled helper modules shipped under `.modules/`. It is an orchestrator: every actual scan or exploit is performed by the wrapped tool, and its installer's hard-coded paths are Termux-specific (`/data/data/com.termux/...`). Reach for it for the convenience of a launcher, not for capabilities beyond the tools it wraps.

## Typical use cases

### Open the tool menu

```
./EasY_HaCk
```

Outcome: clears the screen and prints the numbered menu (payload generator, framework installations, network scanning, web attacks, password cracking, web server, update); enter a number to drop into the corresponding wrapped tool's prompts, or choose the update option to refresh the framework.
