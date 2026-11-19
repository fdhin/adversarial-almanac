---
name: "sj"
categories:
  - blackarch-webapp
version: "2.8.3.r1.g9111e90"
homepage: "https://github.com/BishopFox/sj"
packaged-by: [blackarch]
blackarch-builddate: "2026-11-01"
build-system: go
license: MIT
meta-checked: "2026-11-09"
github-repo: "BishopFox/sj"
github-stars: 881
github-pushed: "2026-10-30"
github-archived: false
github-issues: 3
github-match: "exact"
gh-checked: "2026-11-07"
worth: 7.3
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 75
gh-activity-checked: "2026-11-09"
---

# sj

> Swagger Jacker — audit an exposed Swagger/OpenAPI definition by probing every documented endpoint for weak authentication.

## What it's for

sj (Swagger Jacker, BishopFox) parses an exposed Swagger/OpenAPI definition file — local path or
URL — and works through the API it describes, checking each path/parameter/method combination
for endpoints that answer without proper authentication. It can blast every endpoint and analyze
status codes, generate ready-to-edit curl/sqlmap commands for manual testing, list raw routes,
bruteforce the location of the definition file itself, and convert v2 definitions to v3. It is a
read-and-probe auditor of documented surface: it does not fuzz parameters beyond the definition
or hunt logic bugs.

## Typical use cases

### Automatically probe all documented endpoints

```
sj automate -u https://example.com/v2/swagger.json -qi
```

Outcome: every operation in the definition receives crafted requests and each response's status
code is reported per method+route (accessible vs. error vs. no-answer), surfacing endpoints that
respond without valid credentials. Add `-v` to include response bodies in the output.

### Generate manual test commands

```
sj prepare -u https://example.com/v2/swagger.json -qi
```

Outcome: a curl command per operation is printed, pre-filled with the definition's parameters,
content types and example bodies — ready to be edited, chained, or piped into sqlmap for deeper
testing of a specific endpoint.

### List the raw attack surface

```
sj endpoints -u https://example.com/v2/swagger.json -qi
```

Outcome: every route from the definition prints one per line (placeholders like `{petId}` left
intact), a clean inventory for scoping, deduplication, or handoff to other tooling.

### Find a hidden definition file

```
sj brute -u https://example.com -qi -e
```

Outcome: thousands of common definition-file paths are requested against the target; on a hit,
the located definition file URL is reported and can then be fed to `automate` or `prepare`.

### Convert a v2 definition to v3

```
sj convert -u https://example.com/v2/swagger.json -qi -o openapi.json
```

Outcome: the v2 definition is converted and written to `openapi.json` in the current directory,
so v3-only downstream tooling can consume it.

No corpus evidence — derived from upstream documentation (README).
