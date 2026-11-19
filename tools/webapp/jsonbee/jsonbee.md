---
name: "jsonbee"
categories:
  - blackarch-webapp
version: "30.c0c87fc"
homepage: "https://github.com/zigoo0/JSONBee"
packaged-by: [blackarch]
blackarch-builddate: "2023-10-29"
build-system: scripts
license: GPL3
meta-checked: "2026-11-09"
github-repo: "zigoo0/JSONBee"
github-stars: 764
github-pushed: "2024-05-06"
github-archived: false
github-issues: 7
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.5
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# jsonbee

> Dataset of ready-to-use JSONP payloads for bypassing a target's Content-Security-Policy, plus a local CSP lab.

## What it's for

JSONBee (by zigoo0, presented at HackIT 2018) is primarily a curated dataset: `jsonp.txt` holds ready-to-use JSONP endpoint/payload pairs keyed by domain (Google, Facebook, and many more), each a `<script src=...callback=alert(1337)>` line that executes JavaScript if the target site's CSP `script-src` whitelists that domain. The workflow is manual: read a target's CSP, find a whitelisted domain in the dataset, and use the matching payload to demonstrate script execution. The package also ships `csp_lab.php`, a deliberately CSP-protected reflecting page for practicing these bypasses; the packaged `jsonbee` command serves it with PHP's built-in web server. The announced automated endpoint-discovery tool was never finished — treat the dataset as the tool.

No corpus evidence — derived from upstream documentation (repository contents and packaged wrapper verified).

## Typical use cases

### Find a CSP bypass for a whitelisted domain

Look up payloads for a domain the target's policy trusts.

```
grep -i 'google' /usr/share/jsonbee/jsonp.txt
```

Outcome: prints the dataset's JSONP payload lines involving Google domains; if the target site's `script-src` allows any of them, the corresponding payload line can be pasted into an injection point to run JavaScript despite the CSP.

### Serve the local CSP practice lab

Bring up the reflecting page with its restrictive policy.

```
jsonbee
```

Outcome: starts PHP's built-in web server on port 8080 serving `csp_lab.php`; opening `http://127.0.0.1:8080/?name=test` reflects the `name` value into the page under a CSP-Report-Only header whose directives (including its `script-src` whitelist) can be edited in `/usr/share/jsonbee/csp_lab.php` to rehearse JSONP bypasses safely.
