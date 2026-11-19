---
name: "graphql-cop"
categories:
  - blackarch-scanner
version: "1.16.r0.g2b7e086"
homepage: "https://github.com/dolevf/graphql-cop"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-12"
build-system: scripts
license: MIT
meta-checked: "2026-11-09"
github-repo: "dolevf/graphql-cop"
github-stars: 699
github-pushed: "2025-11-20"
github-archived: false
github-issues: 4
github-match: "exact"
gh-checked: "2026-11-07"
worth: 5.4
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 1
gh-activity-checked: "2026-11-09"
---

# graphql-cop

> Lightweight GraphQL security tester: runs a battery of known GraphQL misconfiguration checks against an endpoint and hands you a cURL command to reproduce each finding.

## What it's for

graphql-cop (Dolev Farhi) runs common security tests against GraphQL APIs: introspection and
GraphiQL/playground exposure, field suggestions, alias/field/directive overloading (DoS), batch
queries, GET-based queries and mutations (CSRF), and tracing/debug-mode leaks. It is deliberately
small and CI-friendly — give it a URL and it prints severity-tagged findings, each with a
`curl_verify` command so a human can reproduce the request by hand. It audits configuration, not
business logic: it is not a fuzzer and does not attempt auth bypasses. If the URL carries no
GraphQL path it probes a list of common ones automatically.

## Typical use cases

### Audit an endpoint

Run the full test battery against a known GraphQL URL.

```
graphql-cop -t https://target.example.com/graphql
```

Outcome: prints findings line by line, each tagged with severity and impact class (`[HIGH]
Introspection Query Enabled (Information Leakage)`, `[LOW] ... (Denial of Service)`, etc.). When
no path is given, common GraphQL endpoints are tried in turn.

### Emit machine-readable output

Feed the results to CI instead of a terminal.

```
graphql-cop -t https://target.example.com/graphql -o json
```

Outcome: the same findings as JSON objects — severity, impact, description and the
`curl_verify` reproduction command — ready to be parsed by a pipeline gate.

### Scan behind a proxy with auth headers

Inspect every test request in an intercepting proxy.

```
graphql-cop -t https://target.example.com/graphql --proxy=http://127.0.0.1:8080 --header '{"Authorization": "Bearer <token>"}'
```

Outcome: all probes are routed through the named proxy (e.g. a local Burp Suite listener) with
the supplied header attached, so each generated request appears in the proxy history for review;
repeat `-H` for additional headers.

### Trim or inspect the test set

Exclude a noisy check or list what can run.

```
graphql-cop -l
graphql-cop -t https://target.example.com/graphql -e field_duplication
```

Outcome: `-l` prints the available test names; `-e` runs everything except the named test,
silencing a known-and-accepted finding.

*No corpus evidence — derived from upstream documentation (dolevf/graphql-cop README; BlackArch
wraps graphql-cop.py in a `graphql-cop` launcher).*
