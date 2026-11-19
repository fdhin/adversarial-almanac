---
name: "casefile"
categories:
  - blackarch-forensic
version: "1.0.1"
homepage: "http://www.paterva.com/web6/products/casefile.php"
packaged-by: [blackarch]
blackarch-builddate: "2025-12-20"
build-system: scripts
license: GPL-2.0-or-later
meta-checked: "2026-11-09"
github-repo: "ParrotSec/casefile"
github-stars: 2
github-pushed: "2017-07-26"
github-archived: false
github-issues: 0
github-match: "search"
gh-checked: "2026-11-07"
worth: 1.5
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---
# casefile

> Paterva's free link-analysis editor — the "little brother to Maltego" for graphing and connecting manually collected case data, without transforms.

## What it's for

CaseFile is a Java GUI application from Paterva (the Maltego authors) for graph and link analysis: the analyst manually adds entities — people, organizations, places, events, documents — and draws the relationships between them, producing an interactive mind map of a case. Unlike Maltego it performs no automated information gathering (no transforms); all data is entered or imported by hand, which makes it suited to recording and exploring what is already known rather than discovering new data. The product is discontinued; distributions package the archived 2012 community release, which is launched through the same platform as Maltego. It has no command-line interface — everything happens in the graph editor window.

## Typical use cases

### Open the graph editor

```
casefile
```

Outcome: starts the CaseFile Java GUI (on the packaged release, a wrapper that execs the bundled Maltego platform). In the window you add entities, connect them into a link graph, rearrange and annotate the result, and save the graph to re-open and extend it later.

*No corpus evidence — derived from upstream documentation.*
