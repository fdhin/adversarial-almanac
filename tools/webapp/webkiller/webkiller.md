---
name: "webkiller"
categories:
  - blackarch-webapp
version: "42.d680598"
homepage: "https://github.com/ultrasecurity/webkiller"
packaged-by: [blackarch]
blackarch-builddate: "2023-05-05"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "ultrasecurity/webkiller"
github-stars: 749
github-pushed: "2023-04-27"
github-archived: false
github-issues: 22
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.5
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# webkiller

> Menu-driven Python 3 recon toolkit: information gathering and CMS detection in one interactive console.

## What it's for

WebKiller V2 (Ultra Security Team) is an interactive, menu-based information-gathering tool: launch it and choose between its Information Gathering module and its CMS Detection module, answering prompts for the target site. It bundles the usual recon lookups (domain/IP intelligence, DNS and whois-style records, related services) behind that menu, using Python 3 with `requests`. It is prompt-driven by design — no non-interactive command-line flags are documented upstream — so it suits manual recon sessions rather than scripted pipelines.

## Typical use cases

### Start the recon console

Open the toolkit against a target.

```
webkiller
```

Outcome: prints the main menu — `[1] Information Gathering`, `[2] CMS Detection`, `[3] Developer`, `[4] Exit` — after selecting a module, follow the prompts to enter the target domain and read the collected results in the console.

*No corpus evidence — derived from upstream documentation (ultrasecurity/webkiller README).*
