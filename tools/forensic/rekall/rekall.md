---
name: "rekall"
categories:
  - blackarch-forensic
version: "1.7.2rc1.r68.g55d1925"
homepage: "https://github.com/google/rekall"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-13"
build-system: scripts
license: GPL-2.0-only
meta-checked: "2026-11-09"
github-repo: "google/rekall"
github-stars: 2008
github-pushed: "2020-10-18"
github-archived: true
github-issues: 201
github-match: "exact"
gh-checked: "2026-11-07"
worth: 0.6
worth-basis: "historical (archived/gone upstream)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# rekall

> Google's memory-forensics framework forked from Volatility in 2013 — now discontinued, but still parses Windows/Linux/macOS memory images.

## What it's for

Rekall is a complete memory-forensics and incident-response framework: it loads raw physical memory images (Windows XP SP2/3 through Windows 10, Linux 2.6.24+, and macOS 10.7–10.12), reconstructs kernel structures, and exposes plugins for process listings, networking, command lines, and artifact extraction; it also shipped memory-acquisition tools such as winpmem. It began as a December 2011 Volatility branch exploring modularity and performance, was forked as a stand-alone project in December 2013, and was used inside Google's GRR for remote live analysis. The project is explicitly discontinued and archived — physical-memory analysis became too fragile and maintenance-heavy to keep alive — so treat it as a historical/legacy tool: for new work prefer maintained alternatives (e.g. Volatility 3), and note winpmem lives on separately under the Velocidex organization.

## Typical use cases

### Open a memory image in the interactive console

Load an image and poke at it interactively.

```
rekall -f memory.raw
```

Outcome: Rekall detects the image's OS profile and drops you into an interactive console prompt (plugin names tab-complete); type plugin names there to run them. The binary is also installed under the historical spelling `rekal`.

### Run a single plugin non-interactively

Get one artifact from an image in batch mode.

```
rekall -f memory.raw pslist
```

Outcome: prints the process listing reconstructed from the image's kernel structures (offset, name, pid, ppid, timestamps) to stdout, then exits — suitable for scripted triage of many images.

### Walk the process tree

See parent/child relationships to spot injected or orphaned processes.

```
rekall -f memory.raw pstree
```

Outcome: processes print as an indented tree by parent/child relationship, making it easier to spot anomalies like orphaned or oddly spawned processes than in the flat pslist view.

### Check the installed version

Confirm which Rekall you are running before comparing plugin output.

```
rekall --version
```

Outcome: prints the Rekall version components and exits without loading an image — useful because plugin behavior differs across releases.

*No corpus evidence — derived from upstream documentation.*
