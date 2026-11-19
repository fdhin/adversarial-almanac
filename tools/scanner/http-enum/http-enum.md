---
name: "http-enum"
categories:
  - blackarch-scanner
version: "0.4"
homepage: "https://www.thexero.co.uk/tools/http-enum/"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-25"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "nullsecuritynet/tools"
github-stars: 70
github-pushed: "2026-10-13"
github-archived: false
github-issues: 2
github-match: "exact"
gh-checked: "2026-11-09"
worth: 6.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 36
gh-activity-checked: "2026-11-09"
---

# http-enum

> TheXero's nullsecurity HTTP enumeration script: one pass over a web server probing which HTTP methods it accepts — standard, IIS/WebDAV and Apache-specific — plus per-server known paths.

*No corpus evidence — derived from upstream documentation (http-enum 0.4 release tarball, flags read from the packaged script's argparse).*

## What it's for

http-enum is a single-target Python 2 enumerator for web servers. It first fingerprints the server banner (Apache, Microsoft-IIS or unknown), then fires a battery of crafted method requests — `OPTIONS`, `TRACE`, `PUT`, `DELETE`, `CONNECT`, `PATCH`, plus deliberately malformed ones — and records which methods the server answers. Depending on the detected server it follows up with vendor-specific WebDAV-style verbs (`PROPFIND`, `SEARCH`, `LOCK`, `COPY`, `MKCOL`, ... for IIS; `BASELINE-CONTROL`, `REPORT`, `VERSION-CONTROL`, ... for Apache) and probes known default locations. The point is to surface dangerous capabilities such as `PUT`/`DELETE` (file upload/removal) or `TRACE` (cross-site tracing) in one quick pass. It enumerates only — it does not attempt to exploit what it finds, and it takes a single target per run.

## Typical use cases

### Enumerate enabled HTTP methods on a host

```
http-enum -t target.example.com
```

Outcome: Prints the server fingerprint, then the request/response result for each probed method, ending with a per-server list of additional vendor methods and default paths checked — any `PUT`, `DELETE` or `TRACE` showing as accepted is the follow-up lead.

### Probe an HTTPS service on a non-standard port

```
http-enum -t target.example.com -p 8443 -s
```

Outcome: Same method battery, but wrapped in SSL (`-s`) against port 8443 instead of port 80 — useful for admin panels and HTTPS-only vhosts.

### Enumerate behind a session cookie, pacing requests

```
http-enum -t target.example.com -c 'Cookie: session=abc123' -l 2
```

Outcome: Sends each probe with the supplied cookie (`-c`) so authenticated application surfaces are enumerated, and sleeps 2 seconds between requests (`-l`) to stay gentle on the target. Add `-v` for per-request verbosity and `-d` when the hostname resolves to multiple IPs.
