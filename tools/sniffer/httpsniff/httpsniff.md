---
name: "httpsniff"
categories:
  - blackarch-sniffer
version: "0.4"
homepage: "https://www.sump.org/projects/httpsniff/"
packaged-by: [blackarch]
blackarch-builddate: "2026-09-14"
build-system: make
license: GPL-1.0-or-later
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# httpsniff

> Capture HTTP responses off the wire and save the files they contain for later review.

## What it's for

httpsniff (from sump.org) sniffs HTTP responses on TCP/IP networks, reassembles the TCP streams, and writes every file the responses carry — images, documents, downloads — to local disk for later review. Despite the name it handles plain HTTP, not TLS: it cannot decrypt or record HTTPS payloads. Stream reassembly uses libnids by default, or the tool's own relaxed TCP/IP stack (`-x`, upstream calls it unsafe). Files are written into the current working directory: by default as sequentially numbered files with a detected extension (e.g. `000001.jpg`), or with `-u` in a directory tree mirroring the requested URL path (also flagged unsafe, since it writes attacker-influenced paths). It is an old, small C tool (libpcap, libnids, libnet, glib) — run it from a dedicated empty directory so everything it pulls is easy to collect, and mind disk usage on busy links.

## Typical use cases

### Save transferred files from live traffic

```
mkdir pulled && cd pulled && sudo httpsniff eth0
```

Outcome: every file inside HTTP responses seen on eth0 is written to the `pulled` directory as a numbered file with its detected extension; verbose logging (`-v`) names each file as it is created.

### Restrict collection to one host

```
sudo httpsniff -i eth0 'src host 198.51.100.10'
```

Outcome: only HTTP responses originating from 198.51.100.10 are processed — `-i` forces the argument to be read as an interface, and the tcpdump-style filter scopes the capture.

### Organize pulled files by URL

```
sudo httpsniff -u -i eth0
```

Outcome: saved files are placed in a directory structure mirroring the request URLs, recreating the site layout on disk; upstream marks this mode unsafe because server-controlled path strings become local paths.

### Mine an existing capture file

```
httpsniff -f capture.pcap
```

Outcome: extracts files from HTTP responses recorded in the pcap (`-f` forces the argument to be treated as a dump file), with no capture privileges required — useful for post-processing an incident capture.

*No corpus evidence — derived from upstream documentation.*
