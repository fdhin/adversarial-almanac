---
name: "mkbrutus"
categories:
  - blackarch-cracker
version: "27.ddd5f8e"
homepage: "http://mkbrutusproject.github.io/MKBRUTUS/"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-25"
build-system: scripts
license: GPL3
meta-checked: "2026-11-09"
github-repo: "mkbrutusproject/MKBRUTUS"
github-stars: 183
github-pushed: "2020-06-19"
github-archived: false
github-issues: 10
github-match: "search"
gh-checked: "2026-11-07"
worth: 2.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# mkbrutus

> Dictionary brute-forcer for MikroTik RouterOS devices that speak the proprietary management API.

## What it's for

MKBRUTUS attacks boxes running MikroTik RouterOS 3.x or newer that have the API service enabled — TCP 8728 by default — instead of (or alongside) SSH/Winbox/HTTPS. Because the RouterOS API uses a vendor-specific protocol rather than Telnet-style banners, generic brute-forcers cannot drive it; MKBRUTUS embeds the vendor's API client code and speaks it natively from Python 3. It runs a straightforward dictionary attack (one user, one wordlist, optional delay between attempts) and is intended for authorized audits of RouterOS gear. It does not touch SSH, Winbox (8291), or the web interface — only the API port.

## Typical use cases

### Brute-force the default admin account over the API

```
python3 mkbrutus.py -t 198.51.100.10 -d passwords.txt
```

Outcome: connects to the RouterOS API on TCP 8728 and tries each dictionary password for user `admin` (the defaults), printing a line per attempt and flagging the working password when one authenticates.

### Custom port, username, and pacing

```
python3 mkbrutus.py -t 198.51.100.10 -p 8728 -u operator -d passwords.txt -s 2 -q
```

Outcome: same attack against user `operator`; `-s 2` waits 2 seconds between attempts (default 1) to stay under RouterOS rate protections, and `-q` suppresses the per-attempt output, reporting only the result.

*No corpus evidence — derived from upstream documentation.*
