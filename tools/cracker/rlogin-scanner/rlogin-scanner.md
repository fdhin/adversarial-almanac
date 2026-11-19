---
name: "rlogin-scanner"
categories:
  - blackarch-cracker
version: "0.2"
homepage: "http://wayreth.eu.org/old_page/"
packaged-by: [blackarch]
blackarch-builddate: "2015-09-07"
build-system: other
license: custom:unknown
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# rlogin-scanner

> Multithreaded rlogin sweep that flags hosts accepting a passwordless root-to-root login.

## What it's for

Scans a single IP or an entire CIDR for the rlogin service (TCP 513) and attempts a root-to-root login with no password on every host where the port is open. It is a finder for legacy Unix exposure: a host whose rlogin daemon accepts root/root without a password is listed as a bare `IP:` line, while hosts that ask for a password, refuse root with "not on system console", or return something unexpected are categorized separately in the output. Targets are given as dotted-quad IPs or a `ip/prefix` range (no hostnames — the address is parsed with inet_addr), defaults are 20 threads and a 5-second timeout, and results go to stdout unless `-o` names a file. It tests only the root/root pair — no other credentials and no other services.

## Typical use cases

### Sweep a subnet for open rlogin

Find legacy hosts that still run rlogin and accept root without a password.

```
rlogin-scanner -h 198.51.100.0/24
```

Outcome: scans all 256 addresses in the range with 20 threads and writes one line per responding host to stdout — a bare `IP: 198.51.100.10` means the root/root login succeeded; other hosts are annotated `require password`, `NOT ON SYSTEM CONSOLE`, or `did a strange reponse` with the raw reply.

### Tune the sweep and log results

More threads, longer timeout, results to a file.

```
rlogin-scanner -h 198.51.100.0/24 -c 60 -t 10 -o rlogin-results.txt -v
```

Outcome: runs 60 threads with a 10-second per-connection timeout, appends the categorized results to `rlogin-results.txt`, and with `-v` also traces the run on stderr (start/end IP of the range, connection progress, "Scan end" when finished).

### Check a single host

Probe one specific server.

```
rlogin-scanner -h 198.51.100.10
```

Outcome: probes the one address; nothing is printed if port 513 is closed or filtered, otherwise the categorized rlogin response for that host appears in the output stream.

*No corpus evidence — derived from upstream documentation.*
