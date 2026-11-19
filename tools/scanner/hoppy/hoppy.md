---
name: "hoppy"
categories:
  - blackarch-scanner
version: "1.8.1"
homepage: "https://labs.portcullis.co.uk/downloads/"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-25"
build-system: scripts
license: GPL2
meta-checked: "2026-11-09"
github-repo: "Seabreg/hoppy"
github-match: "search"
github-stars: 1
github-pushed: "2019-03-18"
github-archived: false
github-issues: 0
gh-checked: "2026-11-09"
worth: 1.4
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# hoppy

> Portcullis' Python "HTTP Options Prober": fires a configurable list of HTTP methods and malformed requests at a web server and summarizes which are enabled or leak information.

## What it's for

hoppy ([H]ttp [O]ptions [P]rober in [PY]thon, by deanx, v1.8.1) reads a methods file and sends each listed request to the target, then summarizes the responses to show which methods are enabled, mishandled, or disclose information. It includes a crude spider that finds directories and tests WebDAV methods against them, an app-only mode that confines testing to a single virtual host, proxy support via the `HTTP_PROXY` environment variable, and verbose modes that dump the full request/response conversation. The methods and response-keyword lists are plain editable files, so the test battery is fully user-controlled. The `github-repo` field points at a third-party mirror (Seabreg/hoppy) of the original 1.8.1 distribution — Portcullis' own downloads site is gone.

## Typical use cases

### Probe a server's HTTP methods

Run the stock battery against a target.

```
hoppy -h http://target.example.com
```

Outcome: sends every request from the default `http-methods` list and prints a per-method summary (`.` = completed, `!` = timeout); enabled unusual methods such as TRACE or WebDAV verbs are the leads to investigate.

### Stay inside a single virtual host

Confine testing to one vhost and location.

```
hoppy -H target.example.com -l /uploads -f test.txt
```

Outcome: `-H` is app-only mode — every request carries a correctly formed `Host` header so testing stays on that virtual host — here probing the `/uploads/test.txt` location.

### Capture full transcripts without filtered status codes

Keep the noise and the conversations.

```
hoppy -h http://target.example.com -4 -vvv -S report.txt
```

Outcome: `-4` stops suppressing 404/100/400/error lines, `-vvv` prints each raw request and response pair, and `-S` saves the whole run to `report.txt` for later review.

*No corpus evidence — derived from upstream documentation.*
