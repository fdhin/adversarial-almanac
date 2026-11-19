---
technology: "TLS/SSL server configuration attack surface"
kind: bug-bounty-methodology
audience: ai-agent
status: upstream-only
family: protocol
version: "1.0"
provenance: "upstream-only; zero corpus evidence by design — Heartbleed (CVE-2014-0160) and POODLE (CVE-2014-3566, TLS_FALLBACK_SCSV, CVE-2014-3568) facts transcribed from OpenSSL security advisories 07 Apr 2014 / 15 Oct 2014; tool semantics transcribed from the testssl.sh man page (branch 3.3dev), rbsec sslscan README, and nmap ssl-enum-ciphers NSE docs (all fetched 2026-11-13); ROBOT / Lucky13 / CRIME / BREACH / CCS injection / Ticketbleed / DROWN / Logjam / Sweet32 stated as history by primary name and mechanism, no unverified CVE digits; source: \"six2dez/pentest-book (enumeration/ssl-tls.md, no license — rewritten with attribution), accessed 2026-11-13\" — the source's `testssl.sh --cert` invocation was checked against the testssl.sh man page and corrected (no such option; certificate checks are part of the default run)"
---

# TLS/SSL bug-bounty methodology

Companion overview: [[ssl-tls]].

Scope: recon extraction from TLS endpoints, protocol/cipher downgrade reading, the named lineage bugs as history, client-certificate trust models, and the verified semantics of the three standard tools. This page covers the TLS wrapper on ANY TCP service; product-specific TLS-wrapped surfaces continue in their own units (e.g. [[fortinet-bug-bounty]] for FortiGate SSL-VPN).

## Phase 0 — Certificate and SAN recon (the goldmine)

```bash
# full certificate detail
echo | openssl s_client -connect example.com:443 2>/dev/null \
  | openssl x509 -noout -text
# expiry dates only
echo | openssl s_client -connect example.com:443 2>/dev/null \
  | openssl x509 -noout -dates
# whole chain in order
openssl s_client -connect example.com:443 -showcerts </dev/null
```

Extraction targets, in value order:

