---
name: "xmlrpc-bruteforcer"
categories:
  - blackarch-webapp
version: "36.6a46371"
homepage: "https://github.com/kavishgr/xmlrpc-bruteforcer"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-14"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "kavishgr/xmlrpc-bruteforcer"
github-stars: 63
github-pushed: "2025-08-03"
github-archived: true
github-issues: 4
github-match: "exact"
gh-checked: "2026-11-07"
worth: 0.6
worth-basis: "historical (archived/gone upstream)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# xmlrpc-bruteforcer

> Password brute forcer for WordPress accounts that works through the xmlrpc.php interface.

## What it's for

A small Python 3 script that brute-forces a known WordPress username's password against a site's `xmlrpc.php` endpoint, the classic fallback when `wp-login.php` is rate-limited, CAPTCHA-protected, or blocked while the XML-RPC interface is still exposed. It takes a target URL (which must include `xmlrpc.php`), a password wordlist file, and a single username; it first checks whether the target is vulnerable (i.e. XML-RPC authentication attempts are answered) and then walks the wordlist, printing progress counts as it goes. The upstream project is discontinued and username-list mode was never finished — one username per run. It does nothing else: no enumeration, no exploitation beyond recovering valid credentials.

## Typical use cases

### Brute-force one WordPress account via XML-RPC

Point the script at an exposed xmlrpc.php with a wordlist and a single username.

```
xmlrpc-bruteforcer http://target.example.com/xmlrpc.php passwords.txt user
```

Outcome: the script prints an "Examining Target" check and whether the target is vulnerable, then a live "Tried: N passwords" counter; on a hit it prints the working `Login:`/`Password:` pair and exits. Re-run it once per username to test other accounts.

No corpus evidence — derived from upstream documentation.
