---
name: "gitrob"
categories:
  - blackarch-scanner
version: "7.7be4c53"
homepage: "http://michenriksen.com/blog/gitrob-putting-the-open-source-in-osint/"
packaged-by: [blackarch, aur]
blackarch-builddate: "2019-05-09"
build-system: go
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "michenriksen/gitrob"
github-stars: 6198
github-pushed: "2022-09-20"
github-archived: true
github-issues: 74
github-match: "search"
gh-checked: "2026-11-07"
worth: 0.8
worth-basis: "historical (archived/gone upstream)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# gitrob

> Clones a GitHub organization's public repositories and flags potentially sensitive files — keys, credentials, configs — for review in a local web UI.

## What it's for

Gitrob (Michael Henriksen) is reconnaissance for GitHub organizations: given a user or org, it
clones their public repositories to a configurable commit depth, walks the commit history, and
matches file paths against signatures of potentially sensitive files — private keys, credential
files, database dumps, configuration secrets. Findings are presented in a local web interface for
browsing and triage. It needs a GitHub access token for the API and only reads public
repositories, so it is passive OSINT, not an attack tool. The upstream repository is archived and
unmaintained; it still runs, but treat it as legacy.

## Typical use cases

### Assess a GitHub organization

Sweep an organization's public code for leaked secrets.

```
gitrob -github-access-token <github-token> examplecorp
```

Outcome: clones the target's public repositories, analyzes their commit history, then serves the
findings browser on http://127.0.0.1:9393. The token can alternatively be exported as
`GITROB_ACCESS_TOKEN` to keep it out of shell history.

### Save an assessment for later

Persist the gathered state instead of losing it on exit.

```
gitrob -save ~/gitrob-session.json examplecorp
```

Outcome: writes all collected repository and finding data to the named file as a JSON document —
shareable with other analysts or parseable for integrations.

### Reload a saved session

Re-open previous results without touching GitHub again.

```
gitrob -load ~/gitrob-session.json
```

Outcome: starts the web interface and serves the stored assessment; no cloning or API traffic
happens, and `-commit-depth`, `-threads` and `-port` tune a fresh run's cost.

*No corpus evidence — derived from upstream documentation (michenriksen/gitrob README; license
corrected to MIT from the repository's LICENSE.txt).*
