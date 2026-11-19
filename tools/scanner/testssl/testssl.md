---
name: "testssl"
categories:
  - scanner
homepage: "https://github.com/testssl/testssl.sh"
github-repo: "testssl/testssl.sh"
github-match: "exact"
origin: "awesome-pentest"
last-refresh: "2026-11-19"
recipe: "upstream"
license: GPL-2.0
github-stars: 9280
github-pushed: "2026-11-12"
github-commits-year: 441
gh-checked: "2026-11-14"
worth: 8.5
worth-basis: "full (activity+popularity+recency+adoption)"
packaged-by: [arch]
---

# testssl

> The standard command-line TLS/SSL server auditor — protocols, ciphers, certificates, HTTP headers and known cryptographic flaws in one colorized run.

## What it's for

testssl.sh connects to any TLS/SSL-speaking service on any port and reports what it supports and how weak it is: SSL/TLS protocol versions (plus QUIC/HTTP/3 and ALPN), cipher suites by category and individually (370 preconfigured ciphers), server cipher preferences, forward secrecy, certificate details and trust chain, relevant HTTP security headers, and a battery of known vulnerabilities (Heartbleed, CCS injection, ROBOT, Ticketbleed, Logjam, CRIME, POODLE family...). Output is rated by color on screen and by severity in file outputs, with client simulation showing what real browsers would negotiate. It does everything through bash sockets and a bundled/OS OpenSSL binary, makes no third-party connections (DNS aside), and handles STARTTLS for mail and other protocols. Use it whenever the question is "what does this endpoint's TLS actually allow, and which flaws does that expose".

Upstream note: the project's original GitHub home `drwetter/testssl.sh` was deleted in early November 2026 and an empty placeholder repository was created at that slug on 2026-11-05 (zero stars, forks, commits — an API call against its commits endpoint returns the empty-repo error). The project itself is alive at its own organization, `testssl/testssl.sh`, which carries the full repository history since 2014 and active development on the `3.3dev` branch; the stats in this note describe that org repository. Older links and Docker images referencing the drwetter path predate this move.

No corpus evidence — derived from upstream documentation.

## Typical use cases

### Full default audit of a TLS endpoint

```
./testssl.sh target.example.com
```

Outcome: the default run — protocol check, standard cipher categories, server preferences, forward secrecy, certificate and server defaults, HTTP headers, vulnerability checks, every supported cipher, client simulation and an overall rating, color-coded by severity.

### Vulnerability checks only

```
./testssl.sh -U target.example.com
```

Outcome: runs just the vulnerability battery (Heartbleed, CCS injection, ticketbleed, ROBOT, padding oracles, DROWN, Logjam, FREAK...) against the target instead of the full default run.

### Audit a STARTTLS mail server

```
./testssl.sh -t smtp mail.example.com:25
```

Outcome: performs an SMTP STARTTLS handshake before running the checks; the same `-t/--starttls` switch covers ftp, pop3, imap, xmpp, ldap, telnet, postgres, mysql and more. Use `--mx example.com` to instead walk every MX record of a domain.

### Protocols and cipher support only

```
./testssl.sh -p target.example.com
```

Outcome: reports only which SSL/TLS protocols (SSLv2 through TLS 1.3, plus QUIC/NPN/ALPN for HTTP) the server accepts — the quick inventory pass.

### Batch-scan targets from an nmap output file

```
./testssl.sh --file nmap-open-ports.gnmap
```

Outcome: mass testing — reads targets from a file of testssl command lines or nmap greppable (`-oG`) output, tests every open TLS-looking port (auto-adding STARTTLS handling for ports like 25), serially by default (`--parallel` to fork).

### Machine-readable report for tooling

```
./testssl.sh -oJ report.json --severity LOW target.example.com
```

Outcome: writes the findings as structured pretty-JSON (flat variant with `-oj`, CSV with `-oC`, HTML with `-oH`, all formats at once with `-oA`), filtered to the given severity threshold; `-oA auto` derives filenames from host and timestamp.

## Bug bounty workflow with testssl.sh

