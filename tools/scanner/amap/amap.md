---
name: "amap"
categories:
  - blackarch-scanner
version: "5.4"
homepage: "http://freeworld.thc.org/releases.php"
packaged-by: [blackarch]
blackarch-builddate: "2026-02-03"
build-system: autotools
license: GPL-2.0-or-later, custom
meta-checked: "2026-11-09"
github-repo: "BlackArch/amap"
github-stars: 15
github-pushed: "2013-11-09"
github-archived: false
github-issues: 1
github-match: "exact"
gh-checked: "2026-11-09"
worth: 1.9
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# amap

> THC's Application MAPper: identifies which application protocol really runs on a port by sending trigger packets and matching the responses.

## What it's for

amap (van Hauser and DJ RevMoon, The Hacker's Choice) fingerprints services even when they
sit on unexpected ports: it sends protocol-specific trigger packets and compares responses
against the `appdefs.trig`/`appdefs.resp` signature databases installed in
`/usr/share/amap/etc/`. Besides the default identification mode (`-A`) it can just grab
banners (`-B`) or act as a full-connect port scanner (`-P`), over TCP or UDP, and it can
read the open ports straight out of an nmap machine-readable output file. The upstream
README itself now states the tool is outdated and recommends nmap, whose service probes
cover the same ground — it remains packaged and working,
and its bundled companion `amapcrap` elicits responses from silent services so new
signatures can be added to the databases. It does protocol/application identification
only, not vulnerability checking.

## Typical use cases

### Fingerprint the service on a known port

Identify what actually runs behind a port, not what its number suggests.

```
amap -bqv 198.51.100.10 443
```

Outcome: sends the trigger set to the port and reports identified application protocols
(e.g. SSL, http); `-b` prints the ASCII banner, `-q` suppresses closed ports, `-v` shows
progress — the option combination the usage hint recommends.

### Identify services across a port range

Follow up a port sweep with protocol identification.

```
amap -bq 198.51.100.10 21-6000
```

Outcome: walks the port range and prints per-port identified applications; add `-1` to
stop probing each port after its first positive identification for speed.

### Banner-grab without sending triggers

Passive-touch reconnaissance against fragile services.

```
amap -B 198.51.100.10 25 465 587
```

Outcome: connects and captures only what the services volunteer, printing raw banners —
no trigger packets that could break odd handshakes.

### Fingerprint a UDP service

Identify protocols that live on UDP.

```
amap -u -bqv 198.51.100.10 161
```

Outcome: `-u` switches the port arguments to UDP, and amap matches responses against its
UDP trigger signatures (SNMP and similar).

### Follow up an nmap sweep

Fingerprint everything nmap already found.

```
amap -i results.nmap -o results.amap -m
```

Outcome: reads the open ports from nmap's machine-readable output (`-i`), fingerprints
each, writes the report to `results.amap` and, with `-m`, a machine-readable copy for
scripting.

### Elicit a response from an unidentified service

Grow the signature database for unknown protocols.

```
amapcrap -m ab 198.51.100.10 12345
```

Outcome: the bundled `amapcrap` sends random letters and binary garbage to the silent
port until something answers; the response it draws out can be turned into new
`appdefs.trig`/`appdefs.resp` entries.

*No corpus evidence — derived from upstream source (BlackArch mirror of THC amap 5.4; flags verified against `usage()` in `amap.c` and `amapcrap.c`).*
