---
name: "proxyp"
categories:
  - blackarch-proxy
version: "2013"
homepage: "http://sourceforge.net/projects/proxyp/"
packaged-by: [blackarch]
blackarch-builddate: "2025-07-18"
build-system: perl
license: GPL3
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# proxyp

> Multithreaded Perl utility (2013) that harvests proxy IPs from files or web pages, checks which are alive, and enumerates open proxy ports, server banners and geolocation.

## What it's for

proxyp (by ArchSeptem "SevenWolf") automates the tedious part of working with free proxy lists: it ingests IP addresses either from a file or by crawling a page that lists them, checks liveness with a configurable thread count and response-time cutoff, and can optionally probe each live address across a pool of typical proxy ports for server banners and IP geolocation. Results print to the terminal and can be written to a fresh file or appended to an existing one — the author's own examples append straight into a proxychains config. It is a single Perl script (installed as `/usr/bin/proxyp` on BlackArch) needing LWP, Net::Ping and IP::Country.

## Typical use cases

### Harvest and test proxies from a listing page

```
proxyp -i http://example.com/proxy-list -e -t 20
```

Outcome: the page is crawled for IP addresses, each candidate is checked with 20 threads, and live addresses are reported on the terminal.

### Filter a proxy file down to fast, live entries

```
proxyp -f proxies.txt -e -d 0.5 -t 50 -o live.txt
```

Outcome: entries of `proxies.txt` that respond within half a second are kept and written (without statistics) to `live.txt`, ready to use as a working proxy list.

### Enumerate ports, banners and geo per live host

```
proxyp -f proxies.txt -e -z -c RU
```

Outcome: live hosts geolocated in Russia only are probed across the default port pool (3128, 80, 8080, 1080 — extend it with `-p`), collecting server banners and country data per address; this pass is slow, so `-b` skips banner grabbing when speed matters.

### Append verified proxies into a proxychains config

```
proxyp -f candidates.txt -e -d 1 -a /etc/proxychains.conf
```

Outcome: live proxies discovered in `candidates.txt` are appended to the given file so the next proxychains run can egress through them.

*No corpus evidence — derived from upstream documentation (flags verified against the packaged script's own usage text).*
