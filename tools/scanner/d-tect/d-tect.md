---
name: "d-tect"
categories:
  - blackarch-scanner
version: "13.9555c25"
homepage: "https://github.com/shawarkhanethicalhacker/D-TECT"
packaged-by: [blackarch]
blackarch-builddate: "2017-09-03"
build-system: scripts
license: GPL3
meta-checked: "2026-11-09"
github-repo: "shawarkhanethicalhacker/D-TECT"
github-status: "gone"
github-match: "exact"
gh-checked: "2026-11-07"
worth: 4.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
---

# d-tect

> Menu-driven Python 2 all-in-one web scanner: subdomains, ports, WordPress checks, sensitive files, XSS, SQLi and clickjacking from one numbered list.

*No corpus evidence — derived from upstream documentation (D-TECT by Shawar Khan; the GitHub repository has been deleted, so the archived README is the source).*

## What it's for

D-TECT ("Pentest the Modern Web", by Shawar Khan) bundles a dozen web recon and detection steps behind a single interactive menu: sub-domain scanning, port scanning, WordPress scanning/username enumeration/backup grabbing, sensitive-file detection, same-site scripting detection, clickjacking detection, and XSS and SQL injection scanning. It is entirely interactive — launch it, pick a menu number, type the target when prompted; there are no CLI flags. It is Python 2.7 with bundled Colorama and BeautifulSoup, the upstream repository is gone (the BlackArch package keeps the last released code), and each individual check is shallow next to a dedicated tool — treat it as a quick one-stop triage pass over a web target, not a substitute for per-task scanners.

## Typical use cases

### Run a menu-driven scan against a site

```
d-tect
```

Then choose an option from the printed menu (e.g. the sub-domain scanner or the WordPress scanner) and enter `target.example.com` when prompted.

Outcome: the tool prints its numbered feature menu; each option asks for the target interactively and streams that module's findings to stdout — discovered subdomains, open ports, WordPress usernames and backup files, sensitive paths, XSS/SQLi candidates, or a clickjacking verdict depending on the choice. The launch command is identical for every module; only the menu selection differs.
