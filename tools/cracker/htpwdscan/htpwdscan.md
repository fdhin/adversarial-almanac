---
name: "htpwdscan"
categories:
  - blackarch-cracker
version: "23.e995d6f"
homepage: "https://github.com/lijiejie/htpwdScan"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-12"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "lijiejie/htpwdScan"
github-stars: 918
github-pushed: "2021-09-07"
github-archived: false
github-issues: 8
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.0
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# htpwdscan

> HTTP weak-password scanner for auth realms, login parameters and leaked-credential stuffing

## What it's for

htpwdScan attacks web authentication from the client side: HTTP Basic/Digest/NTLM logins, GET/POST parameter brute forcing with success/failure markers, and credential stuffing from leaked databases. It accepts a raw HTTP request file captured from a browser or proxy, can wrap dictionary values in transform functions (`md5(...)`, `capitalize(...)`, or custom Python functions) to satisfy password policies, iterates password-first to reduce account lockouts, and rotates requests through HTTP proxy lists with spoofed `X-Forwarded-For` headers. Reach for it when a login flow is too complex for a simple form attack — NTLM realms, custom headers, multi-parameter POSTs — or when testing how an endpoint withstands a leaked-credential replay.

## Typical use cases

### Brute force HTTP Basic, Digest or NTLM

```
htpwdscan -u https://example.com/protected/ --auth users.txt passwords.txt --pass-first
```

Outcome: tries every user against every password on the URL's authentication realm; when the server offers several auth schemes the tool prompts for which to use (NTLM is the usual choice for Exchange/OWA). `--pass-first` fixes each password and cycles usernames first, lowering lockout risk; a single space-separated user:password dump file can replace the two lists. Hits are printed and appended to `_cracked.accounts.txt`.

### Replay a captured login request

```
htpwdscan -f request.txt -d login=users.txt passwd=passwords.txt --fail "login failed"
```

Outcome: parses the raw HTTP request in request.txt (method, path, headers, body), substitutes candidates into the named parameters, and judges each response by whether the body contains the failure string — a response without it counts as cracked. Use `--header-fail "Set-Cookie: ..."` when success is only visible in headers, and `--debug` to dump responses while choosing a marker.

### Brute force a GET parameter

```
htpwdscan -u "http://example.com/search?key=123" -d key=words.txt --get --fail "no results"
```

Outcome: floods the `key` parameter with dictionary values via GET (POST is the default method); requests whose body avoids the failure marker are reported as successes.

### Stuff a leaked-credential database

```
htpwdscan -f request.txt --database login,passwd=leaked_db.txt --regex "(\S+)\s+(\S+)" --fail "login failed"
```

Outcome: extracts login and password from each line of leaked_db.txt using the regex and replays the pairs against the endpoint in request.txt; add `--fake-ip` to spoof `X-Forwarded-For`/`Client-IP` per request and `--sleep N` to throttle.

### Validate a proxy list, then attack through it

```
htpwdscan --proxy-file proxies.txt --check-proxy -u http://example.com/ --suc "welcome" --get -o servers.txt
```

Outcome: verifies each proxy can reach the target and writes the working ones to servers.txt; the same `--proxy`/`--proxy-file` options then spread the actual attack across the surviving proxies.

*No corpus evidence — derived from upstream documentation.*
