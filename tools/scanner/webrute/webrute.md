---
name: "webrute"
categories:
  - blackarch-scanner
version: "3.3"
homepage: "https://github.com/BlackArch/webrute"
packaged-by: [blackarch]
blackarch-builddate: "2025-07-18"
build-system: perl
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "BlackArch/webrute"
github-stars: 7
github-pushed: "2013-12-06"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 1.8
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# webrute

> Classic Perl brute forcer (Dennis Rand, 2004) that discovers hidden directories, files and parameter values on a web server.

## What it's for

WeBrute attacks a web server with either a wordlist or incrementally generated character combinations, logging every path that does not return the default "not found" answer. Its distinguishing trick is templating: the `<bruteforce>` marker inside `-prepend`/`-append` strings lets you brute force values anywhere in a URL — a filename suffix, a path prefix, or the value of a query parameter like `showarticle.asp?articleid=<bruteforce>`. Match logic is adjustable (log only responses containing a chosen error string, or reverse to non-404s), and it can send cookies, custom referer, user agent, HTTP/1.1, SSL and request delays. The BlackArch package ships the original single-file `webrute.pl` script; it is a 2004 tool with no threading, so rate it accordingly.

## Typical use cases

### Brute force paths with a wordlist

```
webrute.pl -t 198.51.100.10 -p 80 -w common.txt
```

Outcome: requests `/word` for every wordlist entry and logs everything that does not answer like the server's 404 baseline; findings appear in the console log (and log files unless `-nolog`).

### Incrementally generate candidate names

```
webrute.pl -t 198.51.100.10 -p 80 -i all -min 1 -max 3
```

Outcome: instead of a wordlist, generates every 1–3 character combination of letters, digits and specials (`-i str,int` limits the alphabet to alphanumerics) and tests each as a path — for short, guessable names.

### Brute force inside a path template

```
webrute.pl -t 198.51.100.10 -p 80 -i all -min 1 -max 3 -prepend secret/ -append <bruteforce>.asp
```

Outcome: the `<bruteforce>` marker is replaced by each candidate, so requests hit `secret/<candidate>.asp` — the same mechanism brute-forces parameter values, e.g. `-prepend "showarticle.asp?articleid=<bruteforce>"`.

### Log only a specific error message

```
webrute.pl -t 198.51.100.10 -p 80 -i int -min 1 -max 3 -prepend "showproduct.asp?id=<bruteforce>" -error Denied
```

Outcome: flips the match rule from "not 404" to "response contains the string Denied", which is how you probe for authorization failures or debug text instead of hidden files.

### Scan an HTTPS host with session cookies

```
webrute.pl -t 198.51.100.10 -p 443 -ssl -w common.txt -setcookie "SESSIONID=abc123; Path=/" -useragent "Mozilla/5.0" -httpver HTTP/1.1
```

Outcome: same enumeration over TLS on port 443, authenticated with the given cookie and a chosen user agent over HTTP/1.1 — needed whenever the interesting paths sit behind a login.

*No corpus evidence — derived from upstream documentation.*
