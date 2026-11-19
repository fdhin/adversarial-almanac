---
name: "ct-exposer"
categories:
  - blackarch-scanner
version: "24.71252ac"
homepage: "https://github.com/chris408/ct-exposer"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-12"
build-system: scripts
license: GPL3
meta-checked: "2026-11-09"
github-repo: "chris408/ct-exposer"
github-stars: 488
github-pushed: "2022-08-16"
github-archived: false
github-issues: 1
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.8
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# ct-exposer

> Subdomain discovery through Certificate Transparency: pulls every name logged in crt.sh for a domain, then DNS-resolves each to keep only the ones that are live.

*No corpus evidence — derived from upstream documentation (chris408/ct-exposer README; usage block matches the packaged ct-exposer.py argparse).*

## What it's for

ct-exposer (chris408) turns CT logs into a subdomain list: it downloads the certificate entries for one domain from crt.sh, extracts the candidate subdomain names, and DNS-resolves each one. The resolution step is the point — CT logs are full of dead, retired or internal-only names, and only the resolving subset is immediately usable externally. Results surface as an IP-and-hostname table by default, or as lists ready to feed other tooling (URLs for crawlers, bare IPs for masscan). Python 3 with gevent; it processes a single domain per run, and names that were only ever covered by a wildcard certificate never appear — it reads logs, it does not guess.

## Typical use cases

### Enumerate a domain's CT-log subdomains

```
ct-exposer -d example.com
```

Outcome: prints progress lines (`Downloading domain list from crt.sh...`, `Parsed N domain(s) from list.`) then a two-column `IP<TAB>hostname` table of every subdomain that resolved. Non-resolving entries are silently dropped as stale or internal-only names — rerun from inside the network if internal DNS matters.

### Emit URLs or a masscan-ready IP list

```
ct-exposer -d example.com -u
ct-exposer -d example.com -m
```

Outcome: `-u` prints one `https://<hostname>` line per resolving subdomain (pipe it straight into a crawler or request tool); `-m` prints the resolved IPs one per line in masscan `-iL` import format, chaining CT-log discovery directly into port scanning.
