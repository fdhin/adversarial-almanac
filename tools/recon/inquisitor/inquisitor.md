---
name: "inquisitor"
categories:
  - blackarch-recon
version: "28.12a9ec1"
homepage: "https://github.com/penafieljlm/inquisitor"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-25"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "penafieljlm/inquisitor"
github-stars: 183
github-pushed: "2017-07-29"
github-archived: false
github-issues: 16
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# inquisitor

> Organization-scoped OSINT workbench: classify owned assets, cascade transforms via Google and Shodan, then dump and visualize the graph.

## What it's for

Inquisitor (penafieljlm) re-implements the Maltego/recon-ng workflow on the command line for one target organization: you seed an intelligence-database file with assets known to belong to the target (hosts, netblocks, emails, registrants, LinkedIn accounts), and its transforms pull related assets from open sources such as Google Custom Search and Shodan, cascading the ownership label onto newly found items. Results can be dumped to JSON or rendered as a zoomable D3.js visualization. Upstream installs the CLI as `inq` (the BlackArch package renames the binary to `inquisitor`), and the author notes the project was never finished, so treat the transforms as best-effort Python 2 code.

## Typical use cases

### Seed the database with known assets

Tell the tool what definitely belongs to the target before scanning.

```
inquisitor classify intel.db -ah www.example.com -ae user@example.com
```

Outcome: creates or opens the intelligence database `intel.db` and marks the host and email address as accepted (owned); `-rh`/`-re` variants mark assets as rejected, and equivalent flags exist for blocks, registrants and LinkedIn accounts.

### Run the OSINT transforms

Expand owned assets into related ones.

```
inquisitor scan intel.db --shodan-api-key KEY
```

Outcome: applies every available transform to all owned assets in the database; transforms that need Google Custom Search run only when `--google-dev-key` and `--google-cse-id` are also supplied, and `--shodan-limit`/`--google-limit` cap query volume.

### Review progress and export

Check what has been collected and hand it to other tooling.

```
inquisitor status intel.db
inquisitor dump intel.db -j intel.json
```

Outcome: `status` prints a summary of the database contents; `dump` writes the full asset graph to `intel.json` (`-a` includes rejected assets).

### Visualize the asset relationships

Render the graph for human review.

```
inquisitor visualize intel.db
```

Outcome: produces a zoomable D3.js pack-layout visualization of the intelligence database for exploring how discovered assets cluster.

*No corpus evidence — derived from upstream documentation.*