TLS findings are the lowest-paying class in most bug bounty programs. The honest framing you must carry into every scan: *the output of a TLS scanner is not, by itself, a finding most programs will accept.* "Weak ciphers," "TLS 1.0 enabled," "missing HSTS," and "BEAST/POODLE theoretically possible" are the single most common auto-closed duplicates on every platform, usually explicitly listed under a program's "won't accept / informational" section alongside "best-practice" SSL Labs grades. testssl.sh pays off in bounty work in exactly three situations: (1) the program *explicitly* scopes TLS/crypto configuration as in-scope and gradeable; (2) the scan surfaces a memory-disclosure or oracle vulnerability with real impact (Heartbleed, Ticketbleed, ROBOT) that you can tie to a concrete consequence; or (3) the TLS state is one link in a chain (a cookie without `Secure`, a mixed-content path, an expired/mismatched cert on an auth host) whose impact is session theft or MITM. Everything else is a note, not a report. This workflow is built to separate those three from the noise before you ever open the report form.

Assume throughout: you have written authorization, the host is in scope, and you are testing at single-host, low-volume cadence. testssl.sh makes no third-party connections by default (DNS aside) and does not exploit anything — it probes handshakes — but the Heartbleed/CCS/Ticketbleed/ROBOT probes send malformed records that an IDS will flag, and `--parallel` batch runs against a shared CDN edge look like an attack. Both are covered below.

### Phase 0 — Read the program's crypto scope first

Before scanning, grep the program policy for the words `TLS`, `SSL`, `cipher`, `HSTS`, `certificate`, and `cryptograph`. Most programs put TLS configuration under "out of scope / informational." If it is excluded, a clean testssl report is wasted effort and a potential signal-to-noise strike against your account — do not submit it. If TLS is in scope, note *which* properties the program cares about (grade floor? specific protocols? cert hygiene?) so you filter findings to those. The one thing almost always in scope regardless: a concrete vulnerability with memory disclosure or a decryption/forgery oracle. Scan for those first.

### Phase 1 — Confirm you are testing the right endpoint (SNI/CDN discipline)

testssl.sh derives the TLS **SNI** from the hostname in the URI you pass. This matters enormously for CDN-fronted and shared-IP targets: pass the **hostname**, never a bare IP, or the edge serves its default/catch-all certificate and every cert and header finding is about a vhost you were never testing.

```
./testssl.sh https://www.example.com          # SNI = www.example.com (correct)
./testssl.sh 198.51.100.10                     # SNI empty -> edge default cert (misleading)
```

To test a *specific* edge node or origin IP while keeping the correct SNI/Host, pin the IP with `--ip` and keep the hostname in the URI:

```
./testssl.sh --ip=198.51.100.10 https://www.example.com
```

Here testssl connects to `198.51.100.10` but still sends `www.example.com` as SNI and in the `Host:` header — the way to audit one node of a pool, or an origin you reach via an `/etc/hosts`-style override, without the CDN's round-robin moving you between runs. `--ip=one` tests only the first resolved A record (AAAA with `-6`). When you must override resolution entirely (testing an origin behind a CDN by its direct IP), use `-n none` (`--nodns none`): it does zero DNS lookups, so the target must be an IP, be pinned with `--ip`, or live in `/etc/hosts` — and you keep the hostname for SNI. Confirm you reached the intended vhost by reading the certificate CN/SAN in the `-S` output before trusting anything else.

### Phase 2 — Fast inventory pass (what is even here)

Do not open with the full default run on first contact — it is slow and loud. Inventory first:

```
./testssl.sh -p https://www.example.com        # protocols only: SSLv2..TLS1.3, ALPN/NPN, HTTP/3
```

This single pass answers the only question that gates most TLS findings: *are there any legacy protocols (SSLv2, SSLv3, TLS 1.0/1.1) enabled at all?* If the answer is "TLS 1.2/1.3 only," the entire POODLE/BEAST/DROWN/FREAK/export-cipher family is off the table before you run a single vulnerability probe, and you have saved yourself the loud scan.

### Phase 3 — Targeted vulnerability probes (the money checks)

Run the checks that can actually produce an accepted finding, individually, so your output is scoped and your evidence is clean:

```
./testssl.sh -H https://www.example.com        # Heartbleed (memory disclosure)
./testssl.sh --BB https://www.example.com      # ROBOT (RSA decryption/forgery oracle)
./testssl.sh -T https://www.example.com        # Ticketbleed (F5 memory disclosure)
```

A `VULNERABLE` (red) verdict on any of these is a real finding. A `not vulnerable` on all of them, with modern protocols only, usually means there is no TLS report to write here. Everything else testssl checks (SWEET32, LUCKY13, CRIME, BEAST, LOGJAM, FREAK, DROWN, POODLE) is almost always informational in a bounty context unless the program says otherwise or you can chain it.

