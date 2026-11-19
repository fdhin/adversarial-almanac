---
name: "parse-evtx"
categories:
  - blackarch-forensic
version: "3.a4b02b9"
homepage: "https://github.com/KasperskyLab/ForensicsTools"
packaged-by: [blackarch]
blackarch-builddate: "2018-06-17"
build-system: cmake
license: GPL
meta-checked: "2026-11-09"
github-repo: "KasperskyLab/ForensicsTools"
github-stars: 123
github-pushed: "2018-01-25"
github-archived: false
github-issues: 1
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.5
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# parse-evtx

> Kaspersky's standalone C++ parser that dumps Windows EVTX event logs as readable per-record summaries with event IDs resolved to descriptions.

## What it's for

parse_evtx (Igor Soumenkov, from Kaspersky Lab's ForensicsTools collection) parses the Windows XML Event Log format — the `.evtx` files Windows has used since Vista — and prints each record in a readable form: record number, timestamp, and the event's attribute pairs with the event ID resolved against a built-in table of known event descriptions, plus the log's template data where present. It has no options at all: name one or more `.evtx` files as arguments and read stdout. It works on saved files, so copy the logs off the target (or out of an image) first; it does not attach to the live event service. For broader Windows-artifact parsing in this vault see [[lfle]] ($LogFile), [[indxparse]] ($INDX) and [[evtkit]] (EVTX toolkit) — separate tools by other authors covering adjacent artifacts.

## Typical use cases

### Dump an event log

Read one evtx file's records.

```
parse-evtx Security.evtx
```

Outcome: one summary line per record — `Record #<n>` with its `YYYY.MM.DD-HH:MM:SS` timestamp, followed by attribute pairs in the form `'name':<eventID> (<description>)` where the built-in table knows the event — a quick human-readable view of the log; a file it cannot parse ends with `Failed on Security.evtx`.

### Batch a set of logs into one stream

Process several files in one invocation.

```
parse-evtx System.evtx Security.evtx Application.evtx > events.txt
```

Outcome: each file is parsed in argument order into a single redirected stream — grep it, or import the combined output into timeline tooling.

*No corpus evidence — derived from upstream documentation.*
