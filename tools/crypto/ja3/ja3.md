---
name: "ja3"
categories:
  - blackarch-crypto
version: "117.cb29184"
homepage: "https://github.com/salesforce/ja3"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-13"
build-system: python
license: BSD
meta-checked: "2026-11-09"
github-repo: "salesforce/ja3"
github-stars: 3097
github-pushed: "2025-05-01"
github-archived: true
github-issues: 34
github-match: "exact"
gh-checked: "2026-11-07"
worth: 0.6
worth-basis: "historical (archived/gone upstream)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# ja3

> TLS client fingerprinting method and tooling: MD5 hashes of Client Hello parameters.

## What it's for

JA3 is a standard method for profiling SSL/TLS clients: it concatenates the decimal values of the Client Hello's SSL version, cipher list, extensions, elliptic curves and elliptic-curve point formats into a string (`SSLVersion,Cipher,SSLExtension,EllipticCurve,EllipticCurvePointFormat`), then MD5-hashes it into a shareable 32-character fingerprint. Because the fingerprint characterizes the client application rather than its IPs or domains, it detects malware C2 that rotates infrastructure (published examples include Tor, Trickbot and Emotet fingerprints) and supports whitelisting of allowed clients in locked-down environments. This repository — now archived upstream, with the original creator maintaining the successor JA4 at FoxIO-LLC — ships a Python `ja3` command that extracts fingerprints from PCAP/PCAPng captures, Zeek scripts, and lists of known JA3 values. JA3S applies the same idea to the Server Hello, so JA3+JA3S together fingerprint the whole cryptographic negotiation.

## Typical use cases

### Extract JA3 fingerprints from a packet capture

```
ja3 tls-capture.pcap
```

Outcome: reads the PCAP (PCAPng also accepted), parses every TLS Client Hello on port 443 and prints one line per hello in the form `[<destination_ip>:<port>] JA3: <field string> --> <md5 digest>`.

### Fingerprint Client Hellos on non-standard ports

```
ja3 -a tls-capture.pcap
```

Outcome: same output as above, but Client Hellos on any TCP port are processed instead of only port 443 — useful for captures of malware or TLS services on alternate ports.

### Produce JSON records for downstream tooling

```
ja3 -j tls-capture.pcap
```

Outcome: prints a pretty-printed JSON array with one record per Client Hello (IPs, ports, JA3 string and digest) for downstream parsing; adding `-r` also includes the raw Client Hello packet data for research use.

*No corpus evidence — derived from upstream documentation.*