### Phase 4 — Full audit + evidence capture (only once there is something to report)

When Phase 2–3 found something worth reporting, run the full default audit *once* with evidence output on, filtered to the severities that matter:

```
./testssl.sh --severity HIGH -oA auto https://www.example.com
```

`-oA auto` writes log + pretty-JSON + CSV + HTML with filenames derived from host, port and timestamp; `--severity HIGH` keeps only HIGH/CRITICAL findings (plus WARN/FATAL, which the filter never drops) out of the file outputs, so your attached artifact is the finding and not 300 lines of `OK`/`INFO`. The HTML file is the human-readable attachment; the pretty-JSON is the machine-checkable one triagers and your own tooling can parse. Capture both.

### Phase 5 — Validate the finding is real and reachable

A scanner verdict is a hypothesis. Before reporting: confirm the finding is on the in-scope host (not a shared edge cert — re-check SNI per Phase 1), confirm it is reproducible on a second run, and for a vulnerability verdict, understand the actual impact (Heartbleed → what memory can leak; ROBOT → decrypt captured traffic / forge a signature). If you cannot state a concrete consequence, downgrade it to a note. Re-run the single relevant check (`-H`, `--BB`, etc.) to produce a clean, minimal evidence block for the report rather than attaching the whole audit.

## Where the money is with testssl.sh

| Finding class | Flag / feature that reaches it | Realistic bounty severity | Dupe / rejection risk |
|---|---|---|---|
| Heartbleed (CVE-2014-0160) — private key / session memory disclosure | `-H` / `--heartbleed` (in `-U`) | **Critical** when live | Near-extinct in 2026; if real, almost never a dupe |
| Ticketbleed (CVE-2016-9244) — F5 session-ID memory leak | `-T` / `--ticketbleed` | **High** | Rare; F5-specific. Low dupe risk |
| ROBOT — RSA PKCS#1v1.5 decryption/signing oracle | `--BB` / `--robot` | **Medium–High** if exploitable | Needs RSA key exchange; triagers want a working-oracle argument |
| DROWN (CVE-2016-0800) — SSLv2 cross-protocol decryption | `-D` / `--drown`; `-p` shows SSLv2 | **High** if SSLv2 + key reuse | Requires SSLv2 enabled somewhere; uncommon |
| STARTTLS command injection | `--SI` / `--starttls-injection` (SMTP/IMAP/POP3) | **Medium** | Niche; needs mail scope |
| Expired / hostname-mismatch / self-signed cert on an **auth** host | `-S` / `--server_defaults` | **Low–Medium** | Accepted only on sensitive hosts; dupe-heavy on marketing hosts |
| Cookie set without `Secure` over a downgradeable channel | `-h` / `--header` + protocol check | **Low–Medium** (chain) | Only pays as part of a session-theft chain |
| SWEET32 / 3DES, RC4, export ciphers, LOGJAM, FREAK | `-s`, `-e`, `--rc4`, `-W`, `-J`, `-F` | **Low / Informational** | **Very high dupe/auto-close.** Usually out of scope |
| BEAST / POODLE / CRIME / LUCKY13 | `-A`, `-O`, `-C`, `-L` | **Informational** | **Almost always rejected.** Legacy, needs MITM position |
| Missing HSTS / weak HSTS / no preload | `-h` / `--header` | **Informational** | **The #1 auto-closed TLS "finding."** Note, don't report |
| TLS 1.0 / 1.1 enabled, no forward secrecy, weak DH params | `-p`, `-f` / `--fs` | **Informational** | High dupe; "best practice," not a vuln |

The table's dividing line is memory disclosure or a cryptographic oracle (top rows — real findings) versus configuration weakness (bottom rows — notes). Opossum (`--OP`) and the TLS-fallback/downgrade check (`-Z`) sit between: report them only against a specific program appetite. When in doubt, the rule holds: if testssl colors it red *and* you can name the data an attacker gets or the traffic they decrypt, report it; if it is yellow/"best practice," file it as a hardening note or skip it.

### Severity mapping: tool color/grade vs. bounty severity

