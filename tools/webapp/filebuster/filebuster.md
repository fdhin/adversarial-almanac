---
name: "filebuster"
categories:
  - blackarch-webapp
version: "95.f2b04c7"
homepage: "https://github.com/henshin/filebuster"
packaged-by: [blackarch]
blackarch-builddate: "2025-07-18"
build-system: perl
license: GPL3
meta-checked: "2026-11-09"
github-repo: "henshin/filebuster"
github-stars: 223
github-pushed: "2023-02-25"
github-archived: false
github-issues: 2
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.1
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# filebuster

> An extremely fast and flexible Perl web fuzzer for content discovery.

## What it's for

Filebuster (by Tiago Sintra, @henshin) brute-forces web paths using the fast Perl Furl HTTP class with an optimized threading model. It stands out on fuzz-position control — an explicit `{fuzz}` tag places the injection point anywhere in the URL — plus multiple wordlists (including wildcards), extension appending, response filtering by status code, body substring or header substring, custom cookies and headers, HTTP/HTTPS/SOCKS proxies, TLS version selection and recursive scans. Use it as an alternative to gobuster/ffuf when per-URL fuzz positions and built-in response filtering matter.

## Typical use cases

### Quick fuzz with the built-in wordlist

Start with no preparation.

```
filebuster -u http://target.example.com/
```

Outcome: since v0.9.1 the wordlist is optional — the bundled "Fast" list loads automatically and discovered paths stream to the terminal as they are found.

### Fuzz a specific position in the URL

Target any part of the path, not just the web root tail.

```
filebuster -u 'https://target.example.com/{fuzz}' -w /usr/share/seclists/Discovery/Web-Content/directory-list-2.3-small.txt
```

Outcome: the `{fuzz}` tag marks where wordlist entries are injected; discovered paths are printed live, and multiple wordlists (or wildcards like `*.txt`) can be passed to `-w` with duplicates ignored.

### Append extensions to every candidate

Surface script and backup files.

```
filebuster -u http://target.example.com/ -w ~/wordlists/common.txt -e php,bak,old
```

Outcome: each word is also requested with `.php`, `.bak` and `.old` appended (`-e`), catching files that exist only with an extension.

### Filter noise and route through a proxy

Clean the output and capture the traffic.

```
filebuster -u 'http://target.example.com/{fuzz}.jsp' -w ~/wordlists/words.txt -t 3 -x http://127.0.0.1:8080 --hc 403,400 --hs 'does not exist'
```

Outcome: responses with status 403/400 (`--hc`) or whose body contains the given string (`--hs`; `--hsh` matches headers) are hidden, while 3 threads send all traffic through the local proxy at `127.0.0.1:8080` (`-x`) for inspection.
