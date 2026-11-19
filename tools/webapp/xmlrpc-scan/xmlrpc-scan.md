---
name: "xmlrpc-scan"
categories:
  - webapp
homepage: "https://github.com/devpwn/xmlrpc-scan"
origin: "howtohunt"
recipe: "upstream"
packaged-by: []
build-system: go
license: custom:unknown
meta-checked: "2026-11-13"
github-repo: "devpwn/xmlrpc-scan"
github-match: "exact"
github-stars: 64
github-pushed: "2023-11-09"
github-issues: 0
gh-checked: "2026-11-13"
worth: 4.1
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-13"
---

# xmlrpc-scan

> WordPress xmlrpc.php probe in Go: checks whether the XML-RPC interface is open and tests every SSRF-capable method with unique out-of-band URLs per attempt.

## What it's for

xmlrpc-scan (devpwn) is a Go tool for testing WordPress XML-RPC exposure.
For each WordPress URL it verifies that the `xmlrpc.php` interface is open,
then tests all possible SSRF-capable XML-RPC methods (the pingback and
proxy-style system methods that let the server make requests on your
behalf), generating a UNIQUE callback URL per attempt so out-of-band hits
(Burp Collaborator-class listeners) can be attributed to the exact method
that triggered them. Reach for it when auditing WordPress estates for the
pingback-SSRF/triage class of issues; it detects the exposure but does not
exploit it further — credential bruteforce via `system.multicall` is the
domain of [[xmlrpc-bruteforcer]] instead. The repo ships the scanner under
the binary name `xmlrpcscan` (one word).

*No corpus evidence — derived from upstream documentation.*

## Typical use cases

### Probe a single WordPress site for SSRF-capable xmlrpc methods

Point it at one URL with your out-of-band listener.

```
xmlrpcscan -target https://target.example.com -server http://bcollab.example.com
```

Outcome: checks whether `xmlrpc.php` is reachable and open, then exercises
each SSRF-capable method with its own uniquely-generated callback URL
derived from `-server`; any hit arriving at your listener identifies both
the vulnerable method and the target host.

### Sweep a list of WordPress URLs

Pipe an asset list through the same checks.

```
cat urls.txt | xmlrpcscan -server http://bcollab.example.com
```

Outcome: reads WordPress URLs from stdin line by line and applies the same
open-interface + SSRF-method battery to each — the batch form for estate
audits, with the same unique-URL-per-attempt attribution on the listener
side.
