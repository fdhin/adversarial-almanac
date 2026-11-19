---
name: "tls-fingerprinting"
categories:
  - blackarch-fingerprint
version: "257.4b6e878"
homepage: "https://github.com/LeeBrotherston/tls-fingerprinting"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-26"
build-system: make
license: GPL3
meta-checked: "2026-11-09"
github-repo: "LeeBrotherston/tls-fingerprinting"
github-stars: 399
github-pushed: "2020-10-20"
github-archived: true
github-issues: 14
github-match: "exact"
gh-checked: "2026-11-07"
worth: 0.6
worth-basis: "historical (archived/gone upstream)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# tls-fingerprinting

> Lee Brotherston's TLS client fingerprinting toolkit: `fingerprintls` detects and creates TLS fingerprints live on the wire or from pcap, and `fingerprintout` exports the database to Suricata/Snort rules, C structs and other formats.

## What it's for

Identifying which TLS *client* implementation initiated a connection — a specific browser, a malware sample's SSL library, a Tor client — from the ClientHello fingerprint rather than the certificate. The `fingerprintls` daemon (built from C sources; the project calls it FingerprinTLS) sniffs a live interface or reads a pcap capture, matches observed TLS ClientHellos against a JSON fingerprint database, and can log unknown fingerprints with their packets for database submission. `fingerprintout` then converts collected fingerprints into detection formats: Suricata/Snort rules (less accurate than live matching and may need tuning), ANSI C structs, binary form, or xkeyscore-style regex. A third helper, `parselog`, cleans up logs. It is passive detection only — it does not send traffic at servers — and the helper scripts are Python 2. The upstream repository is archived, so the shipped fingerprint database is frozen.

## Typical use cases

### Watch for TLS client fingerprints on the wire

Continuously match live TLS sessions against the fingerprint database.

```
sudo fingerprintls -i eth0
```

Outcome: captures ClientHello messages on the interface and prints each session's host pair together with the fingerprint name matched from the database; unknown fingerprints are reported as unmatched.

### Fingerprint clients from an existing capture

Identify TLS clients in traffic you already recorded.

```
fingerprintls -p capture.pcap -l matches.json
```

Outcome: reads the pcap file instead of a live interface and appends each match as a JSON line to the log file, ready for export or database contribution.

### Collect unknown fingerprints for submission

Capture the traffic behind fingerprints the database does not know.

```
sudo fingerprintls -i eth0 -P unknown.pcap
```

Outcome: in addition to normal matching, saves the raw packets of unmatched ClientHellos to `unknown.pcap` so the new fingerprint can be generated from them and contributed upstream; `-j` dumps fingerprints in JSON instead.

### Export fingerprints as IDS rules

Turn collected fingerprints into Suricata/Snort detections.

```
fingerprintout -i matches.json tls.rules
```

Outcome: reads the JSON fingerprint file and writes Snort/Suricata-format rules to `tls.rules`; other formats are `-s` (ANSI C structs), `-x` (xkeyscore regex), `-b` (binary), and `-c` (cleanse the database of duplicates).

*No corpus evidence — derived from upstream documentation.*
