---
name: "halcyon"
categories:
  - blackarch-recon
version: "1.0"
homepage: "https://www.blackhatlibrary.net/Halcyon"
packaged-by: [blackarch, aur]
blackarch-builddate: "2026-01-12"
build-system: scripts
license: GPL-3.0-or-later
meta-checked: "2026-11-09"
github-repo: "Obsidian-Covenant/halcyon"
github-stars: 0
github-pushed: "2025-12-15"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-09"
worth: 3.9
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 2
gh-activity-checked: "2026-11-09"
---

# halcyon

> Walk a git repository's history and emit per-version MD5 signatures of its files.

## What it's for

halcyon crawls a git repository — a local clone or a remote URL cloned on
the fly — through its entire commit history and, for every tracked file at
every commit where it exists, records `md5(git show <commit>:<file>)`. Each
commit is labeled with a version string extracted (via a regex you supply)
from one designated check file, such as `VERSION`. The result is a
signature collection under `./sigs/` mapping file content to product
versions — the raw material for version fingerprinting: identifying which
release of a file, plugin or theme a deployed copy matches, or flagging
content that matches no released version. Filters (`--omit-directory`, `-t`
top-N files) keep the sweep bounded on large histories. The packaged build
is the Obsidian-Covenant Python 3 clean-room reimplementation of the
original blackhatlibrary tool; it only needs `git` and Python.

## Typical use cases

### Build signatures from a remote repository

```
halcyon -c -u https://github.com/example/example-repo \
    -f VERSION -m "^([0-9]+(?:\.[0-9]+)+)" -t 10
```

Outcome: clones the repository (`-c`), walks its history, and writes
YAML-ish signature files under `./sigs/` (`<repo>-<basename>`) mapping every
version's tracked files to their MD5 digests.

### Fingerprint a local checkout, skipping noise directories

```
halcyon -u ./my-local-repo \
    -f VERSION -m "^([0-9]+(?:\.[0-9]+)+)" --omit-directory "docs,vendor"
```

Outcome: the same signature walk over a local clone with `docs/` and
`vendor/` excluded from tracking, keeping the output focused on shipped
code.

*No corpus evidence — derived from upstream documentation.*
