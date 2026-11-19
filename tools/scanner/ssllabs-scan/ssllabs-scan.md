---
name: "ssllabs-scan"
categories:
  - blackarch-scanner
version: "251.53cd76a"
homepage: "https://github.com/ssllabs/ssllabs-scan"
packaged-by: [blackarch, aur]
blackarch-builddate: "2023-10-29"
build-system: go
license: APACHE
meta-checked: "2026-11-09"
github-repo: "ssllabs/ssllabs-scan"
github-stars: 1768
github-pushed: "2024-08-05"
github-archived: false
github-issues: 294
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.8
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# ssllabs-scan

> Command-line client for the Qualys SSL Labs APIs — scriptable, JSON-producing TLS server grading for CI and bulk assessments.

## What it's for

ssllabs-scan (Qualys SSL Labs, Go) is a command-line client for the SSL Labs server-testing APIs, built for automated and bulk assessment of public HTTPS hosts. It submits hosts to the SSL Labs service and returns the full assessment as JSON (hostnames or a `--hostfile` list, one per line), or just the grade with `--grade`; `--usecache` accepts cached results instead of forcing fresh scans. Assessment runs on SSL Labs' servers, so targets must be internet-reachable, and API terms of use apply. Note on versions: the packaged binary is the classic (v3-API) client documented below; upstream master has since moved to a v4 API that requires one-time email registration and passes `--email` on every invocation, with the v3 client relocated to `deprecated-clients/` in the repository.

## Typical use cases

### Grade a single host

```
ssllabs-scan --grade www.example.com
```

Outcome: submits the host to SSL Labs, waits for the assessment to complete and prints `www.example.com: <grade>` — the quickest pass/fail signal for a certificate/TLS configuration.

### Bulk-assess a host list to JSON

```
ssllabs-scan --hostfile hosts.txt --usecache --quiet > results.json
```

Outcome: assesses every host in `hosts.txt` (one per line), accepting cached reports where available to stay inside rate limits, and writes the full JSON assessment for each host to `results.json` for programmatic grading drift checks.

### Tolerate certificate mismatches

```
ssllabs-scan --ignore-mismatch --json-flat www.example.com
```

Outcome: proceeds with the assessment even when the certificate does not match the requested hostname (default behavior stops), and emits flattened JSON — useful for monitoring hosts whose CDN serves a placeholder certificate until SNI is exercised.

*No corpus evidence — derived from upstream documentation (README and v3 client flag definitions).*
