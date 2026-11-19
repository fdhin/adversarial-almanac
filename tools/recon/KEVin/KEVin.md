---
name: "KEVin"
categories:
  - recon
version: "git (2026-11-04)"
homepage: "https://github.com/synfinner/kevin"
origin: "kitploit"
recipe: "upstream"
packaged-by: [aur]
build-system: go
meta-checked: "2026-11-09"
github-repo: "synfinner/kevin"
github-stars: 28
github-pushed: "2026-11-09"
github-archived: false
github-match: "exact"
gh-checked: "2026-11-09"
github-commits-year: 128
gh-activity-checked: "2026-11-09"
worth: 7.0
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
license: see repository
github-issues: 1
---

# KEVin

> REST API over CISA's Known Exploited Vulnerabilities catalog, with merged NVD and MITRE data per CVE.

## What it's for

KEVin is a Flask web service (Python, backed by MongoDB) that turns the CISA Known Exploited Vulnerabilities (KEV) catalog into a queryable API. It serves per-CVE KEV entries, lists of recently added KEVs, full-text search, and an enriched per-CVE view that merges CISA, MITRE and NVD data — useful for feeding exploitability checks into reporting, triage, or automation instead of scraping the CISA page. A public instance runs at `https://kevin.gtfkd.com/`; the same endpoints work against a self-hosted instance (the Docker setup exposes the app on port 8444). It is a data API only — it does not scan, exploit, or validate anything.

## Typical use cases

### Check whether a CVE is known-exploited

Ask KEVin if a specific CVE appears in the CISA KEV catalog.

```
curl -s https://kevin.gtfkd.com/kev/CVE-2021-44228
```

Outcome: a JSON KEV entry (or a not-found response) containing the CISA metadata — ransomware usage, required action, due date, and notes — ready to fold into a vulnerability report.

### Pull recently added KEVs

Retrieve everything CISA added to the catalog in the last week.

```
curl -s https://kevin.gtfkd.com/kev/new/7
```

Outcome: a JSON list of KEV entries added in the last 7 days; change the number for a different window. Useful as a scheduled pull to keep an internal tracker current.

### Get the merged CISA + MITRE + NVD view of one CVE

Fetch enriched data for a single vulnerability in one call.

```
curl -s https://kevin.gtfkd.com/vuln/CVE-2021-44228
```

Outcome: one JSON document combining the KEV entry, NVD CVSS/CWE data, and MITRE references; `/vuln/CVE-2021-44228/nvd` returns only the NVD portion.

### Full-text search the KEV catalog

Find every exploited vulnerability matching a keyword.

```
curl -s 'https://kevin.gtfkd.com/kev?search=Exchange'
```

Outcome: all KEV entries whose text matches the search term, e.g. every exploited Microsoft Exchange issue, with the same per-entry structure as the single-CVE lookup.

### Query a self-hosted instance

Run the same queries against your own deployment.

```
curl -s http://127.0.0.1:8444/kev
```

Outcome: the full KEV list from the local instance (populated with `update.py`, which pulls the catalog from CISA into MongoDB) — no dependence on the public host's rate limits.

*No corpus evidence — derived from upstream documentation.*
