---
name: "datasploit"
categories:
  - blackarch-recon
version: "369.28d9caf"
homepage: "https://github.com/upgoingstar/datasploit"
packaged-by: [blackarch]
blackarch-builddate: "2025-11-23"
build-system: scripts
license: GPL3
meta-checked: "2026-11-09"
github-repo: "upgoingstar/datasploit"
github-stars: 3336
github-pushed: "2025-11-20"
github-archived: false
github-issues: 107
github-match: "exact"
gh-checked: "2026-11-07"
worth: 5.8
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 2
gh-activity-checked: "2026-11-09"
---

# datasploit

> Automated OSINT assistant that pivots from a domain, email, username or IP into one correlated recon report.

## What it's for

DataSploit runs a battery of OSINT collectors against a single target — a
domain, email address, username, IP or phone number — and correlates what
comes back: credentials and API keys/tokens in public leaks, subdomains,
domain registration history, legacy portals and related identifiers. The
point is the consolidated view: instead of running a dozen lookup sites by
hand you get one correlated result set, emitted as HTML and JSON reports plus
per-module text files. Project history matters here: the well-known 2016-era
Python 2 framework went dormant for years (and was dropped from most distro
repos as dead), but the original lead developer revived the repository, which
now carries a current Python 3 interface; the old documentation site is no
longer served, so treat the packaged README as the reference.

## Typical use cases

### Enumerate one target end to end

Point the whole collector battery at a single identifier.

```
datasploit -i example.com
```

Outcome: runs every collector against example.com, printing progress as each
module completes, and writes the correlated results as HTML/JSON reports
alongside per-module text files for later review.

### Sweep a list of targets

Feed a prepared target list instead of typing identifiers one by one.

```
datasploit -f targets.txt
```

Outcome: processes each newline-separated entry in targets.txt in turn with
the same collector set, producing a report per target — useful for the
inventory phase of a multi-client or multi-brand engagement.

No corpus evidence — derived from upstream documentation.
