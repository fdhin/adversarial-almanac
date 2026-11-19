---
name: "userrecon-py"
categories:
  - blackarch-social
version: "15.eebd422"
homepage: "https://github.com/lucmski/userrecon-py"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-14"
build-system: scripts
license: MIT
meta-checked: "2026-11-09"
github-repo: "lucmski/userrecon-py"
github-stars: 6
github-pushed: "2019-07-20"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 1.7
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# userrecon-py

> Lightweight async check of one username against 187 bundled social-site definitions, with JSON export.

## What it's for

userrecon-py (original by decouxviii, whose repository was deleted; packaged from lucmski's preserved fork of it) is a minimal username-footprint tool: given one handle it concurrently requests each of 187 site-profile URLs from a bundled `web_accounts_list.json` — a 2019-era snapshot derived from WhatsMyName and thelinuxchoice's userrecon — and reports which sites show an existing account. Python 3 with aiohttp, no API keys, no metadata enrichment: it answers strictly "does this username exist on site X". Expect the 2019 site list to be stale (dead sites, changed URL schemes); for current coverage use a checker driven by the live WhatsMyName dataset or a maintained scanner. It does not do email checks, breach lookups or password testing.

## Typical use cases

### Check a username across the bundled site list

```
userrecon-py --target user
```

Outcome: prints a live per-site progress list and a summary of every site where the username resolves to an existing profile.

### Save the findings as JSON

```
userrecon-py --target user -o user-sites.json
```

Outcome: same scan, with every result (site and verdict) additionally written to `user-sites.json` for later processing in a pipeline.

### Confirm the tool version in scripts

```
userrecon-py --version
```

Outcome: prints the package version string — useful because multiple forks of this tool circulate under the same name.

*No corpus evidence — derived from upstream documentation.*
