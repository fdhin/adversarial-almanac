---
name: "cisco-scanner"
categories:
  - blackarch-cracker
version: "0.2"
homepage: "http://wayreth.eu.org/old_page/"
packaged-by: [blackarch]
blackarch-builddate: "2025-12-04"
build-system: other
license: custom:unknown
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# cisco-scanner

> Multithreaded finder for Cisco routers whose HTTP server hands out privileged exec pages without authentication.

## What it's for

cisco-scanner (v0.2, by Inode) scans CIDR ranges for the classic 2001-era Cisco IOS HTTP server vulnerability where requesting `/level/16/exec/...` returns a privileged exec page with no authentication. A host counts as vulnerable only if HTTP (port 80) answers `200 OK` with Cisco content to `GET /level/16/exec/-///pwd` and telnet or SSH is also reachable (used as a management-port liveness filter). With `-f` it goes further and pulls the router's configuration through `/level/16/exec/-///show/configuration`, saving it per host. It targets exactly this one vulnerability class on ancient IOS — it is not a general Cisco audit tool, and it does not attempt any login.

## Typical use cases

### Scan a subnet and collect vulnerable hosts
```
cisco-scanner -h 198.51.100.0/24 -o results.txt
```
Outcome: probes every host in the CIDR concurrently and appends each vulnerable host as an `IP: a.b.c.d` line to `results.txt`, giving a clean list of findings; the scan summary (start/end IP, thread count) goes to stderr.

### Dump configurations from vulnerable routers
```
cisco-scanner -h 198.51.100.0/24 -f
```
Outcome: for each vulnerable host additionally requests the running configuration over the exec URL and saves it as `<IP>.conf` (e.g. `198.51.100.10.conf`) in the current directory — the config includes enabled services and any inline keys.

### Tune threads, timeout, and visibility
```
cisco-scanner -h 198.51.100.0/24 -c 50 -t 3 -v -o results.txt
```
Outcome: raises concurrency to 50 threads and shortens the connect timeout to 3 seconds; `-v` prints per-host progress (`Connecting to: ... on port: 80`, `Fetch configuration of: ...`) to stderr while findings still land in the output file.

*No corpus evidence — derived from upstream documentation.*
