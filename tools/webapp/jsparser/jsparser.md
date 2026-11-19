---
name: "jsparser"
categories:
  - blackarch-webapp
version: "31.ccd3ab6"
homepage: "https://github.com/nahamsec/JSParser"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-25"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "nahamsec/JSParser"
github-stars: 852
github-pushed: "2023-11-13"
github-archived: false
github-issues: 41
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.5
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# jsparser

> Tornado web UI that beautifies a JavaScript file and extracts every relative URL from it to surface hidden AJAX endpoints.

## What it's for

jsparser is a bug-bounty recon helper: point it at a JavaScript file and it downloads, beautifies (JSBeautifier) and parses the source, then lists the relative URLs embedded in it — the `/api/...` paths, partial endpoints and AJAX request targets that never appear in the page HTML but are live on the server. It runs as a local Tornado web application on port 8008 rather than a batch CLI, so the workflow is submit-a-URL, read-the-list, then probe each found path yourself. Discovery only: it does not crawl, execute the script, or test whether the endpoints respond. The code is Python 2 era and effectively unmaintained, so it is best treated as a quick single-file triage aid rather than pipeline infrastructure.

## Typical use cases

### Start the parser and submit a JavaScript file

Launch the web UI and hand it a target script.

```
jsparser
# then browse to http://localhost:8008 and submit http://target.example.com/static/app.js
```

Outcome: a Tornado server starts listening on port 8008; submitting a JS file URL makes the tool fetch, beautify and parse the source, then display the relative URLs discovered in it. That list is the deliverable — each entry is a candidate hidden endpoint to request manually with curl or a browser. The tool fetches the script itself, so it must be able to reach the target URL from where it runs, and it relies on a Python 2 runtime under the hood.

No corpus evidence — derived from upstream documentation (nahamsec/JSParser README; packaged launcher runs `handler.py` under Python 2 with Tornado, JSBeautifier and safeurl).
