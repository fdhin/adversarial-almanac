---
name: "vbrute"
categories:
  - blackarch-recon
version: "1.11dda8b"
homepage: "https://github.com/nccgroup/vbrute"
packaged-by: [blackarch]
blackarch-builddate: "2019-11-16"
build-system: autotools
license: GPL3
meta-checked: "2026-11-09"
github-repo: "nccgroup/vbrute"
github-stars: 22
github-pushed: "2014-01-10"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.0
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# vbrute

> Virtual-host tester: tries every domain in a list against every IP in a list and reports which name/address pairs answer.

## What it's for

vbrute (NCC Group) solves a specific shared-hosting / CDN question: of these candidate hostnames, which ones actually resolve to and respond on this server? You give it a file of domains and a file of IPs; it sends an HTTP request to each IP with each domain in the `Host` header and tabulates the response status and length. Where a different status/length shows up per hostname, you have found vhosts worth investigating; identical rows usually mean a default catch-all page. It performs no fuzzing or exploitation — it is purely the cross-product test — and dates from 2014, so it speaks plain HTTP(S) requests only.

## Typical use cases

### Test which domains are hosted on given IPs

Map candidate vhost names onto a set of servers.

```
vbrute --domain-file domains.txt --ip-file ips.txt
```

Outcome: for every domain × IP combination, prints a row `Domain  IP  Code  Length` (HTTP status and body length). Rows whose Code/Length differ from the majority on that IP identify hostnames the server actually serves — typical starting points for further testing of a shared host.

### Slow down for flaky targets

Keep the cross-product test reliable against slow servers.

```
vbrute -d domains.txt -i ips.txt -t 10
```

Outcome: same matrix scan with the per-request timeout raised from the default 5 seconds to 10, so slow or distant hosts are not dropped from the comparison.
