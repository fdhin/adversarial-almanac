---
name: "c5scan"
categories:
  - blackarch-webapp
version: "30.be8845c"
homepage: "https://github.com/auraltension/c5scan"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-25"
build-system: scripts
license: GPL2
meta-checked: "2026-11-09"
github-repo: "auraltension/c5scan"
github-stars: 19
github-pushed: "2020-09-17"
github-archived: false
github-issues: 2
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.0
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# c5scan

> Information gatherer and vulnerability scanner for the Concrete5 CMS.

## What it's for

c5scan fingerprinting-targets sites built on Concrete5: it detects the CMS version (from the meta generator tag and other markers), lists interesting HTTP headers, checks for readable sensitive paths, and walks version-specific update directories under `/updates/` to find what has been installed. It is explicitly unmaintained upstream ("a little out of date presently pending a refactor"), so treat it as a recon helper for ageing Concrete5 deployments rather than a current vulnerability scanner; it does not exploit anything it finds.

## Typical use cases

### Fingerprint a Concrete5 site and dump its robots.txt

Run the scanner against a single target.

```
c5scan -u http://target.example.com -r
```

Outcome: prints the discovered Concrete5 version (e.g. `5.6.2.1`) with the source it was derived from, interesting server/x-powered-by headers, and — with `-r` — the contents of `robots.txt` if present; it then enumerates `/updates/` looking for update folders that reveal the install history. The scheme defaults to `http://` when omitted.

No corpus evidence — derived from upstream documentation.
