---
name: "doser"
categories:
  - cracker
homepage: "https://github.com/Quitten/doser.go"
origin: "howtohunt"
recipe: "upstream"
packaged-by: []
build-system: go
license: custom:unknown
meta-checked: "2026-11-13"
github-repo: "Quitten/doser.go"
github-match: "exact"
github-stars: 542
github-pushed: "2023-09-15"
github-issues: 2
gh-checked: "2026-11-13"
worth: 4.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-13"
---

# doser

> Minimal HTTP DoS load tool in Go: floods a URL with parallel GET or POST requests and reports throttling and server errors as they appear.

## What it's for

doser (doser.go, inspired by hulk) is a deliberately tiny HTTP
denial-of-service utility: it hammers one URL with as many parallel request
goroutines as you ask for, either GETs or POSTs with a form payload, and
watches the responses — printing the first time you get throttled (HTTP
429) or the server starts erroring (HTTP 500), with a running request
counter. There is no rate limiting, proxying, slowloris mode or clever
evasion: it is a raw flood for measuring how a service you OWN degrades
under concurrency (capacity testing before an event, reproducing a
throttling bug, checking autoscaling). Attacking systems you do not have
permission to test is a crime; point it at your own lab.

*No corpus evidence — derived from upstream documentation.*

## Typical use cases

### Flood a URL with GET requests

Measure throttling and error behavior under load.

```
./doser -t 999 -g 'http://target.example.com'
```

Outcome: 999 threads issue GET requests to the URL continuously; a running
counter shows requests sent, and one-time notices print when throttling
kicks in (`You have been throttled after N requests`) or when the server
starts returning 500s. Ctrl+C stops the flood; the thread count (`-t`,
default 500) is the only tuning knob.

### Flood an endpoint with a POST payload

Load a form handler or login path instead of a static page.

```
./doser -t 500 -p 'http://target.example.com/login' -d 'user=test&pass=x'
```

Outcome: the same flood, but each request POSTs the urlencoded payload
(supplied via `-d`) with `application/x-www-form-urlencoded` content type —
useful for exercising the write path of your own service under load.
