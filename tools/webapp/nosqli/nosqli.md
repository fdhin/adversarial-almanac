---
name: "nosqli"
categories:
  - blackarch-webapp
version: "37.6fce3eb"
homepage: "https://github.com/Charlie-belmer/nosqli"
packaged-by: [blackarch, nixpkgs]
blackarch-builddate: "2021-10-31"
build-system: go
license: GPL3
meta-checked: "2026-11-09"
github-repo: "Charlie-belmer/nosqli"
github-stars: 415
github-pushed: "2021-10-31"
github-archived: false
github-issues: 11
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.9
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# nosqli

> Fast command-line NoSQL injection scanner focused on MongoDB-backed web endpoints.

## What it's for

`nosqli` finds NoSQL injection vectors in web applications, with tests aimed at MongoDB in particular. It runs three classes of checks — error-based (injecting characters and payloads, then searching responses for known Mongo errors), boolean-blind (true/false payloads with response comparison), and timing-based injection — and reports the vulnerable parameter and the payload that worked. It is detection-oriented: it does not extract or dump data from the datastore. Written in Go as a single binary with a subcommand CLI.

## Typical use cases

### Scan a GET endpoint for injection

Test a URL whose parameters are all present in the query string.

```
nosqli scan -t "http://target.example.com/user/lookup?username=test"
```

Outcome: runs the error-based and boolean-based scans and prints any finding, including the vulnerable parameter and the injection string (for example `username='`); a clean endpoint reports no injection found.

### Scan a POST endpoint

```
nosqli scan -t "http://target.example.com/login" -d "username=user&password=pass"
```

Outcome: sends the POST body with injected values substituted for each parameter and applies the error, boolean, and timing checks against the responses.

### Replay a saved request from Burp or ZAP

```
nosqli scan -r request.txt
```

Outcome: parses the raw HTTP request file, reuses its method, headers, and body, and scans the parameters it contains without transcribing them into flags by hand.

### Scan through an intercepting proxy

```
nosqli scan -t "http://target.example.com/user/lookup?username=test" -p http://127.0.0.1:8080
```

Outcome: all scan traffic is routed through the given proxy URL, so each injected request can be inspected in the proxy's history.

No corpus evidence — derived from upstream documentation.
