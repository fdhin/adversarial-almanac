---
name: "cryptonark"
categories:
  - blackarch-crypto
version: "0.5.7"
homepage: "http://blog.techstacks.com/cryptonark.html"
packaged-by: [blackarch, aur]
blackarch-builddate: "2025-12-03"
build-system: perl
license: GPL-1.0-only
meta-checked: "2026-11-09"
worth: 0.8
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# cryptonark

> Perl SSL/TLS cipher and certificate scanner for verifying weak-cipher remediation.

## What it's for

CryptoNark (`cnark.pl`, by Chris Mahns of techstacks.com) is an SSL/TLS remediation verification script: it connects to a host and port and reports which ciphers SSLv2, SSLv3, TLSv1 and (with OpenSSL ≥ 1.0) TLSv1.2 accept, color-coding weak findings red — export-grade, NULL, RC4, anonymous ADH/AECDH and DES ciphers, and every SSLv3 cipher since POODLE. Unless `--insecure` is given it first validates the server certificate: chain and hostname match, common name, subject alternative names, expiry date, signature algorithm (pre-SHA-2 flagged red) and public-key size (warns below 2048 bits). It was written for administrators doing before/after testing of SSL/TLS hardening — e.g. checking that NULL, anonymous and weak ciphers are disabled for PCI-DSS — and explicitly not as an attack tool. It needs Perl ≥ 5.10 with CPAN modules (Modern::Perl, Tie::Hash::Indexed, IO::Socket::SSL, Mozilla::CA).

*No corpus evidence — derived from upstream documentation.*

## Typical use cases

### Audit a TLS endpoint's accepted ciphers

```
./cnark.pl -h target.example.com -p 443
```

Outcome: prints the local OpenSSL version, verifies the server certificate (CN, SANs, expiry, signature algorithm, key length), then walks the SSLv2, SSLv3 and TLSv1 cipher lists — plus TLSv1.2 on OpenSSL ≥ 1.0 — printing each accepted cipher green or red; the red lines are the ciphers to disable on the server.

### Scan a service by IP without certificate validation

```
./cnark.pl -h 198.51.100.10 -p 8443 -i
```

Outcome: skips certificate peer and hostname verification (needed when scanning an address that cannot match the certificate's names) and reports the same per-protocol accepted-cipher list for the service on 198.51.100.10:8443.
