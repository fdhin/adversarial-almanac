---
name: "dnspredict"
categories:
  - blackarch-scanner
version: "0.0.2"
homepage: "http://johnny.ihackstuff.com/"
packaged-by: [blackarch]
blackarch-builddate: "2025-07-18"
build-system: perl
license: GPL
meta-checked: "2026-11-09"
github-repo: "BlackArch/blackarch"
github-stars: 3563
github-pushed: "2026-11-08"
github-archived: false
github-issues: 35
github-match: "search"
gh-checked: "2026-11-09"
worth: 8.3
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 1007
gh-activity-checked: "2026-11-09"
---

# dnspredict

> "DNS Predictor by Jimmy Neutron": expands seed words through Google Sets, then checks which resulting subdomains exist.

## What it's for

dnspredict is a Perl subdomain-discovery idea from the Google-hacking era
(distributed via johnny.ihackstuff.com): you give it a domain plus two
seed item names, it scrapes Google Labs Sets (`labs.google.com/sets`) for
names related to the seeds, builds candidate hostnames under the domain,
and DNS-checks which ones exist, writing confirmed names to a file. The
technique — expand a small seed vocabulary statistically, then resolve —
survives in modern alteration-based subdomain tools; this implementation
does not, because the Google Sets backend it queries was shut down with
Google Labs, so the scrape step now fails. Run it as a historical artifact
or for its output plumbing; real subdomain enumeration today belongs to
wordlist scanners like dnscan. The pseudonymous author never had a code
host; the packaged script is carried verbatim (sha512-identical to the
PKGBUILD digest) as `packages/dnspredict/dnspredict.pl` inside the
BlackArch package collection, which is what this note's GitHub link and
stats describe — the distro collection, not the author.

## Typical use cases

### Predict and check subdomains from seeds

Grow two known host names into a candidate list and resolve it.

```
dnspredict --domain example.com --item1 www --item2 mail
```

Outcome: prints its banner, validates the three required arguments
(`--domain`, `--item1`, `--item2`), queries Google Sets with the two seeds
and then DNS-checks each returned name under the domain, appending
confirmed hostnames to `dns.txt`; with the Sets backend retired the scrape
returns nothing, so the run now effectively demonstrates the argument and
file plumbing only. A `--proxy` can be set for the outbound scrape.

### Control output and retries

Point the results somewhere specific and harden the scraping.

```
dnspredict --domain example.com --item1 www --item2 mail --file found-dns.txt --proxy http://198.51.100.10:3128 --timeout 20 --retries 5
```

Outcome: writes confirmed names to `found-dns.txt` (`--file`, default
`dns.txt`), routes the web lookups through the proxy at
198.51.100.10:3128, allows 20 seconds per fetch and 5 retries — the same
run with all of its tuning knobs made explicit.

*No corpus evidence — derived from the packaged source (dnspredict.pl 0.0.2
shipped by BlackArch: GetOptions interface and inline documentation).*
