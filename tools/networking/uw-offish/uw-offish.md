---
name: "uw-offish"
categories:
  - blackarch-networking
version: "0.1"
homepage: "http://uberwall.org/bin/download/42/UW_offish.1.tar.gz"
packaged-by: [blackarch]
blackarch-builddate: "2015-09-06"
build-system: make
license: custom:unknown
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# uw-offish

> ÜberWall's clear-text protocol simulator (khorben, 2006) — a tiny daemon that listens on the ports named in a config file and answers each line it receives with canned lines, to bait credentials or probe traffic on a honeypot network.

*No corpus evidence — derived from archived upstream packaging sources (the original site is gone; the packaged README and binary are the source).*

## What it's for

Faking open services cheaply. UW_offish reads a configuration file that maps service names to listening ports and canned line-by-line responses, then sits single-threaded waiting: for every line a client sends, it replies according to the file. The author's stated uses are a rogue "open" wireless access point — redirect all traffic to this program so that credential-hungry clients (mail clients, FTP clients, telnet) happily type passwords at it while a protocol analyzer watches — and honeypots, where it can occupy an attacking scanner without exposing real services. It does no protocol parsing beyond line matching, supports no transparent-proxy mode (listed as unimplemented), and its configuration file format is undocumented in the surviving material: there is no live upstream to consult.

## Typical use cases

### Start a simulated service

```
uw-offish telnet
```

Outcome: attempts to load protocol definitions from `UWoffish.conf` in the current directory and, if the file is missing, exits immediately with `UWoffish: UWoffish.conf: No such file or directory`; with a valid file it starts listening for the named service. That error is the quick way to confirm which config path it expects before building the file.

### Load an explicit definitions file

```
uw-offish -f /opt/uw-offish/UWoffish.conf ftp smtp
```

Outcome: loads the named definitions file and brings up listeners for the `ftp` and `smtp` services defined inside it, responding to each client line with the configured replies — the bait endpoint for a lab access point or honeypot segment, to be paired with a sniffer on the same segment.
