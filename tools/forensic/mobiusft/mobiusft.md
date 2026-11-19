---
name: "mobiusft"
categories:
  - blackarch-forensic
version: "1.12"
homepage: "http://savannah.nongnu.org/projects/mobiusft"
packaged-by: [blackarch]
blackarch-builddate: "2019-05-15"
build-system: autotools
license: GPL2
meta-checked: "2026-11-09"
github-repo: "eduardoaguiar-oss/mobiusft"
github-stars: 5
github-pushed: "2026-11-07"
github-archived: false
github-issues: 0
github-match: "search"
gh-checked: "2026-11-07"
worth: 6.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 518
gh-activity-checked: "2026-11-09"
---

# mobiusft

> Open-source forensic case-management framework that organizes investigations as cases and evidence items and is extended through plug-ins.

## What it's for

Mobius Forensic Toolkit (by Eduardo Aguiar) structures digital-forensics work: you create a case, attach case items (evidence sources such as image files or physical devices), and process them through extensions. The packaged 1.x generation is a C++/Python-GTK desktop application with a Python API (`pymobius`) — cases and item categories are defined in XML and case data is stored in SQLite, and bundled extensions (`.mobius` plug-ins such as image-file and physical-device datasources, chat and attribute viewers, a cellphone agent) demonstrate the extension model. Operation is GUI-driven rather than scripted, and the packaged 1.12 release belongs to the Python 2/GTK 2 era. Upstream has since been rewritten as 2.x (C++20/Python 3, CMake, hosted on GitHub) with the same case model plus browser, P2P and Windows-artifact parsers.

## Typical use cases

### Launch the forensic case manager

Start the GUI to create or open a case and work its evidence items.

```
mobius
```

Outcome: opens the Mobius Forensic Toolkit GTK window (the `mobius` wrapper runs `mobius_bin.py`); from there you create a case, add case items, and invoke extensions. Further interaction happens through the GUI or the `pymobius` extension API, not command-line flags.

*No corpus evidence — derived from upstream documentation.*
