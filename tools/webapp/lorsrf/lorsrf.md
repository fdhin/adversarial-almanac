---
name: "lorsrf"
categories:
  - blackarch-webapp
version: "v2.1.r3.g5c69453"
homepage: "https://github.com/knassar702/lorsrf"
packaged-by: [blackarch]
blackarch-builddate: "2024-10-06"
build-system: go
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "knassar702/lorsrf"
github-stars: 294
github-pushed: "2024-09-22"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# lorsrf

> Brute-forces parameter names to find SSRF and out-of-band resource load sinks.

## What it's for

Lorsrf (by Khaled Nassar, written in Rust) finds the parameters of a web application that the server will fetch or resolve. You give it a list of target URLs, a parameter-name wordlist, and an OAST host (Burp Collaborator or interactsh.com); it appends each guessed parameter with your callback host as the value, and any backend-side fetch shows up as an HTTP/DNS hit on your callback server. The callback host can embed variables — `%PARAM%`, `%HOST%`, `%PATH%`, `%QUERY%`, `%METHOD%` — so each hit identifies which parameter and endpoint triggered it. Requests are GET by default; `--form` and `--json` switch to POST bodies. It detects blind SSRF only; it does not attempt read-based SSRF or exploit the sinks it finds.

## Typical use cases

### Brute-force parameter names against a URL list

```
lorsrf --urls urls.txt --call http://callback.example.com --wordlist params.txt
```

Outcome: fires concurrent requests adding every wordlist entry as a parameter pointing at the callback host; watch the OAST service's poller — each incoming request names the vulnerable URL and the parameter that caused the fetch.

### Make callbacks self-describing

Embedding `%PARAM%` in the callback host labels every hit, removing the guesswork about which parameter fired.

```
lorsrf --urls urls.txt --call "http://%PARAM%.callback.example.com" --wordlist params.txt
```

Outcome: a callback arriving as `remote.callback.example.com` immediately identifies `remote` as a parameter the backend resolves.

### Probe POST endpoints

```
lorsrf --urls urls.txt --call http://callback.example.com --wordlist params.txt --json
```

Outcome: guesses are sent as JSON body fields (`--form` does the same for url-encoded forms); `--post-only` restricts the run to POST requests entirely.

No corpus evidence — derived from upstream documentation.
