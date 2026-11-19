---
name: "expimp-lookup"
categories:
  - blackarch-binary
version: "4.79a96c7"
homepage: "https://github.com/tr3w/ExpImp-Lookup"
packaged-by: [blackarch]
blackarch-builddate: "2016-12-31"
build-system: scripts
license: GPL2
meta-checked: "2026-11-09"
github-repo: "tr3w/ExpImp-Lookup"
github-status: "gone"
github-match: "exact"
gh-checked: "2026-11-07"
worth: 4.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
---

# expimp-lookup

> Find which PE files in a directory tree export or import a given name.

## What it's for

ExpImp-Lookup is a small Python 2 utility for reverse-engineering sweeps over
collections of Windows binaries. It walks a directory tree (or takes a single
file), parses every Portable Executable with the companion PEace parser, and
prints every export or import name containing a user-supplied substring. The
question it answers is of the form "which binaries in this sample set import
`VirtualProtect`?" — useful when hunting for a specific capability across a
dropped toolkit. The upstream repository has been deleted; the tool survives
through the BlackArch package (`expimp-lookup`), which runs `expimp_lookup.py`
under Python 2.

*No corpus evidence — derived from upstream documentation (deleted upstream;
README usage recovered from the search-engine cache of the repository page and
cross-checked against the BlackArch packaging recipe).*

## Typical use cases

### Find which files import a function

```
expimp-lookup 1 VirtualProtect ./samples
```

Outcome: prints every PE under `./samples` whose import table contains the
string `VirtualProtect`, one match per line. Mode `1` restricts the search to
imports.

### Find which binaries export a name

```
expimp-lookup 0 CreateRemoteThread ./samples
```

Outcome: prints every PE in the tree that exports a name containing the
string, locating the provider of a given API. Mode `0` searches exports only;
mode `2` searches both directions in one pass. The path argument may be a
directory tree or a single executable file.
