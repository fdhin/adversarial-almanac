---
name: "ftpmap"
categories:
  - blackarch-fingerprint
version: "52.cbeabbe"
homepage: "http://wcoserver.googlecode.com/files/"
packaged-by: [blackarch]
blackarch-builddate: "2015-10-17"
build-system: autotools
license: GPL2
meta-checked: "2026-11-09"
github-repo: "ovpn-to/ftpmap"
github-stars: 4
github-pushed: "2019-07-09"
github-archived: false
github-issues: 1
github-match: "search"
gh-checked: "2026-11-07"
worth: 1.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# ftpmap

> Identify FTP server software and version from protocol behavior, not just the banner.

## What it's for

ftpmap scans remote FTP servers to identify what software and which version they
run, even when the banner has been changed or removed or features have been
disabled. It probes the server's protocol behavior, derives a numeric
fingerprint, and compares it against a database of per-FTP-daemon signatures;
with credentials it can also assess FTP PORT sequence prediction difficulty and
flag known exploits for the detected version (ships `db/ftp-versions-db` and
`db/ftp-exploit-db`). It was written as a proof of concept that hiding or
faking FTP banners is not a security measure. Original author Frank Denis
(pure-ftpd); the packaged C rewrite was maintained by Hypsurus.

## Typical use cases

### Fingerprint an FTP server anonymously

```
ftpmap -s ftp.example.com
```

Outcome: prints the banner-based guess, then a list of numbers (the behavioral
fingerprint) and the best-matching server software and version from its
signature database. If several IPs resolve from the name, each is scanned
sequentially.

### Scan a non-standard port

```
ftpmap -p 2121 -s 198.51.100.10
```

Outcome: same fingerprinting run against the FTP service on port 2121 instead
of the default 21.

### Fingerprint with credentials

```
ftpmap -U user -P Password1 -s ftp.example.com
```

Outcome: logs in before probing, which additionally lets ftpmap report the FTP
PORT sequence prediction difficulty — a low difficulty means sessions can be
hijacked without the password. Defaults are anonymous / `hello@world` when
`-U`/`-P` are omitted.

*No corpus evidence — derived from upstream documentation.*
