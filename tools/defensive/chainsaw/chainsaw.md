---
name: "chainsaw"
categories:
  - blackarch-defensive
version: "v2.7.3.r6.g5d908fd"
homepage: "https://github.com/countercept/chainsaw"
packaged-by: [blackarch, aur, nixpkgs]
blackarch-builddate: "2023-09-22"
build-system: go
license: GPL3
meta-checked: "2026-11-09"
github-repo: "WithSecureOpenSource/chainsaw"
github-stars: 3715
github-pushed: "2026-11-05"
github-archived: false
github-issues: 5
github-match: "exact"
gh-checked: "2026-11-09"
worth: 7.9
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 67
gh-activity-checked: "2026-11-09"
---

# chainsaw

> Fast Rust-based searcher and hunter over Windows event logs (EVTX) and other forensic artefacts — keyword, regex, structured queries and Sigma rules.

## What it's for

Chainsaw (WithSecure Countercept) gives incident responders a rapid first pass over collections of Windows event logs: it loads directories of .evtx files and searches them with plain strings, regular expressions, or Tau structured expressions against parsed event fields (for example a specific Event ID), and can hunt whole Sigma rule sets through a mapping file. Beyond EVTX it dumps registry hives and analyses the SRUM database. The CLI has three main subcommands — search, hunt and dump — and can output human-readable tables, CSV or JSON. It is analysis tooling for logs you already collected, not an agent or collector.

## Typical use cases

### Keyword search across event logs

Find a string of interest across a whole log collection.

```
chainsaw search mimikatz -i evtx_logs/
```

Outcome: every event in every .evtx under evtx_logs/ that contains the case-insensitive string is printed as a table row with timestamp, event data and source file; -q suppresses the banner and progress output.

### Pull one event ID with a structured query

Extract a specific event type such as PowerShell script-block logging.

```
chainsaw search -t 'Event.System.EventID: =4104' evtx_logs/
```

Outcome: only events whose EventID field equals 4104 are returned, using Tau field-path syntax to query the parsed event document instead of regexing raw XML.

### Regex search with JSON output for pipeline tooling

Match hostname patterns and emit machine-readable hits.

```
chainsaw search -e 'DC[0-9]\.example\.com' evtx_logs/ --json
```

Outcome: events matching the regular expression are printed as JSON objects, ready for jq or import into case notes.

### Hunt with Sigma detection rules

Run an entire Sigma rule set against a log collection at once.

```
chainsaw hunt evtx_logs/ -s sigma/ --mapping mappings/sigma-event-logs-all.yml
```

Outcome: Chainsaw loads the Sigma rules (filterable by --level, --status, --kind), hunts all EVTX files and reports detections grouped by rule with timestamps, computers and users; add -r rules/ to also use native Chainsaw rules, and --csv or --json with -o results/ to export.

### Dump a registry hive to reviewable JSON

Extract structured data from a binary forensic artefact.

```
chainsaw dump ./SOFTWARE --json --output ./software.json
```

Outcome: the registry hive is parsed and dumped to JSON, making it greppable; the analyse srum subcommand similarly parses a SRUM database when given the SOFTWARE hive with --software.

*No corpus evidence — derived from upstream documentation.*
