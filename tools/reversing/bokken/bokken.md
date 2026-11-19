---
name: "bokken"
gentoo-category: "dev-util"
version: "1.8"
homepage: "https://inguma.eu/projects/bokken"
origin: "pentoo"
recipe: "pentoo-ebuild"
packaged-by: [pentoo]
build-system: python
license: GPL-2
meta-checked: "2026-11-09"
github-repo: "inguma/bokken"
github-stars: 118
github-pushed: "2017-05-18"
github-archived: false
github-issues: 15
github-match: "search"
gh-checked: "2026-11-07"
worth: 2.5
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# bokken

> Abandoned PyGTK front-end that drives the pyew and radare2 analysis engines for PE/ELF, Java and PDF files.

## What it's for

Bokken is a graphical reverse-engineering suite that uses an installed pyew or radare2 as its engine, aimed at malware and vulnerability analysis of PE/ELF executables, Java classes and PDF documents: disassembly, hex and graph visualization without memorizing either engine's console commands. Upstream is explicit that it is a basic disassembler, not a hex editor or full-featured analysis suite. Development has been halted in favour of Iaitō (which later became Cutter), it is Python 2 and GTK-era software, and the repository has been idle since 2017 — on current distributions it is mainly of historical interest.

## Typical use cases

### Launch the GUI

Start Bokken with its default engine.

```
bokken
```

Outcome: the GTK GUI opens with an empty workspace; load a file from the UI to begin analysis.

### Analyze a file from the command line

Hand the target directly to Bokken.

```
bokken sample.exe
```

Outcome: the GUI opens with sample.exe already loaded and ready for disassembly.

### Force the radare2 engine

Use the radare2 core instead of pyew for the session.

```
bokken -r sample.exe
```

Outcome: the loaded file is analyzed through the radare2 core, exposing radare-driven disassembly and graph views.

### Expose the session over the built-in web UI

Serve Bokken through its internal web server.

```
bokken -w -b 127.0.0.1 -l 8080 sample.exe
```

Outcome: starts Mune, the internal web server, bound to 127.0.0.1:8080 (defaults are all interfaces on port 4546), making the loaded file browsable from a web browser.

*No corpus evidence — derived from upstream documentation.*
