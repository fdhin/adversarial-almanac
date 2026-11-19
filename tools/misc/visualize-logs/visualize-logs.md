---
name: "visualize-logs"
categories:
  - blackarch-misc
version: "118.d2e370e"
homepage: "https://github.com/keithjjones/visualize_logs"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-14"
build-system: python
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "keithjjones/visualize_logs"
github-stars: 145
github-pushed: "2022-12-27"
github-archived: false
github-issues: 3
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.0
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# visualize-logs

> Turns Cuckoo sandbox JSON reports and Process Monitor CSV logs into interactive process-activity graphs.

## What it's for

visualize_logs is a Python library plus two command-line tools, plotcuckoojson
and plotprocmoncsv, that convert behavioral logs — Cuckoo sandbox JSON reports
and Sysinternals ProcMon CSV exports — into interactive HTML graphs. Processes
appear as nodes with their file, registry and network activity attached as
edges, so you can visually reconstruct what a sample did: which process wrote
which file, which touched the registry, which opened network connections. The
output is a self-contained HTML file you open in a browser and zoom or hover;
it is a triage visualization aid, not a detector.

## Typical use cases

### Plot the process activity of a Cuckoo report

```
plotcuckoojson -t "Sample triage" -f report.html 1_report.json
```

Outcome: writes an interactive HTML graph of the processes spawned during the sandbox run; open it in a browser, hover nodes for details, drag to zoom.

### Add file, registry and network edges to a Cuckoo plot

```
plotcuckoojson -t "Sample triage" -f report.html -fa -ra -na 1_report.json
```

Outcome: same process graph, with file (`-fa`), registry (`-ra`) and network (`-na`) activity linked to the processes that performed it, giving the full behavioral picture at a glance.

### Visualize a ProcMon CSV capture

```
plotprocmoncsv -sp -t "Run 1" -f run1.html kovter1.csv
```

Outcome: parses the Process Monitor CSV export and writes an HTML plot of the observed process tree (`-sp`) with its recorded activity.

### Filter a ProcMon plot to specific activity classes

```
plotprocmoncsv -sp -pfw -pfd -prw -pu -pt -t "Run 1" -f run1.html kovter1.csv
```

Outcome: plot restricted to the selected edges — file write/delete (`-pfw`/`-pfd`), registry write (`-prw`) and UDP/TCP network (`-pu`/`-pt`) — which keeps large captures readable when only one activity class matters.

*No corpus evidence — derived from upstream documentation.*
