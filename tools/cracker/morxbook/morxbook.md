---
name: "morxbook"
categories:
  - blackarch-cracker
version: "1.0"
homepage: "http://www.morxploit.com/"
packaged-by: [blackarch]
blackarch-builddate: "2025-07-18"
build-system: perl
license: custom:unknown
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# morxbook

> 2013-era Perl dictionary attack against a single Facebook account's login over HTTPS.

## What it's for

MorxBook (Simo Ben Youssef, MorXploit Research) performs a dictionary attack on one specific Facebook user — identified by email address or profile name — by POSTing credentials to `www.facebook.com/login.php` over HTTPS via Net::SSLeay. It takes exactly two arguments: the login and a wordlist file. It is a single-target, rate-limited HTTP attacker (~2 attempts/second on the author's own connection), with no proxy rotation, threading, or captcha solving. The script hardcodes the login-form tokens of the 2013 Facebook flow, and Facebook long since added captcha and rate limiting, so today the tool is of historical and educational interest rather than practical use; its value is as a minimal example of an HTTPS form-login brute-forcer, easily adapted to other form targets in your own lab.

## Typical use cases

### Run a dictionary attack against one account

```
perl morxbook.pl user@example.com wordlist.txt
```

Outcome: prints a `[-] <password> -> Failed` line per candidate tried against the Facebook login endpoint, and a `[+] CRACKED! Your password is ...` banner on success; it exits immediately if the wordlist cannot be opened.

*No corpus evidence — derived from upstream documentation.*
