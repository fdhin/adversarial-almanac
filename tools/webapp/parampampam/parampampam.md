---
name: "parampampam"
categories:
  - blackarch-webapp
version: "45.9171018"
homepage: "https://github.com/Bo0oM/ParamPamPam"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-13"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "Bo0oM/ParamPamPam"
github-stars: 274
github-pushed: "2022-06-27"
github-archived: false
github-issues: 1
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.7
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# parampampam

> Python 3 re-take on brute-discovering GET and POST parameters, with fine-grained request control.

## What it's for

ParamPamPam tackles the same job as its predecessor [[parameth]] — finding which parameter names a web endpoint accepts — by trying a wordlist of candidate names and watching for response changes. It is a Python 3 tool with per-request control: request method, cookies, User-Agent, Content-Type, Authorization header, custom parameter files, and timeouts. Compared to parameth it runs on current Python but stays wordlist-driven.

## Typical use cases

### Discover parameters on an endpoint

```
parampampam -u https://example.com/login
```

Outcome: tries the default parameter wordlist (`params.txt`) against the URL with GET requests and reports the names that change the response.

### Fuzz POST parameters with a custom list

```
parampampam -u https://example.com/api -m POST -f params.txt
```

Outcome: switches discovery to POST requests and uses the given file's parameter names — for example a list tailored to the application's framework.

### Test with a session and JSON content type

```
parampampam -u https://example.com/api -c "SESSIONID=abc123" -ct application/json
```

Outcome: the cookie authenticates every request and the body is sent with the chosen Content-Type, allowing parameter discovery against authenticated JSON endpoints.

No corpus evidence — derived from upstream documentation.
