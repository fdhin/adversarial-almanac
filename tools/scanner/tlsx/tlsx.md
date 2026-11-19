---
name: "tlsx"
categories:
  - blackarch-scanner
version: "v1.4.2.r2.g1e46e32"
homepage: "https://github.com/projectdiscovery/tlsx"
packaged-by: [blackarch, aur, nixpkgs]
blackarch-builddate: "2026-11-01"
build-system: go
license: MIT
meta-checked: "2026-11-09"
github-repo: "projectdiscovery/tlsx"
github-stars: 1158
github-pushed: "2026-11-06"
github-archived: false
github-issues: 7
github-match: "exact"
gh-checked: "2026-11-07"
worth: 7.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 83
gh-activity-checked: "2026-11-09"
---

# tlsx

> Fast TLS data collection: certificates, versions, ciphers and fingerprints for one host or a large list.

## What it's for

tlsx is ProjectDiscovery's TLS grabber and analysis toolkit: it connects to TLS services and extracts certificate data (SANs, common name, organization, serial, fingerprint hashes), the negotiated TLS version and cipher, JARM and JA3 fingerprints, and flags certificate misconfigurations (expired, self-signed, mismatched, revoked, untrusted). It takes single hosts or list files, arbitrary ports, IPv4/IPv6 selection, socks5 proxying, and multiple scan engines (`ctls`, `ztls`, `openssl`, or `auto`). Output is line-oriented or JSONL, built to feed automation pipelines; it collects and analyzes TLS data rather than attacking it.

## Typical use cases

### Grab certificate names for a host

```bash
tlsx -u example.com -san -cn -silent
```

Outcome: prints the subject alternative names and common name from the certificate — the standard first step for expanding a target's hostname surface.

### Filter a host list for misconfigured certificates

```bash
tlsx -l hosts.txt -p 443,8443 -ex -ss -mm -un
```

Outcome: only hosts whose certificates are expired, self-signed, mismatched or untrusted are printed, one per line, ready to triage.

### Enumerate supported TLS versions and ciphers

```bash
tlsx -u example.com -ve -ce -ct insecure,weak
```

Outcome: lists every TLS version the endpoint accepts and its cipher set, tagging insecure and weak ciphers — direct evidence for hardening findings.

### Fingerprint many endpoints with JARM

```bash
tlsx -l hosts.txt -jarm -j -silent
```

Outcome: JSON lines carrying the JARM hash per host, suitable for clustering identical TLS stacks or matching a known server fingerprint across an estate.

### Probe every IP behind a hostname, both families

```bash
tlsx -u example.com -sa -iv 4,6
```

Outcome: all resolved addresses are scanned over IPv4 and IPv6, exposing per-IP differences such as a certificate served on only one address family.

*No corpus evidence — derived from upstream documentation (projectdiscovery/tlsx README usage and full flag reference).*
