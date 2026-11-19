---
name: "xcavator"
categories:
  - blackarch-sniffer
version: "5.bd9e2d8"
homepage: "https://github.com/nccgroup/xcavator"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-26"
build-system: scripts
license: GPL3
meta-checked: "2026-11-09"
github-repo: "nccgroup/xcavator"
github-stars: 33
github-pushed: "2013-10-30"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.1
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# xcavator

> Post-exploitation data locator that sweeps IP ranges for FTP/FTPS/SMB shares and harvests files with credentials you supply.

## What it's for

Xcavator (Nikos Laleas, NCC Group) automates the "where is the interesting data" step after credentials are obtained: given a range of addresses and a set of credentials, it scans for services that host files (FTP, FTPS, and SMB), logs in with those credentials, downloads everything it can, and scans the downloaded files for interesting strings via a configurable regex. A search mode (-s) skips bulk downloading and only fetches files whose names match a regex. All parameters — targets, credentials, ports, regex — live in an INI-style xcavator.conf created on first run. It is a client-side harvester driven by credentials you already hold: it does not crack passwords, sniff traffic, or MITM anything.

## Typical use cases

### Generate the config on first run

Create the configuration file to edit with your credentials and targets.

```
xcavator
```

Outcome: writes xcavator.conf in the current directory with a section per supported protocol (e.g. [SMB]) holding user, pass, regex, port, and range keys; populate it and run again to scan.

### Harvest and scan all files in a range

Log into every matching file host and grep the downloads.

```
xcavator -c xcavator.conf
```

Outcome: walks every host in the configured ranges, authenticates with the configured credentials, downloads the reachable files, and reports matches of the configured regex (for example \bpassword\b) found inside them.

### Fetch only files whose names match a pattern

Use search mode instead of bulk download.

```
xcavator -s -c xcavator.conf
```

Outcome: recursively searches remote file listings for names matching the regex and downloads only those files, rather than everything reachable.

### Tune verbosity and timeouts

Control scan speed and detail.

```
xcavator -vv -t 5 -c xcavator.conf
```

Outcome: each connection gets a 5-second timeout (default 2) and the run prints detailed per-connection progress instead of the basic summary.

*No corpus evidence — derived from upstream documentation.*
