---
name: "infoga"
categories:
  - blackarch-recon
version: "33.79a1c03"
homepage: "https://github.com/m4ll0k/infoga"
packaged-by: [blackarch]
blackarch-builddate: "2023-05-04"
build-system: scripts
license: MIT
meta-checked: "2026-11-09"
github-repo: "m4ll0k/infoga"
github-status: "gone"
github-match: "exact"
gh-checked: "2026-11-07"
worth: 4.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
---

# infoga

> Email OSINT harvester: collects email accounts with source, IP, hostname and country, and checks them against breach data.

## What it's for

Infoga (m4ll0k) gathers email accounts for a domain or a single address from public sources — search engines, PGP key servers and Shodan — and for each address reports associated IP, hostname and country, optionally checking whether it appears in leaked-credential databases via the haveibeenpwned.com API. It is a simple Python 3 CLI aimed at the early reconnaissance phase of a penetration test or measuring a company's exposure. The upstream GitHub repository has since been deleted; the packaged script keeps working with the sources it supports.

## Typical use cases

### Harvest and breach-check a domain's emails

Collect everything public about a target domain in one pass.

```
infoga --domain example.com --source all --breach -v 2 --report example.com.txt
```

Outcome: queries every available source (`--source all`), prints findings at verbosity 2, checks each address against haveibeenpwned (`--breach`) and writes the collected accounts with their metadata to the report file.

### Profile a single known address

Pivot from one email to its infrastructure footprint.

```
infoga --info user@example.com --breach -v 3 --report user.txt
```

Outcome: looks the address up across sources, prints any linked IP, hostname and country plus breach status at higher verbosity, and saves the profile to `user.txt`.

*No corpus evidence — derived from upstream documentation.*