testssl rates findings two ways: a per-line color/severity (OK / INFO / LOW / MEDIUM / HIGH / CRITICAL, plus WARN and FATAL for scan problems) that drives the `--severity` filter, and an overall letter grade (A–F, capped at **T** when certificate trust fails and when STARTTLS is in use) from its implementation of the SSL Labs Server Rating Guide. **Neither maps directly to a bug bounty severity.** The tool rates cryptographic hygiene; a program rates exploitable impact. The gap, by category:

| testssl category | How the tool rates it | How a bounty program usually rates it | What closes the gap |
|---|---|---|---|
| **Protocols** (`-p`) | SSLv2/SSLv3 → red/CRITICAL; TLS 1.0/1.1 → yellow/LOW-MEDIUM; grade capped hard for legacy | SSLv2/SSLv3 live is Low–Medium *only* if you show a downgrade/DROWN path; TLS 1.0/1.1 alone is Informational | A working downgrade or cross-protocol decryption, or a program that scopes protocol floors |
| **Ciphers** (`-s`, `-e`, `-f`) | NULL/EXPORT/anon → red; RC4/3DES/SWEET32 → yellow; no-FS → yellow | Almost all Informational; "weak cipher supported" without a break is the #1 auto-close | A concrete decryption (SWEET32 needs ~an impractical data volume; say so) or program-scoped crypto |
| **Certificate** (`-S`) | Expired/self-signed/mismatch → red; weak key/sig → yellow; short validity → info | Low–Medium on an **auth/sensitive** host (MITM/trust impact); Informational on a marketing host | The host's role + a trust/MITM consequence (e.g. users trained to click through on a login host) |
| **HTTP headers** (`-h`) | Missing/weak HSTS, missing security headers → yellow/LOW | Missing HSTS alone is Informational and the most-duplicated TLS "finding" anywhere | A no-`Secure` session cookie + a reachable plaintext path = session-theft chain (then it's Medium) |
| **Vulnerabilities** (`-U`) | Heartbleed/Ticketbleed/ROBOT/DROWN → red/HIGH-CRITICAL | Matches the tool **when the impact is real and demonstrable** — this is the one category where tool severity and bounty severity align | A clear impact statement (what leaks / what decrypts) |

Operational rule: use `--severity HIGH` to make the tool's own rating do the first cut of your file output, then apply the program-severity column by hand before you submit. The only category where you can usually carry the tool's HIGH/CRITICAL straight into the report is **Vulnerabilities** — and even there, only with an impact sentence.

## Command recipes

Each block is annotated with the low-volume, scope-safe setting to use in a program.

### Scoped, quiet first scan of one host

```
./testssl.sh -q --sneaky -p https://www.example.com
```

`-q` suppresses the banner (and implies you accept the usage terms); `--sneaky` sends a Firefox user-agent instead of testssl's default on the HTTP header checks, so you are less conspicuous in the target's logs; `-p` limits this pass to the protocol inventory. Expected output: a short block listing SSLv2/SSLv3/TLS 1.0/1.1/1.2/1.3 each as offered/not offered, plus ALPN/NPN. This is your go/no-go for the whole legacy-vuln family.

### IDS-friendly run (skip the probes that trip alarms)

```
./testssl.sh --ids-friendly https://www.example.com
```

`--ids-friendly` omits the Heartbleed, CCS-injection, Ticketbleed and ROBOT probes — the four checks that send malformed/unusual records an IDS flags as attacks. Use this for the broad audit when the program's monitoring is sensitive, then run the skipped probes *individually and deliberately* (`-H`, `-I`, `-T`, `--BB`) once, documenting that you did. Expected: a normal audit with those four rows absent.

### CDN edge / origin pinning with correct SNI

```
./testssl.sh --ip=198.51.100.10 -S https://www.example.com
```

Connects to the chosen IP, sends `www.example.com` as SNI, and `-S` prints the certificate subject/SAN, chain of trust, OCSP/CRL and TLS extensions — so you can verify you reached the intended vhost (CN/SAN matches) and not a shared default cert. Expected output: a "Server Certificate" block; check the "subject's CN" and "subjectAltName" lines first.

### STARTTLS mail audit

```
./testssl.sh -t smtp mail.example.com:25
./testssl.sh --mx example.com
```

`-t smtp` performs the SMTP STARTTLS handshake before testing (the switch also covers ftp, pop3, imap, xmpp, xmpp-server, sieve, telnet, ldap, irc, lmtp, nntp, postgres, mysql); `--mx` walks every MX record of a domain on port 25, highest priority first. Note: STARTTLS caps the computed grade at **T** by design, so do not report a "low grade" as a finding on a mail host — read the individual rows instead.

### Evidence-grade output, filtered

```
./testssl.sh --severity HIGH -oA auto --outprefix example-www_ https://www.example.com
```

`-oA auto` emits log + pretty-JSON + CSV + HTML with auto-generated names; `--outprefix` prepends a stable prefix so a batch of hosts sorts together; `--severity HIGH` restricts the file outputs to HIGH/CRITICAL (WARN and FATAL are always kept; INFO and OK are always excluded). Open the `.html` for the report attachment and parse the `.json` (pretty) for your own triage. For a single machine-readable file instead of all four, use `-oJ report.json` (pretty JSON) or `-oj report.json` (flat JSON).

### Restrained batch scan of an authorized range

```
./testssl.sh --mode serial --severity MEDIUM -oA auto --file targets.txt
```

`--file` (alias `-iL`) reads one testssl command line per line, or an nmap greppable (`-oG`) file — it tests only open ports and auto-adds `-t smtp` for port 25. **Keep `--mode serial` (the default) for bounty work.** `--parallel` forks many scans at once; against a shared CDN edge or a single load balancer that is indistinguishable from a flood and can trip rate limits or a WAF, and it muddies which host a log entry belongs to. `--file` also turns on `--warnings batch` automatically so a per-host error ends that scan instead of waiting for a keypress. Scope the file to hosts you are authorized for and nothing else.

### External-query checks (opt-in, privacy-aware)

```
./testssl.sh --phone-out -S https://www.example.com
```

By default testssl makes no third-party connections. `--phone-out` enables outbound queries for CRL, OCSP and HSTS-preload status — useful for a complete cert-revocation picture, but it contacts CAs and Google's preload service, so only enable it when that external contact is acceptable under the engagement, and never with the target's identifying data in a context you have not cleared.

### What a real browser would actually negotiate

```
./testssl.sh -c https://www.example.com
```

`-c` (`--client-simulation`) handshakes as a set of standard clients (browsers, OpenSSL versions, mobile stacks) and reports the protocol and cipher each one lands on. This is the evidence that turns "TLS 1.0 is enabled" into "and modern Chrome/Firefox refuse to connect, so only a downgrade-capable attacker benefits" — which is exactly the context that decides whether a legacy-protocol row is Informational or part of a chain. It reaches no further than a normal handshake, so it is safe at single-host volume.

### Confirm or refute one suspicious cipher

```
./testssl.sh -x 3DES https://www.example.com
./testssl.sh -x RC4 https://www.example.com
```

`-x <pattern>` (`--single-cipher`) tests only ciphers matching the pattern, so you can produce a tight, named evidence block for one claim (e.g. "3DES is offered, enabling SWEET32 in principle") without the full cipher sweep. Pair it with the honest impact caveat (SWEET32 requires an impractical volume of same-key traffic) so the report is defensible.

### Forward secrecy and key-exchange strength

```
./testssl.sh -f https://www.example.com
```

`-f` (`--fs`, also `--nsa`/`--forward-secrecy`) reports the FS key-exchange ciphers, the ECDHE curves, and the FFDHE groups offered — the data behind a "no forward secrecy" or "weak DH parameters (LOGJAM)" claim. In most programs this is Informational; capture it only when the program scopes crypto strength.

### Flaky host or slow origin

```
./testssl.sh --socket-timeout 10 --openssl-timeout 10 https://www.example.com
```

`--socket-timeout` caps the TCP-handshake wait and `--openssl-timeout` caps each openssl connect (both need a `timeout` binary present). Use these when a target behind aggressive rate limiting or a slow origin produces hangs or false "not offered" results — raising the timeouts reduces false negatives without adding request volume.

### Through an authorized proxy

```
./testssl.sh --proxy=198.51.100.9:8080 https://www.example.com
```

`--proxy` routes the sockets through an HTTP proxy (`--proxy=auto` reads the environment); there is no proxy auth, HTTPS-proxy or SOCKS support. Add `DNS_VIA_PROXY=true` to resolve through it as well. Use this only when the engagement requires traffic to egress through a specific, authorized path.

### Authenticated endpoints, mutual TLS, and internal CAs

```
./testssl.sh --basicauth 'user:pass' -h https://portal.example.com
./testssl.sh --reqheader 'Authorization: Bearer TOKEN' -h https://api.example.com
./testssl.sh --mtls /path/to/client.pem https://mtls.example.com
./testssl.sh --add-ca /path/to/internal-ca.pem -S https://internal.example.com
```

`--basicauth` supplies HTTP basic credentials and `--reqheader` adds an arbitrary request header (repeatable) so the HTTP-header checks (`-h`) run against the authenticated response rather than a login redirect. `--mtls` points to a PEM client certificate plus its unencrypted private key for a mutual-TLS endpoint. `--add-ca` adds a private/internal CA (file, directory, or comma-separated list) so the chain-of-trust check in `-S` validates against your organization's CA instead of flagging a false "not trusted." Keep any credentials or client keys out of shell history and out of the report artifacts you attach.

### CI / automation exit codes

testssl returns machine-usable exit statuses: `0` success, `1` for an error or ambiguous result, **`50`–`200` as a vulnerability score for CI gating**, and `242`–`255` for specific failures (e.g. `254` command line could not be parsed, `247` DNS problem, `246` connectivity). In a pipeline, gate on the `50`–`200` band rather than parsing the text — but keep that pipeline pointed only at assets you are authorized and scoped to scan, and keep it serial. For continuous monitoring of your own test infrastructure this is ideal; against a bounty target it is a scope-violation risk, so prefer one-off manual runs.

## Pitfalls, noise and scope discipline

- **Submitting raw scanner output is the fastest way to get auto-closed.** "Weak ciphers / TLS 1.0 enabled / missing HSTS" are the archetypal informational duplicates. Filter with `--severity HIGH` and only report rows you can tie to memory disclosure, an oracle, or a concrete chain. A full-audit dump pasted into a report is a triager's cue that you did not read the program scope.
- **Testing a bare IP on a CDN/shared host gives you the wrong certificate.** Without the hostname in the URI there is no SNI, so you audit the edge's default cert and every cert/header finding is about the wrong vhost. Always pass the hostname; pin the node with `--ip` when you need a specific one (Phase 1).
- **`--parallel` on shared infrastructure looks like an attack.** Forking dozens of scans at a single CDN edge or load balancer can trip rate limiting, a WAF, or DoS protections, and it violates the low-volume posture bounty programs expect. Keep `--mode serial`; if you must parallelize an authorized internal range, confirm it is permitted and that the targets are distinct backends.
- **The intrusive probes (`-H`, `-I`, `-T`, `--BB`) send malformed records.** They are read-only and do not exploit anything, but an IDS flags them. Use `--ids-friendly` for the broad pass and fire the four individually and deliberately when you specifically want them — and say so in your report so defenders can correlate their alerts with your testing.
- **STARTTLS grade cap is a tool behavior, not a finding.** Any STARTTLS target is capped at grade **T**; a "poor grade" on a mail host is an artifact of that cap. Report the underlying individual rows, not the grade.
- **`--phone-out` leaves your environment.** It contacts CAs and the HSTS-preload service. Leave it off unless revocation status is part of the finding and the external contact is cleared.
- **Flag-name drift — verify against your build.** The man page is internally inconsistent on at least one option (`--assume-http` vs `--assuming-http`); `3.3dev` is a moving target. Run `./testssl.sh --help` on the exact version you have before scripting a flag into automation, and note the version in your report. There is no `--sni-name` flag and no `-9`/`--full` flag — SNI comes from the URI hostname automatically, and the full run is simply the default with no single-check flag.
- **Don't over-trust the letter grade.** testssl's rating is a near-complete implementation of the SSL Labs Server Rating Guide, not SSL Labs itself, and it documents several checks it does not yet perform (e.g. GOLDENDOODLE, Zombie POODLE, insecure renegotiation in the grade). Cite specific rows, not the grade, when you report.
- **Respect the move off the old repo.** Docker images and links referencing `drwetter/testssl.sh` predate the project's move to the `testssl/testssl.sh` org (the old slug is now an empty placeholder). Pull from the live org or `ghcr.io/testssl/testssl.sh` so you are running maintained code.

## Reporting evidence

For a TLS finding to survive triage, the report needs the verdict, the proof it is real and on the right host, and a concrete impact statement. Capture:

1. **The exact command and tool version.** Include the full invocation (flags shown) and the version/branch (`3.3dev` + commit, from `--help` or the banner). This lets the triager reproduce and lets you defend the flag names.
2. **The single relevant check, not the whole audit.** Re-run the one check behind the finding (`./testssl.sh -H ...`, `./testssl.sh --BB ...`) and attach that minimal colored output block showing the `VULNERABLE` verdict, rather than a 300-line default dump.
3. **SNI/host proof.** Show the `-S` certificate CN/SAN confirming you reached the in-scope vhost (not a shared edge default cert), and the resolved IP if you pinned one with `--ip`. This pre-empts the "you tested the wrong server" rejection.
4. **The machine-readable artifact.** Attach the pretty-JSON (`-oJ`) and/or HTML (`-oH`) from a `--severity`-filtered run so the finding is isolated. The JSON carries the per-finding `severity`, `id`, and `finding` fields a triager (or your own tooling) can verify; the HTML is the readable attachment.
5. **The concrete impact, stated plainly.** For Heartbleed/Ticketbleed: what memory an attacker reads (session data, potentially the private key) and the consequence. For ROBOT: decryption of captured traffic and/or signature forgery, with the caveat about whether a practical oracle was confirmed. For a cert/cookie chain: the end state (session theft, MITM) and the steps. A verdict without an impact sentence is a note, and triagers treat it as one.
6. **What you did not do.** State that probes were read-only, that no data was exfiltrated beyond the proof, that you scanned at serial/low volume, and which intrusive probes (if any) you ran deliberately — so the defender can reconcile their IDS alerts with your testing window.

Keep the severity you claim aligned to the program's rubric, and resist inflating a configuration weakness into a vulnerability: a correctly-scoped Low with a clean PoC builds more reputation than a "Critical: TLS 1.0 enabled" that gets closed as informational.

### Worked example — a reportable finding

The shape that survives triage, for a ROBOT verdict on an in-scope auth host:

> **Title:** RSA decryption/forgery oracle (ROBOT) on `login.example.com:443`
>
> **Host/scope:** `login.example.com` (in scope per program policy §2), resolved `198.51.100.10`, SNI `login.example.com` — certificate CN/SAN confirmed via `-S` (attached) to be the in-scope vhost, not a shared edge cert.
>
> **Tool & version:** `testssl.sh 3.3dev` (commit from `--help`), invocation `./testssl.sh --BB https://login.example.com`.
>
> **Evidence:** attached `login.example.com-p443-*.json` (pretty, `--severity HIGH`) and the colored `--BB` output block showing the `VULNERABLE` verdict and the specific oracle variant testssl identified.
>
> **Impact:** the server performs RSA key exchange and leaks a distinguishable response to malformed PKCS#1v1.5 padding, giving a Bleichenbacher oracle. An attacker who captures a TLS session using RSA key exchange can decrypt it offline, and can forge a signature with the server's key — enabling impersonation of `login.example.com`. (If a practical oracle was not fully confirmed, say so and scope the claim to "oracle behavior observed.")
>
> **Remediation:** disable RSA key exchange (prefer ECDHE-only cipher suites) or patch the affected TLS stack; re-test with `./testssl.sh --BB` to confirm `not vulnerable`.
>
> **Testing hygiene:** single-host, serial, read-only probes; no data exfiltrated beyond the verdict; `--BB` is an IDS-visible probe, run deliberately at `<timestamp>`.

That template — host-proof, minimal evidence, concrete impact, exact remediation, hygiene statement — is the difference between an accepted TLS finding and an auto-closed scanner dump.

## References

- testssl.sh man page (3.3dev), full option reference — https://raw.githubusercontent.com/testssl/testssl.sh/3.3dev/doc/testssl.1.md
- testssl.sh repository and README (live org) — https://github.com/testssl/testssl.sh
- testssl.sh container image — https://ghcr.io/testssl/testssl.sh
- Qualys SSL Labs Server Rating Guide (basis for testssl's grade) — https://github.com/ssllabs/research/wiki/SSL-Server-Rating-Guide
- Heartbleed (CVE-2014-0160) — https://nvd.nist.gov/vuln/detail/CVE-2014-0160
- Ticketbleed (CVE-2016-9244) — https://nvd.nist.gov/vuln/detail/CVE-2016-9244
- ROBOT attack — https://robotattack.org/
- DROWN (CVE-2016-0800) — https://nvd.nist.gov/vuln/detail/CVE-2016-0800

*Research supplement added 2026-11-16, grounded in the testssl.sh 3.3dev man page and repository README plus the SSL Labs Server Rating Guide and the primary CVE/advisory sources listed above (model: claude-opus-4-8).*
