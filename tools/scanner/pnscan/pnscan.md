---
name: "pnscan"
categories:
  - blackarch-scanner
version: "1.14.1"
homepage: "http://www.lysator.liu.se/~pen/pnscan/"
packaged-by: [blackarch]
blackarch-builddate: "2021-06-23"
build-system: autotools
license: GPL2
meta-checked: "2026-11-09"
github-repo: "ptrrkssn/pnscan"
github-stars: 132
github-pushed: "2020-06-19"
github-archived: false
github-issues: 0
github-match: "search"
gh-checked: "2026-11-07"
worth: 2.5
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# pnscan

> Multi-threaded TCP service surveyor: parallel port scanning with request/response matching.

## What it's for

pnscan (Peter Eriksson) is a parallel TCP port scanner for surveying IPv4 network services. Beyond open/closed detection it can transmit a request string (`-w`, or hex via `-W`) and match the response (`-r`/`-R`), turning a port scan into a service census — SSH, FTP, SMTP and web banners — in one pass. Targets are either a CIDR/host-range plus port-range on the command line, or a stream read from stdin for pipeline use; thread count auto-scales up to the `-n` limit. IPv6 is not supported.

## Typical use cases

### Survey a subnet for open ports

Fast census of a /24 with name resolution.

```
pnscan -s 198.51.100.0/24 21:25
```

Outcome: scans the subnet for the port range, printing each open port with the resolved hostname (`-s`) and any data the service sends on connect — one line per finding. The port spec is a single range (`21:25`), a single port/service name, or the literal `all` for 1:65535.

### Banner-grab with a request and response filter

Find specific servers by their reply.

```
pnscan -l -w"HEAD / HTTP/1.0\r\n\r\n" -r"Server:" 198.51.100.0/24 80
```

Outcome: after connecting, pnscan transmits the request string (escapes like `\r\n` are decoded by pnscan itself), and only hosts whose response contains `Server:` are reported — with `-l`, output is trimmed to the matched line — a one-pass web-server inventory.

### Scan targets piped on stdin

Feed pnscan from another tool.

```
cat targets.txt | pnscan -n 64 22
```

Outcome: pnscan reads one host per line from stdin and probes the single given port (used as the default for lines without one) with at most 64 concurrent workers — pairs naturally with log parsers or CIDR expanders upstream.

### Match a binary protocol signature

Probe non-ASCII services with hex probes.

```
pnscan -W"05 5A 37" -R"FF 00 FF" 198.51.100.10 145
```

Outcome: the hex-coded request bytes are sent to each connected port and only responses containing the hex response signature are printed — usable for embedded/SCADA protocols that don't speak text.

No corpus evidence — derived from upstream documentation (pnscan.1 man page and the ptrrkssn/pnscan README examples).
