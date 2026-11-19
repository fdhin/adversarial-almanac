---
name: "wordbrutepress"
categories:
  - blackarch-cracker
version: "30.5165648"
homepage: "http://www.homelab.it/index.php/2014/11/03/wordpress-brute-force-multithreading/"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-26"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "claudioviviani/wordbrutepress"
github-stars: 86
github-pushed: "2017-04-21"
github-archived: false
github-issues: 2
github-match: "search"
gh-checked: "2026-11-07"
worth: 2.4
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# wordbrutepress

> Multi-threaded WordPress login brute forcer with both wp-login.php and XML-RPC modes.

## What it's for

WordBrutePress (Claudio Viviani) dictionary-attacks a single WordPress username over HTTP or HTTPS using either the standard `wp-login.php` form or the XML-RPC login method, which some setups leave reachable when the login page itself is hardened or rate-limited. It runs multiple threads, rotates the User-Agent per request to look less scripted, and streams large wordlists instead of loading them into memory. One run covers one user and one wordlist: it neither enumerates usernames nor defeats CAPTCHAs, so reach for it when the username is already known and you want a fast, polite-on-bandwidth password test against a site you are authorized to assess.

## Typical use cases

### Brute force through the standard login form

Attack `wp-login.php` directly with a wordlist.

```
wordbrutepress -S -t https://target.example.com -u user -w wordlist.txt
```

Outcome: POSTs each candidate to the login form from several threads with rotating User-Agents and reports the password on a successful login; append `--timeout N` to raise the per-request timeout in seconds on slow hosts.

### Brute force through XML-RPC instead

Use the alternate login path when the form is protected.

```
wordbrutepress -X -t https://target.example.com -u user -w wordlist.txt
```

Outcome: sends the same dictionary through the XML-RPC interface, judging each attempt by the returned fault code rather than an HTTP status — the mode that typically survives plugins that rate-limit `wp-login.php`.

*No corpus evidence — derived from upstream documentation.*
