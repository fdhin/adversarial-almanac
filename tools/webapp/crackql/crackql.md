---
name: "crackql"
categories:
  - blackarch-webapp
version: "1.0.r53.gac26a44"
homepage: "https://github.com/nicholasaleks/CrackQL"
packaged-by: [blackarch, nixpkgs]
blackarch-builddate: "2024-12-25"
build-system: scripts
license: BSD
meta-checked: "2026-11-09"
github-repo: "nicholasaleks/CrackQL"
github-stars: 353
github-pushed: "2024-08-03"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.4
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# crackql

> GraphQL brute-forcer and fuzzer that hides thousands of guesses inside batched alias queries.

## What it's for

CrackQL exploits GraphQL APIs with weak rate-limiting or cost analysis: you give it a query/mutation template containing variable markers (e.g. `{{username|str}}`, `{{otp|int}}`) and a CSV whose header names match those variables, and it programmatically rewrites the template into documents containing many aliased copies of the operation — so a whole dictionary of guesses travels in a single HTTP request. Besides password spraying, the same mechanism drives OTP bypass (every token in one shot), account enumeration, IDOR iteration and general payload fuzzing. It is only as good as its input pairing: valid hits must be distinguishable in the response/errors for the results to be meaningful.

## Typical use cases

### Password spray a GraphQL login mutation

Batch brute-force credentials against an authentication operation.

```
crackql -t http://target.example.com/graphql -q login.graphql -i credentials.csv
```

Outcome: with `login.graphql` containing `login(username: {{username|str}}, password: {{password|str}})` and a CSV headed `username,password`, sends the guesses as batched alias mutations (100 per request by default) and stores per-guess results and errors under `results/target.example.com_<id>/`; successful logins stand out by their response shape (e.g. a returned `accessToken`).

### Evade rate limits and cost analysis

Tune batch size and pacing when the API throttles.

```
crackql -t http://target.example.com/graphql -q login.graphql -i credentials.csv -b 25 -D 3
```

Outcome: shrinks each GraphQL document to 25 aliased operations and waits 3 seconds between requests — trading speed for survivability against per-request cost limits and rate-based blocking.

### Brute-force an OTP or enumerate accounts

Reuse the engine with a different template and CSV.

```
crackql -t http://target.example.com/graphql -q otp-bypass.graphql -i otp-codes.csv -b 500
```

Outcome: submits an entire OTP code list (CSV column headed to match the template's `{{otp|int}}` marker) in large batches, so all candidate tokens are tried within the verification window; the same pattern with an `email`-headed CSV enumerates which addresses an operation accepts.

No corpus evidence — derived from upstream documentation.
