---
name: "ssrf-sheriff"
categories:
  - blackarch-webapp
version: "2.f95d691"
homepage: "https://github.com/teknogeek/ssrf-sheriff"
packaged-by: [blackarch]
blackarch-builddate: "2021-04-27"
build-system: go
license: MIT
meta-checked: "2026-11-09"
github-repo: "teknogeek/ssrf-sheriff"
github-stars: 342
github-pushed: "2024-10-31"
github-archived: false
github-issues: 1
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# ssrf-sheriff

> A self-hosted canary server that proves blind SSRF by echoing your secret token back.

## What it's for

SSRF Sheriff (written in Go, created for the Uber H1-4420 live-hacking event) is a small
server you deploy on infrastructure you control to detect and demonstrate blind Server-Side
Request Forgery. It answers every HTTP method on any path with a configurable secret token —
in the `X-Secret-Token` response header and, for most content types, in the response body —
across formats: plain text, JSON, XML, HTML, CSV, PNG, JPEG, plus GIF/MP3/MP4 without the
token in the body. If a target you cannot observe fetches a Sheriff URL and the token shows
up (in the target's behavior, a log, or a callback), you have proven SSRF. It is a detection
canary, not an SSRF exploiter: it does not scan internal networks or chained attacks.

## Typical use cases

### Start the canary server with your own token

Configure the listen address and secret token, then launch.

```
cd /usr/share/ssrf-sheriff && ssrf-sheriff
```

Outcome: the server starts listening on the address from `config/base.yaml` (default `:8000`) and answers every request with the `ssrf_token` value set in that file. Edit `config/base.yaml` before launching to set your own unguessable token.

### Verify the canary responds with your token

Confirm the deployment works before pointing a target at it.

```
curl -sSD- http://127.0.0.1:8000/foobar
```

Outcome: an HTTP 200 whose `X-Secret-Token` header and body contain your secret token; `.xml`, `.json`, `.html`, `.csv`, `.png` and other extensions return the token wrapped in the matching content type.

### Use the canary URL as an SSRF payload

Feed a Sheriff URL to a suspected-blind-SSRF parameter.

```
curl "http://target.example.com/fetch?url=http://198.51.100.10:8000/foobar.xml"
```

Outcome: if the vulnerable server performs the request, your token appears wherever the fetched content lands (rendered page, screenshot, parser error, or log) — direct evidence of the SSRF. Use a distinct random path or token per test to correlate which parameter triggered the fetch.

No corpus evidence — derived from upstream documentation.
