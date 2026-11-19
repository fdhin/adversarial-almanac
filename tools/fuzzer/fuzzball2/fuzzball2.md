---
name: "fuzzball2"
categories:
  - blackarch-fuzzer
version: "0.7"
homepage: "http://nologin.org/"
packaged-by: [blackarch]
blackarch-builddate: "2025-12-20"
build-system: make
license: custom
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# fuzzball2

> Tiny C fuzzer by warlord (nologin.org) that floods a host with malformed IP and TCP options.

## What it's for

fuzzball2 is a single-file C program (2007) that sends handcrafted raw packets with bogus IP and TCP option fields to a target host, exercising how its network stack parses option kinds, lengths, and padding. It iterates over combinations of option bytes, lengths, and padding values from built-in tables, so the number of packets is finite and deterministic rather than random. Five modes cover TCP options, IP options, ICMP payloads embedding a nested IP header with weird options, UDP payloads with weird IP options, and ICMP echo requests with weird IP options. It is a one-way packet blaster with no state engine, no listening, and no crash detection — you watch the target (dmesg, console, tcpdump) for failures. It needs root to open raw sockets and is compiled from a 2007 tarball preserved on the Wayback Machine.

*No corpus evidence — derived from upstream documentation.*

## Typical use cases

### Fuzz IP option parsing

Send packets with malformed IP options to a target host and port.

```
fuzzball2 198.51.100.10 -p 4662 -m 1
```

Outcome: raw packets with systematically varied IP option bytes, lengths, and padding are sent to the host; a progress readout tracks which index of the option tables is in flight. Watch the target's kernel logs or console for panics, resets, or dropped interfaces while it runs.

### Fuzz TCP option parsing

Do the same against a listening TCP service.

```
fuzzball2 198.51.100.10 -p 80 -m 0
```

Outcome: TCP segments with corrupt option fields are delivered to the service on port 80; failures appear as resets, hangs, or crashes in the target stack rather than in fuzzball2's own output.

### Resume or bound a run by packet index

Restrict the sweep to a range of table indices, e.g. to re-test a suspicious region or split the sweep.

```
fuzzball2 198.51.100.10 -m 4 -s 100 -h 200
```

Outcome: only indices 100 through 200 of the ICMP-echo-with-weird-IP-options tables are generated; `-s`/`-h` also let a second machine or a later run continue where a previous one stopped without repeating the whole sweep.
