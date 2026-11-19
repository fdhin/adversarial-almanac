---
name: "siege"
categories:
  - vulnerability
  - web
version: "4.2.0-1"
homepage: "https://www.joedog.org/siege-home/"
origin: "arch"
arch-repo: "extra"
packaged-by: [arch, kali, nixpkgs]
arch-builddate: "2026-09-07"
build-system: autotools
license: GPL-3.0-or-later
meta-checked: "2026-11-09"
github-repo: "JoeDog/siege"
github-stars: 6218
github-pushed: "2026-11-04"
github-archived: false
github-issues: 148
github-match: "search"
gh-checked: "2026-11-07"
worth: 8.1
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 66
gh-activity-checked: "2026-11-09"
---

# siege

> HTTP load tester and benchmark utility that hammers a URL or a URL list with n simulated concurrent users.

## What it's for

Siege (by Jeffrey Fulmer / JoeDog, in continuous development since 2000)
places a web server "under siege": it drives a configurable number of
simulated concurrent users at a single URL or through a list of URLs,
then reports transactions, elapsed time, bytes transferred, response
time, transaction rate, concurrency, and status-code counts. It speaks
HTTP/1.0 and 1.1 (and FTP), handles GET/POST, cookies, basic
authentication, and per-user configuration, which makes it equally useful
as a regression harness — replay a URL list and diff the status codes —
and as a capacity benchmark. It is a load generator for systems you
operate; it has no vulnerability-detection logic of its own. First run
creates `~/.siege/siege.conf` (`siege.config` writes it explicitly),
where defaults like the 255-user concurrency cap live.

## Typical use cases

### Load a single URL with fixed reps

The basic soak test.

```
siege -c 25 -r 10 http://target.example.com/
```

Outcome: 25 concurrent users each request the URL 10 times, then a
summary table prints transaction count, availability, response time,
throughput, and status-code breakdown — the baseline numbers to compare
every change against.

### Timed benchmark with no delays

Measure raw throughput.

```
siege -b -c 100 -t60S http://target.example.com/
```

Outcome: benchmark mode removes the default 0.5 s delay between requests
and the run stops after 60 seconds (`-t` takes S/M/H modifiers), giving
the server's flat-out transaction rate and error count under full load.

### Replay a URL list like real browsing

Hit many endpoints in realistic order.

```
siege -c 50 -r once -i -f urls.txt
```

Outcome: each simulated user walks the urls.txt file exactly once
(`-r once`), `-i` randomizes the order ("internet mode") so users don't
march in lockstep; response-time and failure counts per run surface
which pages degrade first under load.

### Preview a single transaction

Debug what siege will actually send.

```
siege -g https://target.example.com/
```

Outcome: one HEAD/GET transaction is printed — request headers, response
headers, and status — a quick check of redirects, caching headers, and
auth challenges before committing to a full run.

### Simulate human pacing

Approximate real user behavior between clicks.

```
siege -c 50 -d 3 -t5M -f urls.txt
```

Outcome: each user sleeps a random 1–3 seconds between requests
(`-d NUM`, replacing the 0.5 s default), stretching the test across five
minutes; the resulting concurrency and response-time profile reflects
digest-and-click traffic rather than a flood, which is what capacity
planning against real session lengths needs.

*No corpus evidence — derived from upstream documentation (README and siege(1) man page).*
