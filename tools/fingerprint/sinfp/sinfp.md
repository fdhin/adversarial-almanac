---
name: "sinfp"
categories:
  - blackarch-fingerprint
version: "1.24"
homepage: "https://cpan.metacpan.org/authors/id/G/GO/GOMOR/"
packaged-by: [blackarch]
blackarch-builddate: "2025-07-18"
build-system: perl
license: PerlArtistic
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# sinfp

> Perl OS-stack fingerprinting suite (Net::SinFP3) that identifies an operating system from TCP/IP stack behaviour, actively or passively, over IPv4 or IPv6.

## What it's for

SinFP3 fingerprints the operating system behind a TCP port by sending probe sequences and matching the responses against a SQLite signature database (`sinfp3.db`, updatable with `--db-update`). It works active (probe a target's ports), passive (sniff traffic and match signatures offline), or sweep a local LAN via ARP discovery, in IPv4 and IPv6 alike. Matches come back scored, so you can request only the best match or filter by threshold. Results are printed to the console or emitted as CSV/pcap/filtered outputs (OS only, OS+version family). The packaged `sinfp` command is a symlink to upstream's `sinfp3.pl`; it is part of the Net::Frame ecosystem by Patrice "GomoR" Auffret, last released on CPAN in 2018. It does not port-scan for you — SYN-scan inputs aside, you point it at targets and it fingerprints what responds.

## Typical use cases

### Fingerprint a single open port

```
sinfp -target target.example.com -port 80 -input-ipport
```

Outcome: runs the active single-port probe and prints the signature database matches for the responding service — OS identity with match scores.

### Fingerprint a host across its top ports

```
sinfp -target target.example.com -port top100
```

Outcome: SYN-scans the top-100 ports of the target and fingerprints every open one, printing the OS identified per responding port (`-port full` covers all ports).

### Fingerprint a subnet

```
sinfp -target 198.51.100.0/24 -port top100
```

Outcome: SYN-scans the whole /24, then fingerprints each discovered open port; `-jobs N` bounds the number of parallel jobs (default 10).

### Identify systems passively from live traffic

```
sinfp -mode-passive -search-active -input-sniff
```

Outcome: sniffs traffic on the default interface and matches observed TCP signatures as flows pass by, fingerprinting systems without sending any probe; `-6` enables IPv6 matching.

### Discover and fingerprint the local LAN

```
sinfp -input-arpdiscover
```

Outcome: ARP-discovers neighbors on the local segment, then actively fingerprints each live host found; `-6` runs the IPv6 (NDP) variant.

### Fingerprint over IPv6

```
sinfp -target target.example.com -port 80 -input-ipport -6
```

Outcome: same single-port active probe resolved and sent over IPv6, matched against the IPv6 signature set.

### Keep only the strongest match

```
sinfp -target 198.51.100.10 -port 22 -input-ipport --best-score --threshold 90
```

Outcome: gathers results only for the best-matching signature(s), with the score floor applied where the plugin supports it, cutting noisy near-miss candidates from the output.

*No corpus evidence — derived from upstream documentation.*
