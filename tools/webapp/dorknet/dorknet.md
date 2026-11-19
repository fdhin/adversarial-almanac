---
name: "dorknet"
categories:
  - blackarch-webapp
version: "58.419d6a2"
homepage: "https://github.com/NullArray/DorkNet"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-25"
build-system: scripts
license: MIT
meta-checked: "2026-11-09"
github-repo: "NullArray/DorkNet"
github-stars: 350
github-pushed: "2022-02-20"
github-archived: false
github-issues: 9
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.8
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# dorknet

> Selenium powered Python script to automate searching for vulnerable web apps.

## What it's for

DorkNet automates search-engine dorking: it feeds a dork (or a whole list of them) to Google through a real Firefox browser driven by Selenium and Geckodriver, then saves the result URLs to a text file for follow-up with SQLmap or similar tools. Reach for it when you want to harvest dork hits at browser-realistic fidelity — the Selenium emulation avoids most captchas that kill plain HTTP scrapers (when one does appear, solve it in the driven browser window and the run continues). It is a harvester, not a scanner: it does not test the collected URLs itself. The optional proxy prompt routes the browser through a proxy of your choice, and long runs can be sent through proxychains.

## Typical use cases

### Harvest results for a single dork

Run one dork and collect the hits.

```
dorknet -d "inurl:show.php?id=" -v
```

Outcome: opens Google through Geckodriver, pages through the results for the dork, and writes the found URLs to an output file while `-v` narrates progress — the output file is the input SQLmap or another tool consumes next.

### Batch a dork list overnight

Work through many dorks unattended.

```
dorknet -l dorks.txt --verbose
```

Outcome: iterates every dork in `dorks.txt` (one per line) through the same browser-driven search flow, accumulating result URLs for each; run it under proxychains (`proxychains dorknet -l dorks.txt --verbose`) when the traffic must be routed through a specific hop.

No corpus evidence — derived from upstream documentation (README options list and examples).
