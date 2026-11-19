---
name: "ntp-ip-enum"
categories:
  - blackarch-recon
version: "0.1"
homepage: "http://www.securepla.net/"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-25"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# ntp-ip-enum

> Pulls the list of client IPs an NTP server has talked to, via the mode-7 monlist query.

## What it's for

ntp-ip-enum (SensePost's Gert Burger, edited/packaged via securepla.net; BlackArch installs the Python 2 script as `/usr/bin/ntp-ip-enum`) sends an NTP mode 7 "monlist" request (request code 42) to one or more NTP servers on UDP/123 and extracts every client address from the returned monitor records. Because the monitor list is the server's memory of who recently queried it, the harvested IPs are that server's clients — a way to enumerate infrastructure behind an NTP server (internal hosts, other resolvers) without touching those hosts directly. The script deduplicates across all servers queried, sorts the result, and appends it to `NTP.txt`; a source-level `OUTPUT_FORMAT = 'maltego'` constant switches printing to a Maltego XML entity set for import as a transform result. It is a Python 2 relic of the pre-2014 era: monlist was the vector behind the CVE-2013-5211 reflection attacks, and modern ntpd restricts or disables it by default (`noquery`/`notrust`), so most servers now answer with nothing. Related: [[ntpdos]] abuses the same monlist response for amplification.

## Typical use cases

### Harvest a single server's client list

Enumerate who has been talking to one NTP server.

```
ntp-ip-enum 198.51.100.10
```

Outcome: sends monlist requests (3 tries, 2 s read timeout), parses the returned monitor records, and prints "Completed. Check NTP.txt" — `NTP.txt` in the working directory gains a block headed "Target host:" listing the unique client IPs seen by that server; an empty result means the server restricts mode-7 queries.

### Merge clients across several servers

Build a combined client inventory from a set of NTP servers.

```
ntp-ip-enum 198.51.100.10 198.51.100.11 198.51.100.12
```

Outcome: queries each server in turn, unions all monitor-record addresses, and writes the deduplicated, numerically sorted set to `NTP.txt` — useful for spotting which hosts depend on multiple time servers in an enumerated range.

*No corpus evidence — derived from upstream documentation (interface and behavior read directly from the packaged ntp_ip_enum script: positional targets, NTP.txt writer, monlist parser).*
