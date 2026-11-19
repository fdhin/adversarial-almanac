---
name: "jaidam"
categories:
  - blackarch-webapp
version: "18.15e0fec"
homepage: "https://github.com/stasinopoulos/jaidam"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-25"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "stasinopoulos/jaidam"
github-stars: 82
github-pushed: "2018-03-08"
github-archived: false
github-issues: 2
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.4
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# jaidam

> Batch CMS triage: identifies WordPress and Joomla across a domain list, then drives WPScan or JoomScan over the results.

## What it's for

Jaidam (2013-era, Python 2) automates the first pass of a wide web assessment: it takes a list of domain names, multithreadedly fingerprints which sites run WordPress and which run Joomla, and can then automatically run WPScan against the WordPress sites and JoomScan against the Joomla sites. Results are stored in a local SQLite database (`jaidamSQLite.db`, in the `wp_vulne_table` / `jm_vulne_table` tables). It only reports possible vulnerabilities — it never attempts exploitation. It is a legacy interactive tool: it has no command-line flags beyond launching it, requires WPScan and JoomScan to be installed alongside it, and its last upstream activity was years ago.

No corpus evidence — derived from upstream documentation.

## Typical use cases

### Run the interactive scan console

Launch the menu-driven toolkit against a domain inventory.

```
jaidam
```

Outcome: the interactive menu opens. `L` builds a target list (or `C` loads a custom text file of domains), `S` scans a single site, then `W` runs WPScan over every identified WordPress site and `J` runs JoomScan over the Joomla ones; `D`/`G` generate target lists with the built-in helpers, `M` runs extra modules, `U` updates the tool, `Q` quits. CMS identification and scan findings accumulate in `jaidamSQLite.db` for later review.
