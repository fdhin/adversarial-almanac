---
name: "nexfil"
categories:
  - blackarch-social
version: "43.4d93c57"
homepage: "https://github.com/thewhiteh4t/nexfil"
packaged-by: [blackarch, aur]
blackarch-builddate: "2023-05-04"
build-system: scripts
license: MIT
meta-checked: "2026-11-09"
github-repo: "thewhiteh4t/nexfil"
github-stars: 2645
github-pushed: "2023-09-30"
github-archived: false
github-issues: 10
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.9
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# nexfil

> OSINT tool for finding profiles by username.

## What it's for

NExfil is an OSINT tool written in Python that checks a username against
300+ websites in a single run and reports which platforms have a profile
under that name. It is built for speed (a lookup completes in well under a
minute) and low false positives. It only takes usernames — it does not
search by real name, email, or phone. Results are written to a text file
automatically. Proxy support exists for single proxies via command-line
options.

## Typical use cases

### Check a single username across 300+ sites

One username, quick footprint check.

```
nexfil -u user
```

Outcome: Prints each platform where the username exists as it is found and
writes the full result set to a timestamped text file in the working
directory.

### Check multiple usernames at once

Run one batch lookup for several aliases of the same person.

```
nexfil -l "user1,user2"
```

Outcome: Looks up every comma-separated username in one pass and saves a
combined results file.

### Enumerate usernames from a file

Bulk-process a list of candidate usernames gathered during recon.

```
nexfil -f users.txt
```

Outcome: Reads one username per line from the file, scans each across all
supported platforms, and saves the aggregated results.

### Tune the per-site timeout

Speed up scans on a fast link or tolerate slow sites on a slow one.

```
nexfil -u user -t 20
```

Outcome: Uses a 20-second timeout per site (default 10) before marking a
platform unreachable, reducing false negatives on slow responses.

### Route the scan through a proxy

Hide the source IP of the enumeration.

```
nexfil -u user -pm single -proto http -ph 127.0.0.1 -pp 8080
```

Outcome: Sends all lookups through the HTTP proxy at 127.0.0.1:8080
instead of connecting directly.

*No corpus evidence — derived from upstream documentation.*
