---
name: "dirscanner"
categories:
  - blackarch-scanner
version: "0.1"
homepage: "http://packetstormsecurity.com/files/117773/Directory-Scanner-Tool.html"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-25"
build-system: scripts
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# dirscanner

> Interactive Python 2 scanner that checks a web server for admin panels, phpMyAdmin installs and shell paths.

## What it's for

dirscanner (Ajith KP's "DirDictionaryBruter") is a menu-driven checklist
scanner rather than a general fuzzer: you launch it, type a URL, and pick
what to look for — administrative pages (with separate PHP/ASP/JSP/CGI/CFM
and BruteForce path lists), phpMyAdmin installations, uploaded web shells,
or generic directories. It takes no command-line options; everything happens
through its interactive prompts, and the path lists it walks are plain text
files shipped alongside it. It is Python 2 / urllib2 code, so it needs a
Python 2 interpreter (which distro packages wire up in the launcher).

## Typical use cases

### Run an interactive checklist scan

Check one host for common admin, phpMyAdmin and shell paths.

```
dirscanner
```

Outcome: prints the banner, prompts `URL[http/https]>`, verifies the server
is online, then offers `Select Option: 1 Directory Scan / 2 Admin Page Scan
/ 3 phpMyAdmin Scan / 4 Shell Scan` (admin scan additionally asks the script
type). It requests each bundled path, prints `>>> Found <url>` for hits and
`[x] Not Found` for misses, ends with `Total scanned:`/`Found:` counters,
and appends every hit to `logs/<hostname>.log` under the install directory.

### Extend the path lists

Teach it organisation-specific paths.

```
echo /panel/login.php >> /usr/share/dirscanner/files/admin/php
```

Outcome: the next admin-page scan includes the added path — each file under
`/usr/share/dirscanner/files/` (`admin/php`, `admin/asp`, `shells`,
`phpMyAdmin`, `directory`, ...) is a one-path-per-line wordlist read at
scan time, so customising is just editing those files.

*No corpus evidence — derived from the packaged source (PacketStorm
"Directory Scanner Tool": scanner.py and its Read Me).*
