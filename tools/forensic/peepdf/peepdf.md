---
name: "peepdf"
categories:
  - blackarch-forensic
version: "0.4.2"
homepage: "https://eternal-todo.com/tools/peepdf-pdf-analysis-tool"
packaged-by: [blackarch, aur]
blackarch-builddate: "2026-01-17"
build-system: python
license: GPL-3.0-or-later
aliases: ["python2-peepdf"]
meta-checked: "2026-11-09"
github-repo: "jesparza/peepdf"
github-stars: 1468
github-pushed: "2024-08-19"
github-archived: false
github-issues: 49
github-match: "search"
gh-checked: "2026-11-07"
worth: 3.8
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# peepdf

> All-in-one PDF analysis console: every version of the document, stream decoding, JavaScript and shellcode analysis, VirusTotal checks and modification tools.

## What it's for

peepdf (Jose Miguel Esparza) aims to cover a full PDF investigation without juggling three or four separate tools. It parses all versions of a document, handles object streams and encrypted files, decodes hex/octal/name obfuscation and the common stream filters, and drops you into an interactive console for exploration (`object`, `stream`, `bytes`, `decode`, `tree`, `metadata`, `changelog`, `references`, `extract`, `js_analyse`, `sctest`, `vtcheck`, ...). With PyV8 and pylibemu installed it also analyses embedded JavaScript and shellcode; it can emit XML/JSON reports, batch-execute console commands from a file, deliberately malform or rebuild PDFs, and check hashes on VirusTotal. For a quick keyword-based suspicion score first, see [[pdfid]]; for scripted object-level dumps, [[pdf-parser]]; for pure revision-history recovery, [[pdfresurrect]].

## Typical use cases

### One-shot summary of a document

```
peepdf sample.pdf
```

Outcome: prints a summary block — file hashes, detection of suspicious elements, number of versions, objects and streams, and which version holds the active content — then exits without opening the console.

### Explore a document interactively

```
peepdf -i sample.pdf
```

Outcome: opens the peepdf console prompt on the loaded file; `object 12` / `stream 12` show a decoded view, `bytes 0 64` a hex dump, `decode fl` applies a filter, `changelog` diffs versions, and `help` lists all commands. `quit` exits; `-g` disables colorized output.

### Script console commands for batch runs

```
peepdf -s commands.txt sample.pdf
```

Outcome: executes the console commands listed in the file in order and prints their output — the documented automation path over many files. A single console command can also be run without a TTY via `-C 'metadata'`.

### Get a machine-readable report

```
peepdf -j sample.pdf
```

Outcome: emits the document analysis as JSON (`-x` for XML) instead of console text, ready to feed downstream tooling.

### Load a damaged or deliberately malformed file

```
peepdf -f -l sample.pdf
```

Outcome: `-f` (force mode) ignores parsing errors so a truncated file still loads, and `-l` (loose mode) catches malformed objects strict parsing would reject — use when the one-shot run reports errors and refuses to analyze.

*No corpus evidence — derived from upstream documentation.*
