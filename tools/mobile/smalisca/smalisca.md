---
name: "smalisca"
categories:
  - blackarch-mobile
version: "58.1aa7a16"
homepage: "https://github.com/dorneanu/smalisca"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-26"
build-system: scripts
license: MIT
meta-checked: "2026-11-09"
github-repo: "dorneanu/smalisca"
github-stars: 323
github-pushed: "2019-10-03"
github-archived: true
github-issues: 16
github-match: "exact"
gh-checked: "2026-11-07"
worth: 0.6
worth-basis: "historical (archived/gone upstream)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# smalisca

> Parse a smali codebase into SQLite/JSON, then search it, graph it and serve it over a web API.

## What it's for

smalisca (by Victor "Cyneox" Dorneanu) does static code analysis on smali
files — the disassembled Dalvik you get after dumping an APK with apktool.
It works in two stages: `parser` walks a whole smali tree and extracts
classes, class properties, methods and the calls between them into SQLite or
JSON; `analyzer` then loads that store and gives you an interactive prompt to
search classes, methods and method calls with filters, and to draw the
results as Graphviz graphs (DOT, PDF, PNG). A third sub-command exposes the
parsed data as a REST web API for querying from other tools. The upstream
repository has been archived since 2019 and the code is Python 2 era, so
expect a legacy tool: indexing, searching and graphing, not vulnerability
detection.

## Typical use cases

### Index a dumped smali tree

Turn a directory of smali files into a queryable database.

```
smalisca parser -l path/to/dumped/smali -s java -f sqlite -o app.sqlite
```

Outcome: parses the directory (`-l`) for smali files, logs each extraction
stage (classes, properties, methods, calls) and commits everything to
`app.sqlite` (`-f json` writes JSON instead; `-s` selects the syntax
dialect to parse with).

### Search the parsed data interactively

Explore classes and cross-class calls without re-reading smali.

```
smalisca analyzer -f sqlite -i app.sqlite
```

Outcome: opens the `smalisca>` prompt; e.g. `sc -c class_name -p http -r 10
-x path` lists up to 10 classes whose name contains `http` (column filters,
result limit, excluded columns), and `scl -fc com/example -fm init` filters
method calls by calling class and method-name pattern.

### Run analysis commands from a file

Batch the interactive queries for repeatable triage.

```
smalisca analyzer -i app.sqlite -f sqlite -c cmd.txt
```

Outcome: executes the search/draw commands listed in `cmd.txt` (ending with
`quit`) one after another and exits — a scripted way to regenerate the same
tables and graphs for every sample.

### Export graphs of classes and calls

Visualize relationships from the analyzer prompt.

```
smalisca analyzer -f sqlite -i app.sqlite
```

then, at the prompt:

```
dc -c class_name -p test -f dot -o /tmp/classes.dot
```

Outcome: draws the classes matching the pattern into a Graphviz DOT file
(`-f pdf --prog neato` renders straight to a laid-out PDF using the chosen
Graphviz layout engine).

*No corpus evidence — derived from upstream documentation.*
