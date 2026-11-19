---
name: "sslscan"
categories:
  - information-gathering
  - web
version: "2.2.3-1"
homepage: "https://github.com/rbsec/sslscan"
origin: "arch"
arch-repo: "extra"
packaged-by: [arch, kali, nixpkgs]
arch-builddate: "2026-09-20"
build-system: make
aliases: ["sslscan2"]
meta-checked: "2026-11-09"
github-repo: "rbsec/sslscan"
github-stars: 2633
github-pushed: "2026-09-27"
github-archived: false
github-issues: 68
github-match: "exact"
gh-checked: "2026-11-07"
worth: 7.8
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
last-refresh: "2026-11-19"
github-commits-year: 20
gh-activity-checked: "2026-11-09"
---

# sslscan

> Enumerates a TLS service's supported protocol versions, cipher suites, certificate and classic TLS weaknesses, with graded color output and XML reports.

## What it's for

Auditing a host's TLS configuration: which protocols (SSLv2 through TLSv1.3) and cipher suites are accepted, with weak entries highlighted (NULL, RC4, 3DES, CBC/POODLE, anonymous suites, PFS+GCM marked good), the preferred cipher, certificate details (signing algorithm, key size, expiry), and checks for Heartbleed, TLS compression (CRIME), TLS fallback SCSV, renegotiation, key-exchange groups and signature algorithms. Version 2 carries its own TLS implementation, so protocol coverage no longer depends on the linked OpenSSL. STARTTLS is supported for SMTP, IMAP, POP3, LDAP, MySQL, PostgreSQL, FTP, IRC and XMPP, and RDP services can be scanned with `--rdp`. It characterizes the TLS surface — it is not a web vulnerability scanner.

## Typical use cases

### Fingerprint an HTTPS service

```
sslscan target.example.com
```

Outcome: per-protocol tables of accepted, rejected and preferred ciphers, a certificate summary, and the Heartbleed/compression/fallback-SCSV results; default port 443, color-graded (disable with `--no-colour`).

### Scan a non-standard port or raw IP

```
sslscan 198.51.100.10:8443
```

Outcome: the same report against `host:port`; an `https://` prefix on hosts read from target files is stripped automatically.

### Dump the full certificate

```
sslscan --show-certificate target.example.com
```

Outcome: prints the complete certificate in addition to the summary — SANs, signature and the chain — flagging MD5/SHA-1 signatures, short RSA keys and expiry. `--show-client-cas` lists the CAs the server accepts for client certificates instead.

### Audit STARTTLS on a mail server

```
sslscan --starttls-smtp mail.example.com:25
```

Outcome: negotiates STARTTLS first, then runs the same protocol/cipher/weakness enumeration against the SMTP service; sibling flags exist (`--starttls-ldap`, `--starttls-mysql`, `--starttls-psql`, `--starttls-imap`, `--starttls-pop`, `--starttls-ftp`, `--starttls-irc`, `--starttls-xmpp`), and `--sni-name` overrides SNI.

### Bulk targets into one XML report

```
sslscan --xml=report.xml --targets=hosts.txt
```

Outcome: scans every `host[:port]` line in the file and writes a consolidated XML report (version-2 schema: `<certificates>` parent with typed `<certificate>` elements) suitable for CI diffing; `--sleep` spaces out requests against fragile targets.

## Bug bounty workflow with sslscan

**Audience and rules.** Everything below assumes an authorized program, written permission, and the program's rules of engagement. sslscan is low-risk by nature — it opens TCP connections and completes TLS handshakes; it does not send application-layer payloads — but it is not zero-risk: it opens *one connection per cipher it probes*, so a full run against one host is dozens-to-hundreds of handshakes, and that pattern trips rate limits, WAF connection throttles, and fail2ban on fragile or shared-hosting targets. Treat it as a recon/triage tool, not a payout engine: in most modern programs the TLS-hardening findings it surfaces (weak ciphers, missing Fallback SCSV, TLSv1.0 enabled) are **explicitly out of scope or auto-closed as informational**. Its real value in a bounty context is (1) confirming the narrow TLS issues programs *do* pay for — expired/invalid certs on production customer-facing hosts, wrong-host certificates, genuinely exploitable primitives like Heartbleed or an SSLv2 listener — and (2) cheaply mapping the TLS posture across a large scope so the one anomalous host (a forgotten staging box on TLSv1.0, a plaintext service behind a terminator) surfaces.

