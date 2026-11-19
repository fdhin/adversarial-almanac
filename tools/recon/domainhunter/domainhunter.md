---
name: "domainhunter"
categories:
  - blackarch-recon
version: "51.38cb7ef"
homepage: "https://github.com/threatexpress/domainhunter"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-12"
build-system: scripts
license: BSD
meta-checked: "2026-11-09"
github-repo: "threatexpress/domainhunter"
github-stars: 1687
github-pushed: "2024-06-06"
github-archived: false
github-issues: 9
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.7
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# domainhunter

> Screens expired and available domains for a clean history: category and reputation checks across Blue Coat, IBM X-Force and Cisco Talos plus Archive.org age, to pick good phishing and C2 candidates.

## What it's for

domainhunter (by Joe Vest and Andrew Chiles, Threat Express) automates red-team domain selection. It pulls recently expired/deleted or keyword-matched available domains from the ExpiredDomains.net search engine (an account there is needed for full functionality), then checks each candidate against Symantec/Blue Coat WebPulse, IBM X-Force and Cisco Talos reputation categories and looks up its Archive.org history — because a previously benign, properly categorized domain is often worth far more to an engagement than a fresh registration. Output is a text table plus a timestamped HTML report with links to every reputation source. It does not buy or manage domains, and it is not a subdomain enumeration tool.

## Typical use cases

### Pull the latest expired domains with reputation checks

```
domainhunter
```

Outcome: fetches the ~100 most recently expired/deleted .com/.net/.org domains, prints a text table with each domain's category verdict, and writes the timestamped HTML report to the working directory.

### Keyword search with a result cap

```
domainhunter -k cloud -r 25 -c
```

Outcome: restricts the ExpiredDomains.net search to domains containing "cloud", returns 25 candidates, and `-c` runs the reputation checks; add `--ocr` to auto-solve CAPTCHA challenges and `-t 5` to remove the inter-request delay.

### Deep-check a single domain

```
domainhunter -s example.com
```

Outcome: runs every reputation check against the one domain and prints each verdict (e.g. `example.com: Technology/Internet`) — the final go/no-go check on a candidate before purchase.

### Check a prepared list at full speed

```
domainhunter -f domains.txt -t 5 --ocr
```

Outcome: works through your line-delimited domain file with no request delay, OCR-ing CAPTCHAs as they appear, and adds all results to the report; `-a` filters results to domains that appear in Alexa listings.

*No corpus evidence — derived from upstream documentation.*
