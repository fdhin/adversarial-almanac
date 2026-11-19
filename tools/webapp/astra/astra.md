---
name: "astra"
categories:
  - blackarch-webapp
version: "489.092804a"
homepage: "https://github.com/flipkart-incubator/astra"
packaged-by: [blackarch, aur]
blackarch-builddate: "2024-12-25"
build-system: scripts
license: Apache
meta-checked: "2026-11-09"
github-repo: "flipkart-incubator/astra"
github-stars: 2673
github-pushed: "2024-06-05"
github-archived: false
github-issues: 66
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.9
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# astra

> Automated Security Testing for REST APIs — Flipkart-incubated framework that scans a single endpoint or a whole Postman/Swagger collection for injection, broken auth, CORS, JWT, CSRF and SSRF findings, from a CLI or a web dashboard.

*No corpus evidence — derived from upstream documentation.*

## What it's for

REST API testing is repetitive: endpoints churn, auth tokens expire, and every engagement means re-running the same injection checks. Astra (from Flipkart's security team, presented in the BlackHat Arsenal 2018) automates that sweep: point it at one URL or import an API collection and it tests for SQL injection, XSS, information leakage, broken authentication and session management, CSRF (including blind CSRF), rate limiting, CORS misconfiguration, JWT weaknesses, CRLF, blind XXE, SSRF and template injection. It can auto-detect and drive login/logout APIs, which makes it usable in a CI/CD pipeline. Two interfaces share one engine: a CLI (`astra.py`) and a Flask web dashboard (`api.py`, port 8094) backed by MongoDB and Celery. The project is legacy-era (the packaged recipe installs Python 2 dependencies; upstream requirements say Python 3.7+ with Mongo/Celery/RabbitMQ for the dashboard) — plan for the CLI when trying it today, and expect unmaintained code.

## Typical use cases

### Scan a single API endpoint

```
python astra.py -u http://target.example.com/api/v1/user -method GET -headers '{"token":"123"}'
```

Outcome: runs the full module set against that one endpoint, honouring the custom header (a session token, API key, etc.); findings are reported to the terminal with the vulnerability class per parameter. `-method POST` plus `-b` (request body) covers endpoints that take input in the body.

### Scan an entire API collection

```
python astra.py -c Postman -n collection.json
```

Outcome: imports the Postman collection (`-c Swagger` for OpenAPI/Swagger files) and iterates every documented endpoint, so newly added or changed APIs are re-covered without enumerating them by hand — the mode intended for plugging into a CI pipeline.

### Scan through the target's login flow

```
python astra.py -u http://target.example.com/api/v1/user -l http://target.example.com/api/login -d 'username=user&password=Password1' -H '{"accesstoken":"..."}'
```

Outcome: Astra first authenticates through the login API (`-d` carries the credentials payload, `-H` any header the login endpoint needs), reuses the returned session for the scan, and exercises the logout path, so authenticated-only endpoints and session-handling flaws are actually reached.

### Drive scans from the web dashboard

```
cd API
python api.py
```

Outcome: starts the Flask service; browse to `http://127.0.0.1:8094` to configure scans, watch them progress and read per-scan reports — the interface that needs the MongoDB/Celery/RabbitMQ stack from the requirements. CLI mode needs none of that stack.
