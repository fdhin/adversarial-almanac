---
name: "simplyemail"
categories:
  - blackarch-recon
version: "1.4.10.r7.6a42d37"
homepage: "https://github.com/killswitch-GUI/SimplyEmail"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-26"
build-system: scripts
license: GPL2
meta-checked: "2026-11-09"
github-repo: "killswitch-GUI/SimplyEmail"
github-stars: 957
github-pushed: "2023-01-12"
github-archived: false
github-issues: 23
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.5
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# simplyemail

> Email address enumeration across public sources, with optional SMTP verification and name-pattern generation.

## What it's for

SimplyEmail harvests email addresses for a domain from the same class of public sources as theHarvester and recon-ng — search engines, paste sites and other per-source modules — aiming to be the simple "run everything" option for the email-recon phase of an engagement. Beyond collection it can generate plausible name-based addresses for an organization, verify harvested addresses against their mail server over SMTP, and emit results as JSON for downstream tooling. Modules are listed with -l and run individually or all at once with -all; API-key modules are excluded from -all by design.

## Typical use cases

### Harvest every address for a domain

```
./SimplyEmail.py -all -e example.com
```

Outcome: runs all non-API harvester modules against the domain and prints the addresses found, with per-module status in the console.

### Harvest, verify and export in one pass

```
./SimplyEmail.py -all -e example.com -verify --json emails.json
```

Outcome: additionally checks each candidate against the target's SMTP server and writes the result set to a JSON file, so phishing or password-spray lists start pre-validated.

### List available modules

```
./SimplyEmail.py -l
```

Outcome: prints the loaded harvester modules so specific sources can be chosen instead of -all.

*No corpus evidence — derived from upstream documentation (flags verified against the packaged argparse).*
