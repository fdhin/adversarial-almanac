---
name: "tlshelpers"
categories:
  - blackarch-crypto
version: "43.2b0ce92"
homepage: "https://github.com/hannob/tlshelpers"
packaged-by: [blackarch]
blackarch-builddate: "2026-10-25"
build-system: scripts
license: Unlicense
meta-checked: "2026-11-09"
github-repo: "hannob/tlshelpers"
github-stars: 131
github-pushed: "2026-10-21"
github-archived: false
github-issues: 1
github-match: "exact"
gh-checked: "2026-11-07"
worth: 6.0
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 9
gh-activity-checked: "2026-11-09"
---

# tlshelpers

> Five small shell scripts for the tedious corners of X.509 work: key/certificate matching, one-command OCSP checks, SAN-aware CSRs, CT-log subdomain harvests, and decoy keys for testing validators.

## What it's for

A collection of focused OpenSSL wrappers that each do one certificate chore properly. `matchcertkey` verifies that a private key truly belongs to a certificate — checking internal consistency of the key, the SPKI SHA-256 hash, and a test signature, not just the public-key comparison most guides stop at. `ocspverify` resolves revocation status in a single command by pulling the issuer certificate and OCSP URL out of the certificate's AIA extension. `getsubdomain` harvests subdomains from the crt.sh Certificate Transparency database, `create-csr` generates a key and CSR with subjectAltNames for one or more hostnames, and `fakekey` forges a private key that superficially matches a certificate for testing whether tooling really validates keys. These are thin, readable scripts rather than a framework — reach for them when you would otherwise be copy-pasting multi-step openssl incantations. `fakekey` additionally needs the der-ascii tools, `getsubdomain` a PostgreSQL client.

## Typical use cases

### Confirm a private key belongs to a certificate

Check a key/certificate pair beyond the usual public-key comparison.

```
matchcertkey example.com.pem example.com.key
```

Outcome: runs three checks — openssl key-consistency validation, comparison of the SPKI SHA-256 hashes of key and certificate, and verification of a test signature — and exits non-zero at the first failing check, so a clean run certifies the keyfile genuinely fits that certificate.

### Check a certificate's OCSP status in one command

Get revocation status without manually chasing AIA URLs.

```
ocspverify example.com.pem
```

Outcome: extracts the CA-issuers and OCSP URLs from the certificate's AIA extension, downloads and converts the issuer certificate, and performs the OCSP query — printing the status you would otherwise need several manual openssl and wget steps to obtain.

### Harvest subdomains from Certificate Transparency logs

Build a passive subdomain list for a domain.

```
getsubdomain example.com
```

Outcome: queries the crt.sh certificate database over its PostgreSQL interface (requires a local `psql` client) and prints every DNS name found in CT logs for the domain and its subdomains, deduplicated — a fast, non-intrusive input for further recon.

### Generate a CSR covering several names

Produce a certificate request with all names in the SAN extension.

```
create-csr example.com www.example.com
```

Outcome: writes `example.com.csr` and `example.com.key`, placing both names in the subjectAltName extension with the CN set to the first name — ready to hand to a CA.

### Forge a decoy key for testing validators

Create a key that looks right to sloppy tooling.

```
fakekey example.com.pem decoy.key
```

Outcome: writes decoy.key (default `fake.key`), a file that parses as a valid private key matching the certificate's public key but does not survive deeper inspection — pair it with `matchcertkey` to verify your tooling checks keys properly instead of trusting key metadata.

*No corpus evidence — derived from upstream documentation.*
