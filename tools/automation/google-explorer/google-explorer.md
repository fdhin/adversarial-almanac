---
name: "google-explorer"
categories:
  - blackarch-automation
version: "140.0b21b57"
homepage: "https://github.com/anarcoder/google_explorer"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-12"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "anarcoder/google_explorer"
github-stars: 205
github-pushed: "2018-12-01"
github-archived: false
github-issues: 2
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# google-explorer

> Google dork robot: collects search results with a real browser, then runs an exploit filter over every hit.

## What it's for

google_explorer (anarcoder) is a two-phase mass-exploitation robot. Phase one drives a real browser through Selenium (Chrome/Chromium or Firefox) to run a Google dork with optional language, location, last-update, domain, and proxy filters, and stores the result URLs. Phase two replays a chosen exploit plugin — bundled filters cover things like Joomla CVE-2015-8562, WordPress CVE-2015-1579, Struts2 CVE-2017-5638, Drupalgeddon2, JBoss finder, verbose SQLi, and CORS misconfig checks — against the collected URLs and reports which ones are actually vulnerable. The dork is the targeting mechanism: it only finds what the search indexed. Mass-scanning arbitrary internet hosts is only legal against assets you are authorized to test; treat this as a lab or authorized-engagement tool.

## Typical use cases

### Collect targets with a dork

```
google-explorer --dork="site:example.com inurl:index.php" --browser=chrome
```

Outcome: a Chrome window is driven through the Google search, results are parsed and stored, and the tool reports how many candidate URLs were kept for the exploitation phase.

### Narrow the search with filters

```
google-explorer --dork="inurl:admin.php" --browser=chrome --location="Japan" --last_update="past month" --google_domain="google.co.uk" --proxy=198.51.100.10:8080
```

Outcome: the same collection run restricted to pages in the chosen language/location and update window, served by an alternate Google domain, with all traffic routed through the proxy.

### Run the exploit filter over collected results

```
google-explorer --plugin='joomla_cve_2015_8562'
```

Outcome: every stored URL is tested with the selected plugin's request/response checks and each host is reported as vulnerable or not; `--plugin='verbose_sqli'` or `'cors_misc'` are examples of non-CVE-specific filters.

*No corpus evidence — derived from upstream documentation.*
