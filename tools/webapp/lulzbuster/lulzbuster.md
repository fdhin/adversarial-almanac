---
name: "lulzbuster"
categories:
  - blackarch-webapp
version: "2.0.1"
homepage: "http://www.nullsecurity.net/tools/scanner.html"
packaged-by: [blackarch]
blackarch-builddate: "2026-07-06"
build-system: make
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "noptrix/lulzbuster"
github-stars: 141
github-pushed: "2026-07-06"
github-archived: false
github-issues: 0
github-match: "search"
gh-checked: "2026-11-07"
worth: 6.0
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 10
gh-activity-checked: "2026-11-09"
---

# lulzbuster

> Multithreaded HTTP(S) directory and file brute-forcer written in C on libcurl.

## What it's for

lulzbuster (nullsecurity.net) discovers valid paths on a web server: given a start URL and a wordlist, it fires concurrent HTTP requests and reports every response whose status code was not excluded. It is built for speed and scriptability — dozens of tunable knobs for threads, request type and HTTP version, user-agent rotation, custom headers, HTTP auth, mTLS client certificates, proxies (including authenticated ones), request timeouts, and per-request delay. Use it for content discovery where you want a small, fast binary instead of a Python framework; it does not parse or judge response bodies beyond the configurable filters.

## Typical use cases

### Quick directory scan with defaults

```
lulzbuster -s https://target.example.com
```

Outcome: brute-forces the start URL with the bundled default wordlist at 35 threads, printing each hit (status code, URL, size) as it lands; excluded codes (400,404,500-503 by default) never appear.

### Custom wordlist, more threads, extension list

```
lulzbuster -s https://target.example.com -w /usr/share/seclists/Discovery/Web-Content/raft-medium-directories.txt -t 50 -A '/,.php,~bak'
```

Outcome: scans the custom wordlist with 50 workers, additionally requesting each word with `/`, `.php`, and `~bak` appended, surfacing backed-up or extension-protected files.

### Authenticated and cookie-gated content

```
lulzbuster -s https://target.example.com -c 'Cookie: session=user' -a user:Password1
```

Outcome: sends the session cookie with every request and authenticates via HTTP Basic, enumerating paths only visible to a logged-in session.

### Tune what counts as a hit

```
lulzbuster -s https://target.example.com -x 400,401,403,404,500 -f
```

Outcome: additionally excludes 401/403 responses from the report and follows redirects, so moved paths (`-A /` first is the cheaper alternative) still resolve to real hits.

No corpus evidence — derived from upstream documentation.