**A note on scope before you scan.** Read the program's policy for TLS/SSL exclusions first. A huge fraction of programs carry language like "SSL/TLS best-practice issues, missing HSTS, weak ciphers, and self-signed certs on non-production hosts are not eligible." If that language is present, a raw sslscan dump is not a report — you need the *exploitable* subset or a chain. Scan anyway for recon, but calibrate expectations.

### Phase 0 — Confirm scope and reachability (passive-first)

Before scanning, establish which hosts are in scope and which are live TLS endpoints. Do passive resolution and a single connection check; don't fan out yet.

```bash
# One host, default port, minimal footprint — is this even TLS?
sslscan --no-ciphersuites --no-heartbleed --no-compression \
        --no-renegotiation --no-groups www.example.com
```

This is the lightest possible probe: it negotiates, reports protocol versions and the certificate, and skips the expensive full cipher enumeration (which is the part that opens one connection per cipher). Use it as a liveness-and-fingerprint pass across a host list before committing to full scans. A host that answers here is worth a full scan; a host that refuses is not.

### Phase 1 — Full TLS characterization of in-scope hosts

Once you know which hosts are live, run the full matrix and capture machine-readable evidence from the start.

```bash
sslscan --xml=www.example.com.xml www.example.com | tee www.example.com.txt
```

You now have the human-readable transcript and the XML (v2 schema: a `<certificates>` parent with typed `<certificate type="short|full">` children) for diffing and for the report appendix. Record the certificate block, the preferred cipher, and every protocol line verbatim — this is your baseline.

### Phase 2 — Triage the output against what the program accepts

Walk the output top-down and sort each signal into *eligible*, *chain-input*, or *informational-only*:

- **Certificate validity** (expiry, hostname/SAN mismatch, chain issues) on a production host — usually the only standalone TLS finding programs accept. `--show-certificate` for the detail.
- **Protocol downgrade exposure** — SSLv2 enabled is a DROWN precondition and genuinely exploitable; SSLv3/TLSv1.0/1.1 are usually informational unless the program says otherwise.
- **Exploitable primitives** — Heartbleed (CVE-2014-0160, sslscan checks it directly), insecure renegotiation, TLS compression (CRIME). Rare in 2026 but high-value when real.
- **Weak ciphers** (NULL, anon, RC4, EXPORT/FREAK-class, 3DES/SWEET32-class) — almost always informational/dupe-prone on their own.

### Phase 3 — Escalate: deep audit or chain

For anything that looks genuinely exploitable, switch to the deep-audit tool (**testssl.sh**, see Command recipes) which names and confirms the specific vuln (ROBOT, DROWN, Ticketbleed, SWEET32) and assigns an SSL-Labs-style rating a triager recognizes. sslscan tells you *a weak cipher is offered*; testssl.sh tells you *the host is exploitable via SWEET32 and here is the grade*. Use sslscan to find candidates fast across many hosts, testssl.sh to confirm the few that matter.

### Phase 4 — Validated finding

A reportable TLS finding needs: the exact host:port, the sslscan transcript, the XML artifact, a second-tool confirmation for anything claimed as exploitable, and an impact statement that survives the program's exclusion list. "TLSv1.0 is enabled" is not a finding in most programs; "the production payment host presents an expired certificate, breaking TLS for all customers" is.

## Where the money is with sslscan

sslscan surfaces TLS-configuration facts; **payout potential is modest and heavily dupe-prone** because these issues are discoverable by every automated scanner and are frequently out of scope. Ranked by realistic bounty value, not by how loud the finding looks:

