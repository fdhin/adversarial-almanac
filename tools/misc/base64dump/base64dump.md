---
name: "base64dump"
categories:
  - blackarch-misc
version: "0.0.14"
homepage: "https://blog.didierstevens.com/my-software/#base64dump"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-25"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "Seabreg/base64dump"
github-stars: 0
github-pushed: "2016-12-18"
github-archived: false
github-issues: 0
github-match: "search"
gh-checked: "2026-11-07"
worth: 1.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# base64dump

> Find and decode base64 — and a dozen other encoded formats — hidden inside any file.

*No corpus evidence — derived from upstream documentation.*

## What it's for

base64dump scans a file for strings that decode under a configurable set of encodings — plain BASE64, `\u` and `%u` unicode-escape, several hexadecimal notations (`\x90`, `0x90`, `&H90`), decimal byte lists and more — and reports every hit with its size, encoded and decoded previews, and an MD5 of the decoded data. It is a triage tool for malware analysis and file inspection when you suspect an encoded payload (shellcode, script, embedded binary) inside a document, capture or log but do not know where it is or which notation was used. Any listed item can then be dumped, hex-dumped or run through strings, with an optional translation step (e.g. UTF-16) for payloads that are encoded a second time. It only reports and decodes — it never fetches anything or executes what it finds. Written and distributed by Didier Stevens from his blog's software page.

## Typical use cases

### List decodable base64 strings in a file

```
base64dump sample.bin
```

Outcome: prints a table of ID, size, encoded fragment, decoded preview and MD5 for every base64 run found. Short hits are often coincidental (any brief alphanumeric run decodes), so raise the minimum decoded size with `-n` to cut noise.

### Dump the decoded payload

```
base64dump -s a -d sample.bin
```

Outcome: writes the raw decoded bytes of the selected items to stdout (`-s a` = all items; `-s 2` = only row 2) instead of the summary table — pipe into a file or another tool. Swap `-d` for `-x` or `-a` to get a hexdump or ASCII dump of the same selection.

### Hunt other encodings

```
base64dump -e all -n 8 suspect.js
```

Outcome: tries all supported encodings (unicode-escape, hexadecimal, decimal, letter-only, …) and lists hits of at least 8 decoded bytes — catches shellcode stored as `\x90\x90…`, `%u9090`, `0x90,0x90` or decimal lists that a plain base64 scan misses.
