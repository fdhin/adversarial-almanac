---
name: "yeti"
categories:
  - blackarch-defensive
version: "3545.c5fb74d4"
homepage: "https://github.com/yeti-platform/yeti"
packaged-by: [blackarch]
blackarch-builddate: "2026-11-01"
build-system: scripts
license: Apache
meta-checked: "2026-11-09"
github-repo: "yeti-platform/yeti"
github-stars: 2041
github-pushed: "2026-10-28"
github-archived: false
github-issues: 70
github-match: "exact"
gh-checked: "2026-11-07"
worth: 8.1
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 144
gh-activity-checked: "2026-11-09"
---

# yeti

> Self-hosted threat-intelligence platform that links observables, indicators, and TTPs into one queryable knowledge base for CTI and DFIR work.

## What it's for

Yeti is an open-source threat-intelligence platform (FastAPI backend, ArangoDB store, Celery workers, deployed as a set of Docker containers) built to answer "where have I seen this artifact before?" It stores observables, indicators, TTPs, campaigns, and other entities in a single repository, enriches them through pluggable feeds and analytics, and exposes everything through a web UI, a REST API with interactive docs at `/docs`, and export formats a SIEM or DFIR platform can ingest. Reach for it when pivoting from an observable to related indicators across incidents, or curating YARA/Sigma/DFIQ collections in one place. It organizes intelligence — it is not a sandbox and does not collect endpoint telemetry itself.

## Typical use cases

### Create the first admin user

```
docker compose run --rm api create-user user 'Password1' --admin
```

Outcome: creates an administrator account against the running Yeti stack that you use to log into the web UI (served on port 80 by the frontend container); drop `--admin` for a regular analyst account.

### Obtain an API token from a user API key

```
curl -s -X POST http://localhost:8000/api/v2/auth/api-token \
  -H 'x-yeti-apikey: API_KEY'
```

Outcome: returns a JSON `access_token` (a JWT) that you pass as `Authorization: Bearer ACCESS_TOKEN` on subsequent API calls — the three-step auth flow from the upstream docs, here against the api container's port 8000.

### Bulk-import observables from pasted text

```
curl -s -X POST http://localhost:8000/api/v2/observables/import/text \
  -H 'authorization: Bearer ACCESS_TOKEN' \
  -H 'content-type: application/json' \
  -d '{"text": "example.com\n198.51.100.10\nd41d8cd98f00b204e9800998ecf8427e", "tags": ["triage"]}'
```

Outcome: each line is parsed as one observable (IPs, domains, URLs, and hashes are recognized) and stored with the supplied tags, ready for search, enrichment, and pivoting in the web UI.

*No corpus evidence — derived from upstream documentation.*