| Finding class | How sslscan reaches it | Typical severity | Dupe / scope risk |
|---|---|---|---|
| Heartbleed (CVE-2014-0160) | Default scan runs the check; `--no-heartbleed` disables it | Critical | Very low dupe (near-extinct), but almost never present in 2026 |
| Expired / wrong-host / invalid production cert | Default cert summary; `--show-certificate` for SANs & chain | Low–Medium | Medium — accepted by many programs if customer-facing |
| SSLv2 enabled (DROWN precondition) | `--ssl2` or default protocol enumeration | Medium–High (as a chain) | Low dupe, rare; confirm exploitability with testssl `--drown` |
| Insecure TLS renegotiation | Default; `--no-renegotiation` disables | Medium | Medium |
| TLS compression / CRIME | Default; `--no-compression` disables | Low–Medium | Medium |
| Weak ciphers: NULL, anonymous, EXPORT (FREAK/LOGJAM-adjacent) | Default cipher matrix (weak suites colour-graded) | Low–Medium | High — scanner-trivial |
| 3DES / SWEET32, RC4 | Default cipher matrix | Low | High — almost always informational |
| Missing TLS Fallback SCSV | Default; `--no-fallback` disables | Low | High |
| Weak cert signature (MD5/SHA-1) or short RSA key (<2048) | Default flags these; `--no-check-certificate` suppresses | Low | High |
| Deprecated protocol (SSLv3 / TLSv1.0 / TLSv1.1) | Default; or targeted `--tls10` etc. | Info–Low | Very high — usually excluded |
| Recon value: odd host in a large scope | `--targets=hosts.txt` sweep, diff the matrices | — (enables other findings) | n/a — this is the real win |

The honest takeaway: the single most valuable use of sslscan in bug bounty is **the last row** — running it across a wide, authorized scope and letting the one host that disagrees with the fleet (an old subdomain on TLSv1.0 with an expired cert and SSLv2, a service answering plaintext where its siblings answer TLS) point you at forgotten infrastructure, which is where the *application* bugs live.

## Command recipes

Each block is a real command (flags verified against the rbsec/sslscan man page and README), its low-volume settings, and the output to expect. Flag note: sslscan **hides rejected ciphers by default** — unlike `nmap --script ssl-enum-ciphers`, there is no noise to suppress and **no `--no-failed` flag exists**; the README's `--failed` is the opposite switch (it *shows* rejected ciphers). There is also **no JSON output** (`--xml` is the only machine format).

### Baseline single-host scan

```bash
sslscan www.example.com
```

Expected output (abridged), colour-graded in a terminal — weak entries in red/yellow, strong in green:

```
  Supported Server Cipher(s):
Preferred TLSv1.3  128 bits  TLS_AES_128_GCM_SHA256        Curve 25519 DHE 253
Accepted  TLSv1.3  256 bits  TLS_AES_256_GCM_SHA384        Curve 25519 DHE 253
Accepted  TLSv1.2  256 bits  ECDHE-RSA-AES256-GCM-SHA384   Curve 25519 DHE 253
Accepted  TLSv1.2  112 bits  ECDHE-RSA-DES-CBC3-SHA        Curve 25519 DHE 253   <- 3DES/SWEET32
  SSL Certificate:
Signature Algorithm: sha256WithRSAEncryption
RSA Key Strength:    2048
Not valid before:    ...
Not valid after:     ...   <- check expiry here
```

Read the **`bits` column as OpenSSL effective strength, not key length**: 3DES reports `112 bits`, a CCM8 suite reports `64 bits`. That is a documented quirk, not a bug.

### Lightweight liveness / fingerprint pass (program-safe)

```bash
sslscan --no-ciphersuites --no-heartbleed --no-compression \
        --no-renegotiation --no-fallback --no-groups 198.51.100.10:8443
```

Skips every per-cipher and extra-check connection; returns protocol support and the certificate with the smallest possible connection count. The ideal first touch on a fragile or shared-hosting target, and the right pass to run broadly before committing to full scans.

### Non-standard port and raw IP

```bash
sslscan 198.51.100.10:8443
```

Same report against `host:port`; an `https://` prefix on hosts read from a targets file is stripped automatically. Use `--sni-name=www.example.com` when scanning by IP so the server selects the right virtual host / certificate.