- **SAN entries** — a free hostname enumeration feed. Every `DNS:` entry is a vhost/subdomain candidate; wildcards show scope breadth; internal naming schemes leaked into public SANs hand you the org's host-naming convention. Feed all entries into the target inventory.
- **Issuer chain** — private/internal CA distinguished names reveal the PKI (and often the company's real name behind a front domain). Self-signed certs on internal appliances name the device class.
- **Serial numbers and fingerprints** — pivot keys for certificate-transparency log searchers and exposure databases (censys.io and crt.sh-style services, named in prose).
- **Certificate hygiene** — expiry, CN/SAN mismatch, SHA-1/MD5 signatures, sub-2048-bit RSA keys, missing intermediates. Each is a report finding in its own right, and together they score the operator's patch discipline.

## Phase 1 — Protocol and cipher downgrade reading

First pass with the comprehensive tool, then targeted probes:

```bash
testssl.sh https://example.com                 # default run: protocols, ciphers,
                                               # preferences, cert, headers, vulns
nmap --script ssl-enum-ciphers -p 443 example.com
sslscan example.com:443

# single-protocol probes (openssl)
openssl s_client -connect example.com:443 -tls1_3
openssl s_client -connect example.com:443 -tls1_2
openssl s_client -connect example.com:443 -tls1_1
openssl s_client -connect example.com:443 -tls1
# does the server enforce its own ordering? offer two ciphers, both orders:
openssl s_client -tls1_2 -cipher 'AES128-GCM-SHA256:AES128-SHA' -connect example.com:443
openssl s_client -tls1_2 -cipher 'AES128-SHA:AES128-GCM-SHA256' -connect example.com:443
# fallback protection: a server implementing TLS_FALLBACK_SCSV answers this
# with an inappropriate_fallback alert instead of completing the handshake
openssl s_client -tls1 -fallback_scsv -connect example.com:443
```

Decision table for what the reads mean:

| Server behavior | Meaning |
|---|---|
| SSLv2 negotiates | DROWN-era stack; if the same RSA cert is used on a TLS server anywhere, cross-protocol attack surface |
| SSLv3 negotiates | POODLE-era; downgrade victim without fallback SCSV |
| TLS 1.0/1.1 negotiate | Deprecated protocols; policy finding + BEAST/Lucky13-class CBC exposure |
| 3DES/RC2/IDEA suites accepted | Sweet32-class 64-bit block ciphers |
| EXPORT / LOW suites accepted | FREAK/Logjam-era export crypto still live |
| aNULL/ADH suites accepted | Encryption without authentication — immediate finding |
| NULL / eNULL suites accepted | Cleartext masquerading as TLS |
| RSA key exchange only, no (EC)DHE | No forward secrecy — session-decryption-on-key-compromise model |
| Weak DH parameters (≤1024-bit) | Logjam-class parameter weakness |
| Client-ordered cipher selection | Server honors attacker's ordering — downgrade-enabling misconfiguration |
| TLS compression offered | CRIME-class compression side channel |
| HTTP body compression with reflected input | BREACH-class (application layer; needs the HTTP response, not just the handshake) |

## Phase 2 — The named lineage bugs, as history

State these with their era and mechanism; convict only with a tool check, not a memory. Verified anchors first:

- **Heartbleed — CVE-2014-0160** (OpenSSL advisory, 7 Apr 2014): missing bounds check in the TLS heartbeat extension handling reveals up to 64k of server (or peer) memory per crafted heartbeat. Affected OpenSSL 1.0.1 through 1.0.1f and 1.0.2-beta1; fixed in 1.0.1g (or rebuilt with `-DOPENSSL_NO_HEARTBEATS`). Discovered by Neel Mehta, Google Security. Loot historically included private keys and session material. The heartbeat extension answering is the *precondition marker*; the version-gated check convicts.
- **POODLE — CVE-2014-3566** (OpenSSL advisory, 15 Oct 2014): SSL 3.0's CBC padding is unverifiable, giving a padding-oracle downgrade target. The advisory shipped `TLS_FALLBACK_SCSV` (draft-ietf-tls-downgrade-scsv) so clients can block MITM-forced downgrades. Companion trap from the same advisory: **CVE-2014-3568** — OpenSSL built with the `no-ssl3` option could STILL complete SSLv3 handshakes; the build flag was not the kill switch operators assumed. Check behavior, not config claims.

History stated by name and mechanism (no CVE digits carried without primary verification):

- **ROBOT** (Böck & Somorovsky, 2017): the Bleichenbacher RSA PKCS#1 v1.5 padding oracle returning — servers (including major sites at disclosure) leak timing/behavior differences on crafted premaster secrets, decrypting RSA key-exchange sessions without the key.
- **Lucky13** (AlFardan & Paterson, 2013): timing side channel in TLS CBC padding/MAC processing; the reason the whole CBC-in-TLS family, not just SSLv3, deserves the POODLE treatment.
- **CRIME** (Rizzo & Duong, 2012): TLS-level compression lets a MITM recover request secrets (session cookies) byte-by-byte; check the handshake for any non-NULL compression method.
- **BREACH**: the same idea against HTTP-layer compression (gzip) — handshake checks do not convict; test reflected secrets in compressed responses.
- **CCS injection** (OpenSSL, 2014): ChangeCipherSpec accepted early, weakening the handshake's finished-message integrity.
- **Ticketbleed** (2017): session-ticket memory over-read, BigIP lineage — the Heartbleed shape recurring in another vendor's stack.
- **Logjam / FREAK** (2015): export-grade DH/RSA parameters either accepted or forced, breaking key exchange.
- **DROWN** (2016): SSLv2 on any port as a cross-protocol oracle against the same RSA certificate's TLS servers; the reason "SSLv2 still answers somewhere" is a finding even when nothing uses it.

## Phase 3 — Client-certificate trust models

When the deployment uses mTLS, the server discloses its trust model during the handshake:

- The certificate-request message advertises the **acceptable CA distinguished names** — enumerate them with `sslscan --show-client-cas` (its dedicated flag) to map which PKIs the server honors, before any certificate forgery planning.
- Audit for the classic bypass classes: server *requests* a client cert but does not *verify* it (optional-verify misconfig); accepts a self-signed cert; accepts any cert from an over-broad CA; verifies chain but not identity/authorization mapping.
- To test past the handshake with a real client cert, `testssl.sh --mtls <PEM file>` supplies the pair (cert plus unencrypted key).
- RDP, Postgres, LDAP and mail STARTTLS all have their own client-cert quirks — probe each with the right tool mode (`sslscan --rdp`, `--starttls-ldap`, STARTTLS variants).

## Phase 4 — Reporting the fix

Protocol floor TLS 1.2+ (disable SSLv3/v2 outright — behaviorally verified, per the CVE-2014-3568 lesson), TLS compression off, PFS-only suites (ECDHE preferred; DHE with ≥2048-bit parameters), no export/LOW/anonymous/NULL/RC4/3DES suites, SHA-256+ signatures on ≥2048-bit RSA or ECDSA certs, server-enforced cipher ordering, fallback SCSV present on any mixed-version estate, automation for cert renewal (expiry findings are process failures, not one-offs), and HSTS for the HTTP tier.

## Appendix — tool semantics (verified against each tool's own docs, 2026-11-13)

**testssl.sh** (branch 3.3dev man page): `testssl.sh <URI>` default run covers protocols (incl. QUIC/ALPN), standard cipher categories, server cipher preference, forward secrecy, server defaults incl. certificate info, HTTP headers, vulnerabilities, per-cipher tests and client simulation. Any single-check flag disables the default run. Vulnerability single checks: `-U/--vulnerable` (all of the following), `-H/--heartbleed`, `-I/--ccs`, `-T/--ticketbleed`, `--BB/--robot`, `-O/--poodle` (SSLv3+CBC), `-W/--sweet32` (64-bit block ciphers), `-F/--freak` (EXPORT RSA), `-D/--drown` (checks SSLv2 availability; does NOT detect the same-cert-elsewhere variant), `-A/--beast`, `-L/--lucky13`, `-J/--logjam`, `-B/--breach` (HTTP compression; only the supplied URL is tested), `-C/--compression/--crime`, `-R/--renegotiation`, `-Z/--tls-fallback` (TLS_FALLBACK_SCSV). Cipher mapping: `-e/--each-cipher` (≈370 ciphers), `-E/--cipher-per-proto`, `-s/--categories` (incl. NULL/aNULL/EXPORT/LOW/3DES buckets), `-f/--fs`; `--mapping <openssl|iana|...>` renames output. Input: `--file` accepts command lists or nmap grepable (`-oG`) output and auto-selects STARTTLS per port; `-t/--starttls <proto>` for ftp/smtp/pop3/imap/xmpp/sieve/telnet/ldap/irc/lmtp/nntp/postgres/mysql; `--mx <domain>` walks MX hosts. `--ids-friendly` skips Heartbleed/CCS/Ticketbleed/ROBOT. Note: there is no `--cert` option — certificate analysis is part of the default run's server-defaults section (the source note's `testssl.sh --cert` invocation is a miscite).

