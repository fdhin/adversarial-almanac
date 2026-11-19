---
name: "ycrawler"
categories:
  - blackarch-webapp
version: "0.1"
homepage: "http://packetstormsecurity.com/files/98546/yCrawler-Web-Crawling-Utility.html"
packaged-by: [blackarch]
blackarch-builddate: "2025-07-18"
build-system: perl
license: GPL2
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# ycrawler

> Perl crawler that maps every user-supplied input point (GET/POST) of a site, with proxy and log-file support.

## What it's for

yCrawler (by Giovanni Buzzin, "Osirys"; distributed via PacketStorm) crawls a website and records all user-supplied input it encounters — links carrying GET parameters and form POSTs — so you get a map of where input enters the application before attacking it. The crawl can be limited to the exact site/domain or extended to all its subdomains, results can be stored to a log file, and requests can be routed through an HTTP proxy. It is a single-script Perl era tool (the distro package installs `yCrawler.txt` as the `ycrawler` command) with a fixed positional argument order rather than GNU-style flags.

## Typical use cases

### Crawl a single domain and log all inputs

```
ycrawler target.example.com -m lim -l crawl.log -p n
```

Outcome: crawls only `target.example.com` (no subdomain expansion), writes every discovered GET/POST input point to `crawl.log`, and runs without a proxy (`-p n`).

### Expand the crawl to all subdomains

```
ycrawler example.com -m all -l crawl.log -p n
```

Outcome: the crawler also follows and enumerates the site's subdomains, mapping input points across the whole domain.

### Crawl through a proxy without logging

```
ycrawler target.example.com -m lim -l n -p 127.0.0.1:8080
```

Outcome: every crawl request is sent through the proxy at `127.0.0.1:8080` (e.g. Burp) so traffic is inspectable, and nothing is written to disk (`-l n`).

*No corpus evidence — derived from the packaged upstream source (argument parsing of yCrawler.txt in the PacketStorm tarball BlackArch builds).*