### Full certificate detail

```bash
sslscan --show-certificate www.example.com
```

Prints the full certificate (SANs, signature algorithm, chain) alongside the summary, flagging MD5/SHA-1 signatures, sub-2048-bit RSA keys, and expiry. `--show-certificates` (plural) dumps the entire chain; `--show-client-cas` lists the CAs the server will accept for client-certificate auth (blank on IIS/Schannel), which maps an mTLS trust store. `--ocsp` adds the OCSP status.

### Target a single protocol (minimal-footprint downgrade check)

```bash
sslscan --ssl2 www.example.com          # is SSLv2 (DROWN precondition) listening at all?
sslscan --tls10 www.example.com         # TLSv1.0 ciphers only
```

`--ssl2`/`--ssl3` check only whether that protocol is enabled; `--tls10`/`--tls11`/`--tls12`/`--tls13` each check only that version's ciphers; `--tlsall` restricts to TLS 1.0–1.3. These are far cheaper than a full scan when you only need one answer.

### STARTTLS services (and the mandatory rate cap)

```bash
sslscan --starttls-smtp --sleep=250 mail.example.com:25
```

Negotiates STARTTLS first, then runs the normal enumeration. **`--sleep=<msec>` is not optional for mail and other rate-limited services** — SMTP servers (and greylisting/fail2ban front-ends) will throttle or temp-ban a burst of connections; the man page calls out `--sleep` specifically for SMTP. Sibling flags: `--starttls-imap`, `--starttls-pop3`, `--starttls-ldap`, `--starttls-mysql`, `--starttls-psql`, `--starttls-ftp`, `--starttls-irc`, `--starttls-xmpp` (try `--xmpp-server` for s2s if `--starttls-xmpp` fails). `--rdp` scans RDP with the correct preamble.

### Batch sweep with evidence capture — the restraint settings

```bash
sslscan --targets=hosts.txt --sleep=200 --connect-timeout=10 \
        --timeout=5 --xml=sweep.xml
```

Scans each `host[:port]` line and writes one consolidated XML report. The restraint settings that keep a wide sweep inside program etiquette:

- `--sleep=200` — pause 200 ms between connections; raise it on shared hosting where many logical targets live behind one IP (your per-host budget is really a per-IP budget there).
- `--connect-timeout=10` — cap the initial connect (default 75 s) so dead hosts don't stall the sweep.
- `--timeout=5` — socket timeout for hosts that don't answer ciphers they don't understand (default 3 s); bump slightly for high-latency targets.
- `--xml=sweep.xml` — evidence for every host in one artifact; `--xml=-` streams XML to stdout for piping.

sslscan is **single-threaded per invocation** — there is no thread-count flag, which is a feature for politeness: you control volume through `--sleep` and by how you batch the target file, not by throttling a thread pool. For a large scope, split `hosts.txt` and space the batches over time rather than scanning everything at once.

### Comparison: sslscan vs testssl.sh (fast triage vs deep audit)

Use the right tool for the phase. The division of labour:

| Dimension | sslscan | testssl.sh |
|---|---|---|
| Form | Compiled C binary; v2 carries its own TLS stack (needs OpenSSL ≥ 3.5.0 LTS to build as of 2.2.0) | Single `bash` script using a bundled static OpenSSL in `./bin/`; most checks over raw TCP sockets |
| Speed | Fast — seconds per host | Slow — a full run tests ~370 preconfigured ciphers plus every vuln; minutes per host |
| Output | Terminal + XML only (no JSON) | Terminal + JSON (flat and pretty), CSV, HTML, log files |
| Named vuln checks | Heartbleed, CRIME (compression), Fallback SCSV, renegotiation — you *infer* the rest from the cipher/protocol matrix | Named & confirmed: Heartbleed, CCS injection, Ticketbleed, ROBOT, CRIME, BREACH, POODLE, SWEET32, FREAK, DROWN, LOGJAM, BEAST, LUCKY13, RC4, Winshock |
| Rating | None — raw facts, colour-graded | SSL-Labs-style rating (its run ends with a rating step; `--rating-only` does the minimum to produce one) |
| Best for | Wide-scope triage, per-host matrix, fast recon sweeps | Confirming a specific exploitable vuln and producing a triager-legible graded report |