**sslscan** (rbsec fork README): v2 rewrote the backend so legacy protocol probes (SSLv2/v3 handshakes, TLS 1.3) no longer depend on the local OpenSSL build. Highlights weak findings in output (SSLv2/v3 suites, POODLE-flagged CBC, 3DES/RC4, anonymous ADH/AECDH, NULL/low-bit suites) and flags MD5/SHA-1-signed, short-key, expired and self-signed certificates. Dedicated checks: TLS compression (CRIME), Heartbleed (CVE-2014-0160), TLS fallback SCSV support. Recon flags: `--show-client-cas` (acceptable client-cert CA list), `--show-certificate`/`--show-certificates`, `--iana-names`, `--sni-name`, `--failed`, key-exchange group and signature-algorithm enumeration, `--rdp`, `--starttls-ldap` and MySQL STARTTLS, `--ipv6`, `--sleep`. The `bits` column reports effective strength (3DES shows 112, not 168).

**nmap ssl-enum-ciphers** (NSE docs): completes one handshake per candidate cipher/compressor and reports every accepted suite with an A–F grade derived from the Qualys SSL Labs rating guide — deliberately excluding protocol-version support, which is ~30% of SSL Labs' own score, so do not quote the grade as a Labs rating. The `Least strength` line is the weakest accepted cipher (usually the finding). When the server enforces its own ordering, extra probes recover its sorted preference list (`cipher preference: server` vs client). Built-in warnings flag Sweet32 (3DES), RC4 (RFC 7465), MD5 integrity, SHA-1 certificate signatures, weak DH. Classified intrusive/noisy (many connections); run with `-sV` so it finds TLS on unexpected ports, and set `tls.servername` for SNI.
