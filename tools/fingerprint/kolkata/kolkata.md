---
name: "kolkata"
categories:
  - blackarch-fingerprint
version: "3.0"
homepage: "https://www.blackhatlibrary.net/Kolkata"
packaged-by: [blackarch]
blackarch-builddate: "2025-12-18"
build-system: perl
license: GPL-2.0-or-later
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# kolkata

> Perl web-application fingerprinter that identifies WordPress, Joomla and MediaWiki versions by hashing a known per-app file.

## What it's for

kolkata is a small Perl fingerprinting engine for web applications: it walks
the YAML signature files in its `sigs/` directory (WordPress, Joomla and
MediaWiki ship with it), downloads each application's designated `check_file`,
computes its MD5 digest, and reports the application and exact version when
the digest matches a known-version signature. Requests go through the bundled
libwhisker2 (LW2) HTTP library with anti-IDS encoding and a browser
User-Agent, which is where its "cryptography plus IDS evasion" pitch comes
from. It only versions the applications it has signatures for, and it must run
from the directory containing `sigs/` (it reads `./sigs/*.yml` relative to the
current directory).

## Typical use cases

### Identify the version of a web application

```
./kolkata.pl -d target.example.com
```

Outcome: for each signature file, downloads the application's check file from
the target and compares its MD5 against the version digests; on a match it
prints the application name and version (e.g. WordPress 3.x) and exits.

### Fingerprint an app installed under a subpath

```
./kolkata.pl -d target.example.com -p /blog/ -v
```

Outcome: prefixes every check-file request with `/blog/` so applications not
installed at the web root are still found; `-v` additionally prints each
digest comparison as it happens.

*No corpus evidence — derived from upstream documentation.*
