---
name: "parsero"
categories:
  - blackarch-recon
version: "81.e5b585a"
homepage: "https://github.com/behindthefirewalls/Parsero"
packaged-by: [blackarch, nixpkgs]
blackarch-builddate: "2024-12-25"
build-system: scripts
license: GPL2
meta-checked: "2026-11-09"
github-repo: "behindthefirewalls/Parsero"
github-stars: 173
github-pushed: "2017-09-01"
github-archived: false
github-issues: 6
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.7
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# parsero

> robots.txt auditor: probes every Disallow entry for a live HTTP status and can hunt down the paths that Bing indexed anyway.

## What it's for

Parsero (Javier Nieto, behindthefirewalls) reads a web server's robots.txt and checks each Disallow entry directly — the directories admins tried to keep out of search engines are often still reachable by anyone who just types the URL, and Parsero automates the check by requesting each path and reporting its HTTP status code (200/302/403/404...). It can also search Bing for indexed copies of those Disallow paths, since a robots.txt entry is a request crawlers may ignore, and status-checks those results too. It is a single-purpose Python 3 script (v0.75, dormant since 2017 but simple enough to keep working) that needs nothing beyond its dependencies. Use it against hosts you are authorized to audit; it only issues GET requests, but those still touch the target once per Disallow entry per run.

## Typical use cases

### Audit a host's robots.txt entries

See which hidden paths are actually reachable.

```
parsero -u target.example.com
```

Outcome: prints each Disallow path from robots.txt with its live status code (`http://target.example.com/admin 200 OK`), ending with a summary of how many links were analyzed and how many were available — the 200/302 lines are your follow-up list.

### Show only reachable paths

Cut the noise and keep just what answers.

```
parsero -u target.example.com -o
```

Outcome: filters the report to entries returning HTTP 200, so only directly accessible "hidden" directories are listed.

### Hunt Bing-indexed Disallow entries

Check whether search engines cached or indexed the paths anyway.

```
parsero -u target.example.com -sb
```

Outcome: after the status report, Parsero issues a `site:` Bing query per Disallow entry, prints the search URLs, and status-checks any indexed results it finds — surfacing content that leaked into the index despite the robots.txt.

### Audit many hosts from a list

Run the same robots audit across a file of domains.

```
parsero -f domains.txt
```

Outcome: each domain in the list is processed in turn with the same Disallow status reporting, giving a one-pass robots posture check across an authorized scope.

> No corpus evidence — derived from upstream documentation.
