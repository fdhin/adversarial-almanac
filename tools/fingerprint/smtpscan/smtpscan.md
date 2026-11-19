---
name: "smtpscan"
categories:
  - blackarch-fingerprint
version: "0.5"
homepage: "http://packetstormsecurity.com/files/31102/smtpscan.5.tar.gz.html"
packaged-by: [blackarch]
blackarch-builddate: "2025-07-18"
build-system: perl
license: GPL2
meta-checked: "2026-11-09"
github-repo: "julienbordet/smtpscan"
github-stars: 0
github-pushed: "2018-05-26"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-09"
worth: 1.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# smtpscan

> Remote SMTP server version detector: fingerprints which mail software a host runs from how it reacts to malformed SMTP commands, even when the banner has been altered or hidden.

## What it's for

Guessing the mail server software (and often its exact version) on a remote host whose SMTP banner is missing, generic or deliberately falsified. Written in Perl by Julien Bordet (zejames), it runs a battery of about fifteen unusual or edge-case SMTP requests whose correct answers are not precisely pinned down by the RFCs, records the SMTP status codes each one produces, and matches that sequence against a fingerprint database of known MTAs. When no fingerprint matches exactly, it reports the fingerprints with the fewest differing responses. It can also work from a domain name, resolving and scanning the MX hosts. It is purely a version detector: it does not enumerate users, test for open relays, or send mail. BlackArch installs the fingerprint and test databases under `/usr/share/smtpscan/`. The author keeps the canonical source on GitHub (julienbordet/smtpscan); its tree is byte-identical to the packaged 0.5 tarball.

## Typical use cases

### Fingerprint the MTA on a mail host

Identify what SMTP software and version a single server really runs.

```
smtpscan mail.example.com
```

Outcome: performs the test battery against port 25, then prints the fingerprinted server software and version (or the nearest candidate fingerprints with their match quality); `-v` adds per-test detail.

### Resolve a domain and scan its primary mail exchanger

Fingerprint the MTA behind a domain's MX record instead of a named host.

```
smtpscan -D example.com
```

Outcome: performs an MX lookup for the domain and scans the highest-priority mail exchanger; `-n 2` selects the second exchanger instead.

### Scan every mail exchanger with a single connection each

Cover all MX hosts while being gentle with servers that dislike repeated connections.

```
smtpscan -D example.com -a -c -i 15
```

Outcome: scans every IP returned by the MX lookup; `-c` reuses one connection per server via SMTP `RSET` instead of reconnecting per test, and `-i 15` waits up to 15 seconds per connection for slow gateways.

### Probe a nonstandard SMTP port

Fingerprint a mail service listening somewhere other than 25.

```
smtpscan -p 2525 target.example.com
```

Outcome: runs the same tests against the specified port and reports the server software fingerprinted there.

*No corpus evidence — derived from upstream documentation.*
