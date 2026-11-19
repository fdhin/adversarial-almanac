---
name: "ike-scan"
categories:
  - blackarch-scanner
version: "1.9.5"
homepage: "http://www.nta-monitor.com/tools/ike-scan/"
packaged-by: [blackarch, kali, aur, nixpkgs]
blackarch-builddate: "2024-11-07"
build-system: autotools
license: GPL
meta-checked: "2026-11-09"
github-repo: "royhills/ike-scan"
github-stars: 423
github-pushed: "2024-09-15"
github-archived: false
github-issues: 24
github-match: "search"
gh-checked: "2026-11-07"
worth: 3.7
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# ike-scan

> Roy Hills' IKE/IPsec VPN discovery and fingerprinting tool: finds IKE servers, identifies them from vendor IDs and retransmission backoff patterns, and captures Aggressive Mode handshakes for offline pre-shared-key cracking.

*Usage grounded in corpus command-line evidence (plain discovery scans); the fuller flag set verified against the upstream README.*

## What it's for

ike-scan speaks the Internet Key Exchange protocol used to set up IPsec VPNs. It sends IKE Main Mode (or Aggressive Mode) SA proposals to target hosts and listens for responses, which tells you an IKE service is live and yields a fingerprint: the implementation's vendor ID payloads, its supported transforms (by proposing custom ones with `--trans`), proprietary notify codes (e.g. Check Point's 9101), and its retransmission backoff pattern (`--showbackoff`), which differs between implementations. Because ike-scan never completes the handshake, many servers never log the probe. Its second role is credential exposure: an Aggressive Mode exchange with PSK authentication puts the hash on the wire, so ike-scan can capture it and the bundled `psk-crack` utility can attack it offline with wordlists or brute force. It does not test for IKE vulnerabilities beyond what fingerprinting reveals.

## Typical use cases

### Discover and fingerprint IKE servers on a network

```
ike-scan 198.51.100.0/24
```

Outcome: Sends Main Mode SA proposals to every host and prints each responder with its IKE handshake details and any recognized vendor ID strings, bracketed by `Starting ike-scan ... with N hosts` / `Ending ike-scan: N hosts scanned in T seconds` lines. Single hosts, ranges (`198.51.100.1-198.51.100.254`) and `--file=hostlist.txt` all work as input.

### Fingerprint by retransmission backoff pattern

```
cat hostlist.txt | ike-scan --file=- --showbackoff=20
```

Outcome: After the normal scan, records and prints each responder's retransmission timing table (waiting 20 seconds instead of the default 60 for stragglers) — the backoff pattern is matched against known implementations to identify the VPN product.

### Probe Aggressive Mode with a custom transform

```
ike-scan --aggressive --trans=5,2,1,2 --id=user@target.example.com 198.51.100.10
```

Outcome: Sends an Aggressive Mode proposal with transform 3DES/SHA1/PSK/DH2 and the given identity; a responding server returns the full handshake including the PSK hash, which the bundled `psk-crack` can attack offline. `--trans` accepts repeated, fully numeric proposals (`encryption/keylen,hash,auth,dhgroup`) and `--idtype`/`--dhgroup` tune the exchange.

### Check whether only port-500 sources get answers

```
ike-scan --sport=500 --nodns 198.51.100.10
```

Outcome: Probes with source port 500 (some implementations, e.g. Windows 2000, only respond to requests from port 500, while others such as Check Point accept any source port) and skips DNS resolution of targets (`--nodns`) for faster runs against pure IP lists.
