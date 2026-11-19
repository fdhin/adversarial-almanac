---
name: "rtfm"
categories:
  - blackarch-misc
version: "95.a807a80"
homepage: "https://github.com/leostat/rtfm"
packaged-by: [blackarch, aur, nixpkgs]
blackarch-builddate: "2026-01-13"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "leostat/rtfm"
github-stars: 749
github-pushed: "2024-01-30"
github-archived: false
github-issues: 3
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.7
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# rtfm

> Searchable terminal database of commands and one-liners for security work.

## What it's for

rtfm is a terminal search engine over a community-maintained SQL database of commands, one-liners and snippets — a self-described spiritual successor to the Red Team Field Manual book. Every entry carries the command itself, a comment explaining what it does, tags (linux, XSS, pivoting, buffer overflow, ...), reference URLs and the author who added it. Reach for it when you half-remember a command's syntax or want a curated set of payloads for a technique instead of grepping shell history. It is a lookup tool only — it never executes the commands it returns. Run the update step once before first searching; you can also insert your own entries interactively and dump them in a format suitable for contributing back upstream.

## Typical use cases

### Populate or refresh the command database

Fetch the latest community database updates before first use and periodically after.

```
rtfm -u
```

Outcome: contacts the upstream update feed, prints version information and progress lines, and merges new commands, tags and references into the local database; the "No DB, please run rtfm -u" warning disappears on the next search.

### Search everything at once

When you know roughly what you are after (a technique, protocol or keyword) but not which field it lives in.

```
rtfm -e x11 -pP
```

Outcome: prints every entry whose command, comment or references mention the term as a pretty boxed table (`-pP`) containing the full command, its comment, tags, date added and reference URLs — ready to copy into a terminal.

### List commands grouped by tag

Browse all snippets for a technique family rather than a specific keyword.

```
rtfm -t xss -pP
```

Outcome: prints every entry carrying the given tag in the same pretty-table format; run `rtfm -Dt` first to list all known tags if you are unsure what exists.

### Look up syntax for one known command

When you know which binary you need but not its common flag combinations.

```
rtfm -c sqlmap -pP
```

Outcome: prints only entries whose command field matches, typically curated one-liners showing frequently used flags for that tool.

*No corpus evidence — derived from upstream documentation.*