Workflow: **sslscan to find candidates across the fleet, testssl.sh to confirm and grade the few that matter.** testssl.sh's `--ids-friendly` skips the intrusive checks (Heartbleed, CCS injection, Ticketbleed, ROBOT) when a program is sensitive to IDS noise; sslscan has no such switch because it never sends those app-layer payloads except the (benign, read-only) Heartbleed probe, which you can disable with `--no-heartbleed`.

### Signature algorithms and key-exchange groups

```bash
sslscan --show-sigs --all-groups www.example.com
```

`--show-sigs` enumerates the server's accepted TLS signature algorithms (e.g. whether it still accepts RSA-PKCS1-SHA1). `--all-groups` enumerates supported key-exchange groups across *all* TLS versions rather than only the highest of TLS 1.0/1.1/1.2 (the default), which is how you catch a legacy group offered only under an older protocol; `--no-groups` skips group enumeration entirely when you want a faster, lighter scan. These feed hardening findings more than payouts, but a server still signing with SHA-1 or offering a weak DH group is a clean, grounded data point.

### Enumerate what the binary knows (no network)

```bash
sslscan --show-ciphers        # full list of cipher suites this build supports
sslscan --show-cipher-ids     # with hex cipher IDs
```

Offline reference — useful for mapping an observed suite name to its hex ID when you cross-reference against a CVE or an IANA registry entry. Add `--iana-names` to any scan to print IANA/RFC cipher names instead of OpenSSL's names, which is what most advisories and the program's triager will expect to see.

### mTLS / client-certificate endpoints

```bash
sslscan --show-client-cas www.example.com                 # which CAs the server trusts for client auth
sslscan --pk=client.key --certs=client.pem --pkpass=secret api.example.com:8443
```

`--show-client-cas` lists the CAs a server will accept for client-certificate authentication (blank on IIS/Schannel), mapping an mTLS trust store without presenting a cert. When the program issues you a test client certificate, `--pk=<file>` supplies the private key (or a PKCS#12 bundle), `--pkpass=<password>` its passphrase, and `--certs=<file>` the PEM/ASN.1 client certs — so you can characterize the TLS posture *behind* a client-cert gate that would otherwise refuse the handshake.

### Handshake timing (with the man-page caveat)

```bash
sslscan --show-times --no-cipher-details www.example.com
```

`--show-times` prints per-handshake time in milliseconds. The man page is explicit that **this is not a benchmark** — only one request is made per cipher — and recommends pairing it with `--no-cipher-details` (which drops the NIST curve name and EDH/RSA key length) to keep the timing column readable. Treat timing as a coarse signal, never as evidence of a timing-oracle finding on its own.

### Workaround and transport flags

```bash
sslscan --bugs www.example.com                 # enable OpenSSL bug workarounds for cranky servers
sslscan --ipv6 www.example.com                 # force IPv6 (default is IPv4 with v6 fallback; --ipv4/-4 forces v4)
sslscan --no-check-certificate www.example.com # stop flagging weak-sig / short-key certs (quieter output)
sslscan --no-preferred www.example.com         # suppress everything except the specified checks
```

`--bugs` is the flag to reach for when a handshake fails against an old or non-conformant stack. `--ipv4`/`-4` and `--ipv6`/`-6` force the resolver family — worth setting explicitly when a host has split A/AAAA records and you need to prove the posture on a specific address. `--verbose` adds protocol-level detail when a scan behaves unexpectedly and you need to see why a handshake failed.

### XML as evidence and for regression/sweep triage

The `--xml` output is the deliverable artifact and the basis for diffing. The v2 schema wraps certificates in a `<certificates>` parent, each `<certificate type="short">` (summary) or `type="full"` (with `--show-certificates`). Two durable uses:

```bash
# 1. CI / point-in-time regression: did a host's TLS posture change between scans?
sslscan --xml=before.xml www.example.com
# ... later ...
sslscan --xml=after.xml www.example.com
diff <(xmllint --format before.xml) <(xmllint --format after.xml)
```

A diff that newly shows an `sslv2`/`sslv3` element, a weak cipher, or a changed `notValidAfter` date is a regression you can report with both artifacts attached. For a wide sweep, pull just the weak signals out of the consolidated XML to triage which of hundreds of hosts deserve a closer look:

```bash
# 2. Sweep triage: find hosts that still speak SSLv2/SSLv3 in one XML report
xmllint --xpath '//ssltest[cipher[@sslversion="SSLv2" or @sslversion="SSLv3"]]/@host' sweep.xml
```

Keep the raw XML unmodified in the report appendix — its element names and attributes are the reproducible, machine-checkable evidence a triager can re-run against.

### Interpreting the matrix: from sslscan observation to named vulnerability

sslscan reports raw cipher/protocol facts and a few direct checks; it does **not** name most vulnerabilities for you. That translation is the hunter's job, and it is the single most useful skill when working from sslscan output, because a triager wants the named vuln, not a cipher dump. Each mapping below is the standard precondition relationship (the same logic testssl.sh's named checks encode); use it to turn a sslscan line into a candidate, then confirm exploitability with testssl.sh before claiming impact:

| sslscan observation | Named vulnerability it implies | Confirm with |
|---|---|---|
| `SSLv2` accepted | DROWN (CVE-2016-0800) — cross-protocol RSA decryption | `testssl --drown` |
| `SSLv3` accepted + CBC ciphers | POODLE (CVE-2014-3566) | `testssl --poodle` |
| RC4 ciphers accepted | RC4 biases (NOMORE) | `testssl --rc4` |
| 3DES / any 64-bit-block cipher accepted | SWEET32 (CVE-2016-2183) | `testssl --sweet32` |
| EXPORT RSA ciphers accepted | FREAK (CVE-2015-0204) | `testssl --freak` |
| EXPORT DH ciphers / weak common DH prime | LOGJAM (CVE-2015-4000) | `testssl --logjam` |
| TLS compression enabled | CRIME (CVE-2012-4929) — sslscan checks this directly | sslscan default (`--no-compression` to skip) |
| Heartbleed flagged | Heartbleed (CVE-2014-0160) — sslscan checks this directly | sslscan default + `testssl --heartbleed` |
| CBC ciphers under SSLv3/TLSv1.0 | BEAST (CVE-2011-3389) | `testssl --beast` |
| Insecure renegotiation flagged | CVE-2009-3555 — sslscan checks this directly | sslscan default (`--no-renegotiation` to skip) |
| Missing TLS Fallback SCSV | Downgrade-attack exposure — sslscan checks this directly | sslscan default (`--no-fallback` to skip) |

Two caveats that keep these honest: a cipher being *offered* is a precondition, not proof of exploitability — RC4/3DES/BEAST in particular need client-side conditions that may not hold, which is why the confirm column exists. And several of these (POODLE, BEAST, LOGJAM) are old enough that most programs treat them as informational unless you can demonstrate real impact; lead with DROWN (if SSLv2 is live), Heartbleed, or a cert issue, not with BEAST.

## Pitfalls, noise and scope discipline

**Reporting out-of-scope hardening as if it were a vuln.** The fastest way to get a report closed as N/A (and your signal dinged) is to paste a raw sslscan dump of "TLSv1.0 enabled, 3DES offered, no HSTS" into a program that excludes TLS best-practice issues. Read the exclusions first; report only the eligible subset with impact.

**Inventing flags.** `--no-failed` and `--json` do **not** exist. Rejected ciphers are already hidden by default; the only machine format is `--xml`. If you need JSON or named-vuln confirmation, that is testssl.sh's job. Don't cite flags you haven't verified against the man page — a report that references a non-existent flag tells the triager you didn't run the command.

