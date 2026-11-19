---
name: "catnthecanary"
categories:
  - blackarch-recon
version: "7.e9184fe"
homepage: "https://github.com/packetassailant/catnthecanary"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-25"
build-system: scripts
license: MIT
meta-checked: "2026-11-09"
github-repo: "packetassailant/catnthecanary"
github-stars: 6
github-pushed: "2014-01-02"
github-archived: false
github-issues: 1
github-match: "exact"
gh-checked: "2026-11-07"
worth: 1.7
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# catnthecanary

> Query the canary.pw disclosure data set for IPs, emails, hosts, hashes, phone numbers and URLs.

## What it's for

catnthecanary (by Chris Patten / packetassailant, 2014) searches the canary.pw data set — an index of potentially sensitive data disclosed on public paste sites — for indicators you already hold. Because canary.pw exposed no API, the tool wraps its search operators (`!ip`, `!email`, `!host`, `!hash`, `!phone`, `!http`) behind simple command-line flags, supporting both single values and newline-delimited bulk files for each operator. It is alpha-quality 2014 code: results depend entirely on canary.pw still being reachable in its then-current form.

## Typical use cases

### Check a single indicator

See whether one email address (or IP, host, hash, phone, URL) appears in the data set.

```
catnthecanary -e user@example.com
```

Outcome: prints the number of canary.pw records referencing the address plus the search URL for reviewing the matches in a browser.

### Screen a bulk list

Run a whole asset inventory through the data set in one pass.

```
catnthecanary -I hosts.txt
```

Outcome: queries every IP in the newline-delimited file in turn and prints one line per value with its hit count and canary.pw link, so leaked assets stand out by result count.

### Show the operator reference

List every query flag and its bulk-file counterpart.

```
catnthecanary -u
```

Outcome: prints the built-in usage screen mapping each operator (-i/-I for IP, -e/-E for email, -h/-H for host, -a/-A for hash, -p/-P for phone, -w/-W for URL) to single and bulk forms.

*No corpus evidence — derived from upstream documentation.*
