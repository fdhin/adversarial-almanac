---
name: "cent"
categories:
  - blackarch-webapp
version: "v2.2.0.r0.gfcf1c1c"
homepage: "https://github.com/xm1k3/cent"
packaged-by: [blackarch, nixpkgs]
blackarch-builddate: "2026-06-13"
build-system: go
license: Apache
meta-checked: "2026-11-09"
github-repo: "xm1k3/cent"
github-stars: 1052
github-pushed: "2026-06-06"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 6.7
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 9
gh-activity-checked: "2026-11-09"
---

# cent

> Aggregator that keeps the community's Nuclei templates in one maintained folder.

## What it's for

Cent (Community Edition Nuclei Templates) manages the flood of third-party Nuclei template collections: it clones every community template repository listed in its configuration into a single local folder, optionally organised by category, and then keeps that folder healthy — checking that source repositories still exist, pulling updates, validating that templates still parse (deleting invalid ones so Nuclei never trips over them), and printing summaries of what you have. It is template lifecycle management for Nuclei users, not a scanner itself; run Nuclei against the folder cent produces. Written in Go.

## Typical use cases

### Initialise the configuration

Create cent's config before the first harvest.

```
cent init
```

Outcome: writes the configuration file (default `.config/cent/.cent.yaml`) listing the community template repositories to track; edit it to add or remove template sources before harvesting.

### Harvest all community templates into one folder

Clone and organise every configured template repository.

```
cent -p cent-nuclei-templates
```

Outcome: clones each configured repository into `cent-nuclei-templates`, printing `[CLONED] <repo>` lines as it goes; with `-k` templates are kept in per-category folders, `-r` groups them by source repository, `-t N` sets the parallel clone threads and `-T` the per-clone timeout.

### Verify the template sources are still alive

Check that the tracked repositories have not vanished.

```
cent check
```

Outcome: walks the configured template repositories and reports which are still available — dead sources get pruned so later updates do not stall on them. Related maintenance commands: `update` pulls new template commits, `validate` drops templates that fail Nuclei's parser, and `summary` prints a detailed inventory of the template collection.

No corpus evidence — derived from upstream documentation.
