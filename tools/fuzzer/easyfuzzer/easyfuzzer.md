---
name: "easyfuzzer"
categories:
  - blackarch-fuzzer
version: "3.6"
homepage: "http://www.mh-sec.de/downloads.html.en"
packaged-by: [blackarch]
blackarch-builddate: "2024-09-05"
build-system: perl
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "Seabreg/easyfuzzer"
github-stars: 0
github-pushed: "2019-09-14"
github-archived: false
github-issues: 0
github-match: "search"
gh-checked: "2026-11-07"
worth: 1.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# easyfuzzer

> Marc Heuse's template-driven request fuzzer: replay a request template containing `__FUZZ__` statements, capture every response under /tmp/easyfuzzer, and diff them to find behavioral differences.

*No corpus evidence — derived from upstream documentation.*

## What it's for

easyfuzzer takes a plain-text request template — a raw HTTP request or any line-based TCP dialogue — and systematically expands the fuzz statements it contains, sending one variant per connection and storing each request/response pair on disk for analysis. Web requests are the primary target ("not only for web" per the author): helper scripts convert WSDL and WBXML output into templates, strip proxy and HTTP/1.1 chrome from captured requests, and automate fuzzing of everything passing through a webscarab proxy session. Response comparison is built in — the diff helper is what actually flags interesting requests. Perl/bash, platform independent; the upstream README notes it was not intended for public distribution, so treat licensing accordingly.

## Typical use cases

### Fuzz a web target from a template

Send every fuzz variant of a prepared HTTP request template.

```
easyfuzzer -X fuzz-template.txt target.example.com 80
```

Outcome: each variant is sent to the target over plain HTTP (`-X`), and every request/response pair is written to /tmp/easyfuzzer/fuzzfile.\<n\>.req/.resp; run easyfuzzer-diff on the template afterwards to see which responses diverge.

### Fuzz an HTTPS target

Same sweep against a TLS listener.

```
easyfuzzer -X -s fuzz-template.txt target.example.com 443
```

Outcome: identical run with encryption enabled (`-s`); the same on-disk request/response capture is produced for later diffing.

### Turn a captured request into a fuzz template

Convert a raw captured request into a ready-to-fuzz template.

```
prepare4easyfuzzer -f -c captured.txt fuzz-template.txt
```

Outcome: proxy and HTTP/1.1-specific lines are stripped from the captured request, a fuzz statement is inserted into every GET/POST parameter (`-f`) and every cookie (`-c`), and the resulting template is written for easyfuzzer — the same file is also directly sendable with netcat.

### Diff the collected responses

Find which fuzz variants changed the application's behavior.

```
easyfuzzer-diff fuzz-template.txt
```

Outcome: MD5-checks and diffs the cleaned responses belonging to that template, writing one diff per response plus a complete summary in fuzz-template.txt.diff — the requests whose responses deviate from the baseline are the ones to inspect.

### Auto-fuzz everything captured through a webscarab proxy

Fuzz each request as it is seen by the webscarab web proxy.

```
easyfuzzer-webscarab-watcher -a target.example.com ~/.webscarab
```

Outcome: watches the webscarab data directory; every request to target.example.com is converted, fuzzed and diffed automatically in the background, and `-a` also re-fuzzes requests already seen before the watcher started.
