---
name: "ppmap"
categories:
  - blackarch-webapp
version: "v1.2.0.r15.g9426af6"
homepage: "https://github.com/kleiton0x00/ppmap"
packaged-by: [blackarch]
blackarch-builddate: "2022-06-26"
build-system: go
license: GPL3
meta-checked: "2026-11-09"
github-repo: "kleiton0x00/ppmap"
github-stars: 519
github-pushed: "2022-06-22"
github-archived: false
github-issues: 2
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.9
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# ppmap

> Scanner/exploiter that chains client-side prototype pollution into XSS by firing known script gadgets.

## What it's for

Turning a prototype-pollution primitive into a demonstrated XSS: it heuristically checks whether a page is pollutable via `location.hash`/`location.search`, fingerprints which known gadgets are present by probing for specific variables in the global context, and then assembles and displays the final XSS payload that exploits the pollution through those gadgets. Written in Go and drives a local Chromium, which must be installed (the repo's `setup.sh` handles that on supported distros). Scope limit stated by the author: it only exploits known, pre-fingerprinted gadgets — no code analysis and no custom-gadget discovery, and it does not cover server-side prototype pollution.

## Typical use cases

### Scan a site root

Check a whole origin in one shot.

```
echo 'https://target.example.com' | ppmap
```

Outcome: ppmap loads the site headlessly, tests hash/search-based pollution, fingerprints gadgets and — on success — prints a ready-to-use XSS payload URL; a clean run simply reports no pollution found.

### Test a specific endpoint with parameters

Focus on the parameter-bearing page where sinks usually live.

```
echo 'https://target.example.com/page/?page=home' | ppmap
```

Outcome: the same heuristic-gadget pipeline runs against that exact URL, catching pollution that only manifests with particular query strings.

### Mass-scan from a file

Sweep a whole engagement's URL inventory.

```
cat urls.txt | ppmap
```

Outcome: every URL in the file (one per line) is tested in turn, with any vulnerable target and its generated XSS payload reported inline — collect the output as the deliverable evidence for each finding.

*Provenance: no corpus evidence — derived from upstream documentation (README, verified 2026-11).*
