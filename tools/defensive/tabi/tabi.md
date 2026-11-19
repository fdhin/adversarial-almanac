---
name: "tabi"
categories:
  - blackarch-defensive
version: "13.068a406"
homepage: "https://github.com/ANSSI-FR/tabi"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-26"
build-system: scripts
license: MIT
meta-checked: "2026-11-09"
github-repo: "ANSSI-FR/tabi"
github-stars: 117
github-pushed: "2018-02-14"
github-archived: false
github-issues: 4
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.5
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# tabi

> Detects BGP prefix conflicts (potential hijacks) by replaying RIPE RIS MRT dumps and classifying them into hijacking events.

## What it's for

TaBi ("Track BGP Hijacks") is the French ANSSI framework for detecting BGP IP-prefix conflicts — an AS advertising a prefix, or a more specific one, delegated to another AS — and classifying them into BGP hijacking events. It processes BGP messages archived in MRT dump files (its companion parser is MaBo; CAIDA's bgpreader also works), follows the evolution of routes across the dumps, and writes per-collector JSON reports of routes, defaults and detected conflicts. The `tabi` command is the CLI entry point; the library can also be imported to build custom detection pipelines (e.g. hijack classification enriched with RPKI/IRR data). It analyzes routing archives — it does not capture BGP itself. Development stopped in the Python 2 era, so expect legacy runtime requirements.

*No corpus evidence — derived from upstream documentation.*

## Typical use cases

### Fetch a day of BGP data from a route collector

Grab the MRT inputs TaBi works on.

```
wget -c http://data.ris.ripe.net/rrc01/2016.01/bview.20160101.0000.gz
wget -c http://data.ris.ripe.net/rrc01/2016.01/updates.20160101.0000.gz
```

Outcome: two MRT archives in the working directory — a full BGP view (`bview.*`) and the day's routing updates (`updates.*`) from RIPE RIS collector `rrc01`, named to the RIS convention the tool expects by default.

### Scan MRT dumps for prefix hijacks

Run hijack detection across a collector's dumps.

```
tabi -j 8 rrc01 results/ bview.20160101.0000.gz updates.20160101.0000.gz
```

Outcome: after parallel processing with 8 worker jobs, `results/2016.01/` contains `all.hijacks.json.gz` (detected BGP prefix conflicts), `all.routes.json.gz` (all monitored routes) and `all.defaults.json.gz` (default routes seen). Decompress `all.hijacks.json.gz` first for hijack candidates.

### Limit monitoring to a watchlist of ASes

Focus the reports on the networks you actually care about.

```
tabi -a ases.txt rrc01 results/ updates.20160101.0000.gz
```

Outcome: with `-a` pointing at a file listing the ASes to monitor, output covers only those ASes' routes and the conflicts involving them, rather than every AS seen in the dumps (legacy per-AS output mode creates roughly two files per AS and exhausts file descriptors — the combined default avoids that).
