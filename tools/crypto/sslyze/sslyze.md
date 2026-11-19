---
name: "sslyze"
categories:
  - blackarch-crypto
version: "6.3.1"
homepage: "https://github.com/nabla-c0d3/sslyze"
packaged-by: [blackarch, kali, aur]
blackarch-builddate: "2026-04-12"
build-system: python
license: AGPL-3.0-or-later
meta-checked: "2026-11-09"
github-repo: "nabla-c0d3/sslyze"
github-stars: 3795
github-pushed: "2026-10-31"
github-archived: false
github-issues: 33
github-match: "exact"
gh-checked: "2026-11-07"
worth: 7.1
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 19
gh-activity-checked: "2026-11-09"
---

# sslyze

> Fast SSL/TLS server-configuration scanner in Python — protocols, cipher suites, certificates and known TLS attacks, with Mozilla-compliance verdicts and JSON output.

*No corpus evidence — derived from upstream documentation.*

## What it's for

SSLyze (by Alban Didevez / nabla-c0d3) connects to a server and analyzes its SSL/TLS configuration: which protocol versions and cipher suites are enabled, the certificate chain and its validation against trust stores, elliptic curves, and exposure to known attacks such as Heartbleed, ROBOT and OpenSSL CCS injection. It is built for volume and automation — hundreds of thousands of servers scanned daily — and its default behavior doubles as a compliance gate: results are checked against Mozilla's recommended "intermediate" TLS configuration and the process exits non-zero when a target is not compliant, so it drops straight into CI/CD. The same scan engine is exposed as a documented Python API, and results can be serialized to JSON. Beyond HTTPS it speaks the STARTTLS handshakes of SMTP, XMPP, LDAP, POP3, IMAP, FTP, RDP and PostgreSQL. It reports configuration; it does not exploit what it finds, and it is a scanner, not a TLS proxy.

## Typical use cases

### Scan a server's TLS configuration

```
python -m sslyze www.example.com '[2001:db8::10]:443'
```

Outcome: a per-target report covering certificate chain and trust-store validation, enabled TLS versions, cipher suites per version, and results of the built-in vulnerability checks (Heartbleed, ROBOT, CCS). Multiple targets can be given at once; non-default ports are written as `host:port` (bracketed for IPv6). Results are also checked against Mozilla's "intermediate" profile and the exit code reflects compliance.

### Enforce a stricter baseline

```
python -m sslyze --mozilla_config=modern www.example.com
```

Outcome: compliance is judged against Mozilla's "modern" profile instead; a FAILED verdict lists exactly what is rejected (old TLS versions, RSA-only certificate types, weak ciphers), and the non-zero exit code lets a pipeline fail the build.

### Check against a custom in-house profile

```
python -m sslyze --custom_tls_config custom_tls_config_example.json www.example.com
```

Outcome: the compliance verdict is computed against your own JSON file in Mozilla's TLS-configuration format, so organizational baselines can be enforced with the same exit-code semantics as the built-in profiles.

### Scan non-HTTP services via STARTTLS

```
python -m sslyze --starttls=smtp mail.example.com:25
```

Outcome: SSLyze performs the SMTP STARTTLS handshake before analyzing the session, then reports the same protocol, cipher and certificate findings as for HTTPS. Protocols: smtp, xmpp, xmpp_server, pop3, imap, ftp, ldap, rdp, postgres — or `--starttls=auto` to deduce the protocol from each target's port.

### Produce machine-readable results

```
python -m sslyze --quiet --json_out scan.json www.example.com
```

Outcome: the full scan result is serialized to `scan.json` (the same node/attribute structure as the Python API's ScanResult objects) while `--quiet` suppresses the console report; `--json_out -` prints the JSON to stdout instead of a file.

### Sweep many targets from a list

```
python -m sslyze --targets_in targets.txt
```

Outcome: targets are read from the file — one `host:port` per line — and each is scanned in turn, which turns SSLyze into a batch auditor for whole ranges of hosts.
