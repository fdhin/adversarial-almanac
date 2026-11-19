---
name: "dsfs"
categories:
  - blackarch-webapp
version: "36.8e9f8e9"
homepage: "https://github.com/stamparm/DSFS"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-12"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "stamparm/DSFS"
github-stars: 61
github-pushed: "2019-11-13"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.3
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# dsfs

> A fully functional File inclusion vulnerability scanner (supporting GET and POST parameters) written in under 100 lines of code.

## What it's for

DSFS (Damn Small FI Scanner) is Miroslav Stampar's file-inclusion scanner in under 100 lines of Python: point it at a URL with parameters and it tests each GET (or POST, via `--data`) parameter for local/remote file inclusion and scheme inclusion (`data://` wrappers). Reach for it when you want a zero-dependency, readable FI check in a script you can actually audit line-by-line — its value is exactly its size, not coverage; it is not a substitute for a full web scanner. It supports an HTTP proxy and the `User-Agent`, `Referer` and `Cookie` headers for authenticated targets. The BlackArch package installs the script as `/usr/bin/dsfs`.

## Typical use cases

### Test a GET parameter for file inclusion

The canonical run against a URL with a candidate parameter.

```
dsfs -u "http://target.example.com/page.php?file=README.txt"
```

Outcome: probes the `file` parameter with LFI/RFI and scheme-inclusion payloads and prints findings such as `GET parameter 'file' appears to be (R)FI vulnerable` with the proof-of-concept URL, ending with `scan results: possible vulnerabilities found` on success.

### Test a POST parameter

Target a form field instead of the query string.

```
dsfs -u "http://target.example.com/page.php" --data="file=README.txt"
```

Outcome: runs the same inclusion payload set against the POST parameter named in `--data`, reporting `POST parameter 'file' appears to be ... vulnerable` in the same shape as GET testing.

### Scan through a proxy with custom headers

Inspect or authenticate the scan traffic.

```
dsfs -u "http://target.example.com/page.php?file=doc.txt" --proxy "http://127.0.0.1:8080" --cookie "session=abc123" --random-agent
```

Outcome: sends every probe through the intercepting proxy with the session cookie applied and a randomly chosen User-Agent (`--user-agent`/`--referer` set those headers explicitly instead), so the whole test is visible in Burp and lands authenticated.

No corpus evidence — derived from upstream documentation (README sample runs and options).
