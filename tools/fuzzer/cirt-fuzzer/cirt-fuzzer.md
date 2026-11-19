---
name: "cirt-fuzzer"
categories:
  - blackarch-fuzzer
version: "1.0"
homepage: "http://www.cirt.dk/"
packaged-by: [blackarch]
blackarch-builddate: "2025-07-18"
build-system: perl
license: GPL
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# cirt-fuzzer

> A template-driven TCP/UDP protocol fuzzer in Perl by Dennis Rand (CIRT.DK).

## What it's for

The CIRT fuzzer sends generated data at a single host and port and watches how
the service copes. You write a template file holding the request you want to
fuzz, with a `<FUZZER>` tag marking the injection point, optional
`<COUNT>...<COUNT>` regions and a `<SIZE>` tag that expands to the current
payload length — with `\xNN` hex bytes supported, so binary protocols are
workable. The fuzzer then substitutes growing payloads: the built-in overflow
string table ("A" x 33 up to "A" x 75536) with `-type string`, format-string
values with `-type format`, or steadily growing data between `-minimum` and
`-maximum` with `-type special`, over TCP or UDP, optionally wrapped in SSL.
It is a 2006-era, single-purpose script: purely blackbox crash discovery with
no coverage feedback or crash triage, and by design it is slow.

## Typical use cases

### Print the built-in usage

```
cirt-fuzzer -help
```

Outcome: prints the warning banner plus every option with a one-line
description (`-host`, `-port`, `-protocol`, `-timeout`, `-delay`, `-wait`,
`-load`, `-ssl`, `-noupdate`, `-type`, `-minimum`, `-maximum`).

### Fuzz a field in a text-protocol request

Grow classic overflow strings through the `<FUZZER>` tag of a template.

```
cirt-fuzzer -host 198.51.100.10 -port 80 -type string -load /usr/share/cirt-fuzzer/template.txt
```

Outcome: after the banner, the fuzzer walks its overflow-string table ("A"
x 33, 254, 255, 511, ... 65536, 75536), sending one request per size with the
payload substituted at `<FUZZER>` and `<SIZE>` replaced by the current
payload length; watch the target service for crashes, hangs and resets.

### Fuzz a binary or UDP service with growing payloads

Use incrementing sizes instead of the fixed string table.

```
cirt-fuzzer -host 198.51.100.10 -port 69 -protocol udp -delay 1 -type special -minimum 100 -maximum 5000 -load template.txt
```

Outcome: sends requests whose `<FUZZER>` content grows from 100 to 5000 bytes,
one attempt per size with a one-second pause between attempts, over UDP —
useful for length-field handling faults in simple binary services.

### Fuzz an SSL-wrapped service

```
cirt-fuzzer -host 198.51.100.10 -port 443 -ssl -type string -load /usr/share/cirt-fuzzer/template.txt
```

Outcome: the same overflow-string sequence as the plain run, but each
connection is tunneled through SSL, so TLS-fronted services can be fuzzed
without terminating the encryption yourself.

*No corpus evidence — derived from upstream documentation.*
