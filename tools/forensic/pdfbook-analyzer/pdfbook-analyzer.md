---
name: "pdfbook-analyzer"
categories:
  - blackarch-forensic
version: "2"
homepage: "http://sourceforge.net/projects/pdfbook/"
packaged-by: [blackarch]
blackarch-builddate: "2016-12-31"
build-system: scripts
license: GPL2
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# pdfbook-analyzer

> Recover Facebook artifacts — messages, activities, emails, user IDs — from the strings of a browser process memory dump.

## What it's for

pdfbook is a 2009-era forensic script by Jeff Bryner that mines a web browser's process memory for Facebook residue. The workflow: dump the memory of a running browser process (e.g. with a process dumper), extract its strings (`strings -el` for UTF-16LE), and run the script over the result. It matches 2009-generation Facebook UI markup — news-feed stories, user-info blocks, internal notification emails — and prints story messages, recent activities, email details and user-ID/name associations, including its best guess at the account owner of the artifacts. The regexes are tied to that era's Facebook HTML, so against current Facebook traffic it finds little; its value is the strings-from-memory methodology and historical artifact work, and it expects a Python 2 runtime.

## Typical use cases

### Extract Facebook artifacts from a memory dump

Convert a process dump to strings, then scan them.

```
strings -el browser.dump > memorystrings.txt
pdfbook.v2.py -f memorystrings.txt
```

Outcome: prints lines prefixed by artifact type — `StoryMessage:`, `RecentActivity:`, `FacebookEmailDetail author:/Date:/Body:` and the collected `FacebookUserID`/name pairs — and finishes with the likely owner of the artifacts when one account dominates the dump.

### Read from stdin with verbose context

```
cat memorystrings.txt | pdfbook.v2.py -v
```

Outcome: with no `-f` file the script reads stdin; `-v` adds per-item context (filename and item hash) to the deduplicated story-message and activity output. `-V` prints version info and exits.

*No corpus evidence — derived from upstream documentation.*
