---
name: "thc-ssl-dos"
categories:
  - blackarch-dos
version: "1.4"
homepage: "http://www.thc.org/thc-ssl-dos/"
packaged-by: [blackarch, kali, aur]
blackarch-builddate: "2025-09-29"
build-system: autotools
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "BlackArch/thc-ssl-dos"
github-stars: 7
github-pushed: "2019-12-06"
github-archived: false
github-issues: 2
github-match: "search"
gh-checked: "2026-11-07"
worth: 2.0
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# thc-ssl-dos

> The Hackers Choice's SSL/TLS handshake stress tester that drains server CPU via repeated renegotiation.

## What it's for

thc-ssl-dos stress-tests SSL/TLS-enabled services by opening many parallel
connections and repeatedly forcing processor-intensive RSA operations (SSL
handshakes and renegotiations) on the server side — the CPU-exhaustion
weakness THC publicized in 2011. Use it against your own TLS endpoints to
verify how they degrade under handshake load and whether mitigations (TLS
1.3, renegotiation limits, rate limiting) hold. The packaged build's
interface is `thc-ssl-dos [-h] [-l <n>] [--accept] [--skip-delay] <ip>
<port>`, with 400 parallel connections by default (maximum 999). For
authorized testing only.

## Typical use cases

### Stress a TLS endpoint with default parallelism

Run against your own HTTPS server to measure handshake-throughput collapse.

```
thc-ssl-dos 198.51.100.10 443
```

Outcome: opens up to 400 parallel TLS connections to the target and keeps
issuing handshake/renegotiation requests, printing running statistics
(connections established, renegotiations, errors). Server load climbs;
stop with Ctrl+C.

### Raise the parallel connection limit

Push closer to the target's limit when defaults do not saturate it.

```
thc-ssl-dos -l 700 198.51.100.10 443
```

Outcome: same flood with up to 700 concurrent connections (the `-l` ceiling
is 999), typically visible as sharply rising CPU on the TLS front end until
interrupted.

### Print the packaged build's usage

Confirm the exact flag set of this build.

```
thc-ssl-dos -h
```

Outcome: prints the usage summary — `-h` for help and `-l <n>` to limit
parallel connections — plus the `<ip> <port>` argument contract.

*No corpus evidence — derived from upstream documentation.*