**Connection-count bans on shared hosting.** A full scan opens one connection per probed cipher. On shared hosting, a WAF, or a service with aggressive fail2ban, that burst looks like a scan and can get your source IP temp-banned — which breaks the rest of your engagement from that IP. Use the liveness pass first, add `--sleep`, and split batches. Remember that many logical targets behind one IP share one rate-limit budget.

**STARTTLS without `--sleep`.** Mail servers throttle hard. Scanning `--starttls-smtp` at full speed is the classic way to get greylisted or banned mid-engagement. Always pace STARTTLS scans.

**Scanning the terminator, not the app.** If TLS is terminated at a CDN/load balancer, sslscan reports *that* device's posture, not the origin's. A clean result at the edge says nothing about whether the origin speaks plaintext internally. Note in the report exactly what host:port you measured, and don't claim origin properties from an edge scan.

**Over-trusting the `bits` column.** It is OpenSSL effective strength, not key length (3DES → 112, CCM8 → 64). Don't report "64-bit key" for a CCM8 suite; report the suite name and its real weakness.

**IP vs SNI confusion.** Scanning a raw IP without `--sni-name` can make the server present a default/fallback certificate, producing a spurious "wrong host / self-signed cert" finding that evaporates once you set SNI. Always set `--sni-name` when scanning by IP, and verify any cert-mismatch finding with the correct SNI before reporting.

**Edge of scope.** Only scan hosts the program lists in scope. A TLS sweep across a wildcard you *assume* is in scope, but isn't, is unauthorized scanning. When in doubt, ask the program.

## Reporting evidence

Capture and include in the report:

- **Exact target** — `host:port` and whether you set `--sni-name` (and to what). State whether the endpoint is the origin or a terminator/CDN edge.
- **sslscan version** — `sslscan --version` (note the `-static` suffix if OpenSSL was statically linked, since that affects protocol coverage). Pin the version so the triager can reproduce.
- **The exact command**, flags included, so the result is reproducible verbatim.
- **Human-readable transcript** — the relevant block (certificate summary for a cert finding; the protocol/cipher matrix for a protocol/cipher finding), pasted as text, not only a screenshot, with the weak lines highlighted.
- **XML artifact** — attach the `--xml` output; it is the structured, timestamp-bearing evidence and lets the triager diff against their own scan.
- **Second-tool confirmation** — for any claim of *exploitability* (Heartbleed, DROWN via SSLv2, SWEET32, ROBOT), attach the testssl.sh output that names and confirms the vuln and gives the rating. sslscan alone shows the precondition; the confirmation shows the impact.
- **Certificate specifics** — for cert findings: `--show-certificate` output with the expiry dates, the SAN list, the signature algorithm, and the exact mismatch between the cert and the host.
- **Impact statement** — framed to survive the program's exclusion list: who is affected, under what realistic conditions, and why it exceeds "TLS best practice."

For a cert-expiry or wrong-host finding, the minimal evidence is: the command, the `Not valid after` / SAN lines from `--show-certificate`, and a one-line impact ("production host serving customers presents an expired certificate; browsers hard-fail the connection"). For an exploitable-cipher finding, add the testssl.sh confirmation and rating.

## References

- rbsec/sslscan — project repository and README — https://github.com/rbsec/sslscan
- rbsec/sslscan — README (raw) — https://raw.githubusercontent.com/rbsec/sslscan/master/README.md
- rbsec/sslscan — man page source (`sslscan.1`, raw) — https://raw.githubusercontent.com/rbsec/sslscan/master/sslscan.1
- sslscan(1) man page (Debian, v2.1.5-1) — https://manpages.debian.org/testing/sslscan/sslscan.1.en.html
- testssl.sh — project repository — https://github.com/testssl/testssl.sh
- testssl.sh — man page source (`doc/testssl.1.md`) — https://github.com/testssl/testssl.sh/blob/3.3dev/doc/testssl.1.md

*Research supplement added 2026-11-16, grounded in the rbsec/sslscan README and sslscan.1 man page and the testssl.sh man page (flag set cross-checked across three primary sources; `--no-failed` and `--json` confirmed non-existent) (model: claude-opus-4-8).*
