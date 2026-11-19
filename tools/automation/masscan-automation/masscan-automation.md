---
name: "masscan-automation"
categories:
  - blackarch-automation
version: "26.a170abc"
homepage: "https://github.com/trevordavenport/MasscanAutomation"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-25"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "trevordavenport/MasscanAutomation"
github-stars: 43
github-pushed: "2017-07-12"
github-archived: false
github-issues: 2
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# masscan-automation

> Single-script pipeline: masscan a range, then enrich and upload results via the Shodan API.

## What it's for

masscan-automation is a short Python glue script (Trevor Davenport, 2015) that chains masscan with Shodan: it launches a masscan scan against a target range, parses the XML results, maps the open ports to services using Shodan's port-to-service dictionary, and pushes the results to `scanhub.shodan.io` for security/vulnerability-management analysis. It automates scan → enrich → report; it does not exploit anything and has no other modes. It is Python 2 era code whose own README lists input validation and API-key testing as unfinished, so treat it as a reference implementation and read it before running. Requires masscan, curl and a Shodan API key.

## Typical use cases

### Run one enriched scan

Point it at a range with your Shodan key.

```
masscan-automation -i 198.51.100.0/24 -k YOUR_SHODAN_API_KEY
```

Outcome: masscan scans the range, the XML output is parsed and uploaded to your scanhub.shodan.io repository, and a per-service breakdown of the findings is printed (upstream, run the script as `python masscan_automation.py -i ... -k ...`). Both options are required; without them the script prints a usage error.

*No corpus evidence — derived from upstream documentation.*
