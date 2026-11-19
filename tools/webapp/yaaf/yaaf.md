---
name: "yaaf"
categories:
  - blackarch-webapp
version: "7.4d6273a"
homepage: "https://github.com/RomeuG/YAAF"
packaged-by: [blackarch]
blackarch-builddate: "2024-04-28"
build-system: scripts
license: GPL-1.0-or-later
meta-checked: "2026-11-09"
github-repo: "RomeuG/YAAF"
github-status: "gone"
github-match: "exact"
gh-checked: "2026-11-07"
worth: 4.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
---

# yaaf

> Yet Another Admin Finder — a Python 3 script that brute-forces common admin-panel paths under a target site.

## What it's for

YAAF (by RomeuG) locates administrative panels on a website by trying common admin paths from a wordlist. Compared to older admin finders it adds multi-threading (1 to 10 threads), custom file extensions (e.g. try `.php` on every candidate), custom wordlists, a configurable User-Agent, optional sanitizing of URLs to a canonical form via its `--sanitize` argument, and result logging into `~/.yaaf_output`. It was written in Python 3.4 as a learning project, explicitly replacing a simpler admin finder the author found too limited. The upstream repository is gone (deleted by the author), so the tool is documented from the last archived state of the project page; its full option list is available in the tool itself via `--help`.

## Typical use cases

### List the tool's options

```
yaaf --help
```

Outcome: prints YAAF's complete option list (wordlist, extension, thread count, User-Agent, sanitization, output) — the authoritative reference for the CLI, since the upstream repository no longer exists.

*No corpus evidence — derived from upstream documentation (the archived project page of the now-deleted RomeuG/YAAF repository); the CLI beyond `--help`/`--sanitize` could not be re-verified, so no further command examples are given.*
