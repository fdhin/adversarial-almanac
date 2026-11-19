---
name: "whatsmyname"
categories:
  - blackarch-social
version: "2922.038ec49"
homepage: "https://github.com/WebBreacher/WhatsMyName"
packaged-by: [blackarch]
blackarch-builddate: "2026-11-01"
build-system: scripts
license: MIT
meta-checked: "2026-11-09"
github-repo: "WebBreacher/WhatsMyName"
github-stars: 3038
github-pushed: "2026-11-02"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 8.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 203
gh-activity-checked: "2026-11-09"
---

# whatsmyname

> The reference OSINT dataset (wmn-data.json, ~690 sites) describing exactly how to detect whether a username exists on each site.

## What it's for

WhatsMyName (Micah "WebBreacher" Hoffman, 2015; data licensed CC BY-SA 4.0) is not a scanner you run but the community-maintained dataset the scanning ecosystem reads: a single `wmn-data.json` holding one detection entry per site — the profile URL template (`{account}` placeholder), the HTTP status and body string that prove an account exists (`e_code`/`e_string`) and the pair that proves it does not (`m_code`/`m_string`), plus categories, known-good test usernames and bot-protection notes. Sites qualify only if the profile URL contains the username and the page is publicly viewable. The project's own checker scripts were removed from user-facing duty in May 2023 — checking is left to consumers such as the whatsmyname.app web interface, Naminter and Blackbird — so treat this package as data plus maintainer utilities. Packaging caveat: the BlackArch `/usr/bin/whatsmyname` wrapper still invokes `whats_my_name.py`, which no longer exists upstream; work directly with the installed data at `/usr/share/whatsmyname/`.

## Typical use cases

### Survey the dataset's coverage

```
jq '.sites | length' /usr/share/whatsmyname/wmn-data.json
jq -r '.categories[]' /usr/share/whatsmyname/wmn-data.json
```

Outcome: prints the current number of site detections (~690) and the category list (social, gaming, tech, coding, ...), telling you at a glance whether the dataset covers an investigation's platforms.

### Extract check URLs for one category

```
jq -r '.sites[] | select(.cat=="social") | .uri_check' /usr/share/whatsmyname/wmn-data.json
```

Outcome: lists every social-category profile URL template (e.g. `https://example.com/User/{account}/`) — the raw material for a custom checker or a quick manual sweep.

### Manually test one detection

```
jq -r '.sites[] | select(.name=="GitHub") | .uri_check' /usr/share/whatsmyname/wmn-data.json | sed 's/{account}/user/' | xargs curl -s -o /dev/null -w '%{http_code}\n'
```

Outcome: substitutes a real handle into the site's URL template and fetches it; compare the returned status code and page body against that entry's `e_code`/`e_string` (found) and `m_code`/`m_string` (not found) to see the dataset's logic in action.

### Validate detections as a maintainer

```
python3 /usr/share/whatsmyname/scripts/checker/wmn_check.py --limit 10 --workers 10
```

Outcome: the standalone checker script re-tests site entries (known and random usernames; requests first, headless-Chromium fallback for JS-heavy pages, requires `requests` and `playwright`) and writes a dated Markdown report grouped by failure type — this is the workflow for fixing broken detections before submitting a PR; `--data` points it at an alternate JSON.

*No corpus evidence — derived from upstream documentation.*
