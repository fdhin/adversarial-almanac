---
name: "smtpmap"
categories:
  - blackarch-fingerprint
version: "0.8.234_BETA"
homepage: "http://www.projectiwear.org/~plasmahh/software.html"
packaged-by: [blackarch]
blackarch-builddate: "2015-09-06"
build-system: autotools
license: GPL
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# smtpmap

> Identifies the exact SMTP — and POP3/FTP — server software and version on a host from behavioural fingerprints, ranked by probability.

## What it's for

smtpmap determines which mail-server software a host runs even when the greeting banner has been changed or stripped. It drives an SMTP conversation and compares the server's behaviour against a local fingerprint database using three independent methods — configuration fingerprinting, RFC-conformance fingerprinting, and an overall match — then prints version candidates with probabilities for each method (candidates above ~95% are reliable; agreement across methods identifies at least the software family). Since 0.8.212 it can also fingerprint POP3 (`-P`) and FTP (`-F`) services, on default or custom ports. It is deliberately not a field port scanner: each host takes minutes, and although wildcarded IP ranges (`1.2.*.*`) are accepted they can exhaust memory; DNS lookups can be disabled with `-d`. Fingerprints live in share files, and `-v` output lets you paste newly scanned fingerprints back into the database to extend it.

## Typical use cases

### Identify the MTA on a host

```
smtpmap target.example.com
```

Outcome: runs the full three-method fingerprint and prints three probability tables (Configuration, RFC, Overall), each listing candidate versions such as `Sendmail 8.12.6 -rv- 100 %` — agreement across the tables identifies the software despite a faked banner.

### Quick banner-only survey

```
smtpmap -f target.example.com
```

Outcome: skips all fingerprint probes and only shows what the service banners reveal — a fast overview of what runs where, with no version probabilities.

### Fingerprint POP3 and FTP services on the same host

```
smtpmap -P -F target.example.com
```

Outcome: scans the host as a POP3 and as an FTP server (combinable with `-S`; SMTP is the default when none is given), logging in anonymously where allowed — POP3 fingerprinting is most reliable with valid credentials via `--pop3-user`/`--pop3-pass`.

### Fingerprint a mail service on a non-standard port

```
smtpmap -S --smtp-port 2525 target.example.com
```

Outcome: runs the SMTP fingerprint against port 2525 instead of 25, producing the same probability tables; `--pop3-port` and `--ftp-port` work likewise.

### Show only high-confidence matches

```
smtpmap -n 98 target.example.com
```

Outcome: prints only fingerprints matching above 98 percent (at least three matches are always displayed), decluttering output for unusual servers.

### Watch the probes being sent

```
smtpmap -v -v target.example.com
```

Outcome: raises verbosity one step per `-v` — level 1 adds scanned fingerprints and statistics, level 2 logs which fingerprint sets were read and which commands were sent to the server; a third `-v` is heavy debug output.

*No corpus evidence — derived from upstream documentation.*
