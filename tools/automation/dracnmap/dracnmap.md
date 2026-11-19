---
name: "dracnmap"
categories:
  - blackarch-automation
version: "69.09d3945"
homepage: "https://github.com/screetsec/Dracnmap"
packaged-by: [blackarch]
blackarch-builddate: "2017-10-12"
build-system: scripts
license: MIT
meta-checked: "2026-11-09"
github-repo: "screetsec/Dracnmap"
github-stars: 1390
github-pushed: "2018-01-23"
github-archived: false
github-issues: 5
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.1
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# dracnmap

> Menu-driven front end that runs nmap scans through guided prompts instead of option syntax.

## What it's for

Dracnmap wraps nmap in an interactive bash menu so common reconnaissance workflows — host discovery, service/version detection, NSE script runs — can be launched by picking numbers and answering prompts for the target, instead of memorizing nmap flag combinations. After each scan it asks how to save the result (normal, grepable, XML, or HTML format) and suggests its own Output/ directory for the report file. It adds no scanning capability of its own: every action is an nmap invocation underneath, so its value is guidance and consistent saved output, and its menus reflect the nmap era it was written in (2017–2018; the project is dormant). Requires nmap and root.

## Typical use cases

### Start the guided scan menu

Run reconnaissance without writing the nmap command yourself.

```
sudo dracnmap
```

Outcome: opens the Dracnmap console; choose a scan type, enter the target IP or range when prompted, and the underlying nmap scan runs with live output and the report saved in the format you select (XML, HTML, normal, or grepable) to the Output/ path you confirm.

*No corpus evidence — derived from upstream documentation.*
