---
name: "slowhttptest"
categories:
  - vulnerability
  - web
version: "1.10.0-1"
homepage: "https://github.com/shekyan/slowhttptest"
origin: "arch"
arch-repo: "extra"
packaged-by: [arch, kali, nixpkgs]
arch-builddate: "2026-10-11"
build-system: autotools
license: Apache
meta-checked: "2026-11-09"
github-repo: "shekyan/slowhttptest"
github-stars: 1656
github-pushed: "2026-09-25"
github-archived: false
github-issues: 3
github-match: "exact"
gh-checked: "2026-11-07"
worth: 7.7
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 74
gh-activity-checked: "2026-11-09"
---

# slowhttptest

> Application-layer slow-DoS simulator: slowloris, slow POST, range, and slow-read attacks with availability tracking.

## What it's for

SlowHTTPTest (by Sergey Shekyan) simulates the family of application-layer
denial-of-service attacks that exhaust a server's connection pool by
never finishing conversations: slowloris-style partial headers (`-H`),
slow-dripping POST bodies (`-B`, R-U-Dead-Yet style), range-header abuse
(`-R`, the Apache Killer pattern), and slow reads of the response (`-X`).
What separates it from a raw flooder is its probe socket: every second it
issues one complete request and measures whether the server still answers,
so the run ends with a verdict on availability, not just a connection
count. It reports per-run statistics and can emit CSV plus an HTML chart
of service availability over time, making it the standard tool for
checking whether HTTP-layer DoS mitigations (timeouts, per-IP limits,
slowloris defenses) actually hold. Point it only at systems you own or
are paid to test. A C++ rewrite, `slowhttptest-ng`, adds HTTP/2 attacks
(rapid reset, CONTINUATION flood) alongside the classic binary.

## Typical use cases

### Slowloris (slow headers) with stats

Hold connections open with unfinished headers.

```
slowhttptest -c 1000 -H -g -o header_stats -i 10 -r 200 -t GET -u http://target.example.com/ -x 24 -p 3
```

Outcome: 1000 connections open at 200/s, each sending a valid request
header set minus the terminating blank line, then dribbling a random
header every 10 seconds (max 24 bytes each); `-g` writes header_stats CSV
and HTML availability charts, and the probe judges the server down when
its responses exceed the 3-second timeout.

### Slow POST body

Tie up workers with an endless request body.

```
slowhttptest -c 1000 -B -g -o body_stats -i 110 -r 200 -s 8192 -t POST -u https://target.example.com/login -x 10 -p 3
```

Outcome: each connection posts a body announced as 8192 bytes (`-s`) that
arrives 10 bytes every 110 seconds, keeping the handler waiting; servers
without a body-timeout defense show availability dropping in the
generated stats while total bandwidth stays trivial.

### Slow read against a large resource

Starve the server by reading responses a byte at a time.

```
slowhttptest -c 1000 -X -r 1000 -w 10 -y 20 -n 5 -z 32 -u http://target.example.com/largefile.bin -p 5 -l 350
```

Outcome: complete requests are sent for the large file but the client
advertises a tiny TCP window (10–20 bytes) and reads 32 bytes every 5
seconds, pinning server buffers for the 350-second run; the final message
("Hit test time limit", "No open connections left", ...) plus the
availability chart shows whether the server's pool or memory degraded.

For triage behind an egress proxy, `-d host:port` routes attack traffic
and `-e host:port` routes only the probe, so availability is measured
from a different vantage point than the attack.

*No corpus evidence — derived from upstream documentation (project wiki usage pages).*
