---
name: "volatility-extra"
categories:
  - blackarch-forensic
version: "92.d9fc072"
homepage: "https://github.com/volatilityfoundation/community"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-26"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "volatilityfoundation/community"
github-stars: 378
github-pushed: "2021-04-05"
github-archived: false
github-issues: 15
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.8
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---
# volatility-extra

> Community-contributed plugin pack for the Volatility 2 memory-forensics framework, loaded at runtime with `--plugins`.

## What it's for

This is the Volatility Foundation's `community` repository: a frozen (2021) collection of third-party plugins for the Volatility 2 framework, gathered per author under one directory tree — extra artifact parsers, browser-history recovery, SQLite carving, malware-family helpers and more beyond the built-in plugin set. It is not a standalone program: every invocation goes through the parent Volatility 2 `vol.py`, on which it depends entirely (profiles, `-f` image loading and `--profile` all belong to that framework, which has no separate note in this vault). The plugins target Volatility 2's Python 2 plugin API and do not load into Volatility 3. Quality varies — each author's subdirectory carries its own README with usage, dependencies and license.

## Typical use cases

### Load the pack and see what it adds

Register every community plugin in one go.

```
vol.py --plugins=/opt/volatility-community --help
```

Outcome: Volatility recursively registers all plugins under the directory, and the command listing grows by every community plugin name — run any of them against an image by adding `-f memory.dump --profile=<profile>`.

### Run a community plugin

Use one of the collected plugins on a dump.

```
vol.py --plugins=/opt/volatility-community -f memory.dump --profile=Win7SP1x64 sqlitefind
```

Outcome: runs the pack's `sqlitefind` plugin (MichaelBrown subdirectory), which carves SQLite databases out of the memory image and recovers table schemas and rows; check the plugin's subdirectory README for plugin-specific options.

### Load selected authors only

Avoid option-name collisions between plugins.

```
vol.py --plugins=/opt/volatility-community/MichaelBrown:/opt/volatility-community/AdamBridge -f memory.dump --profile=Win7SP1x64 sqlitefind
```

Outcome: loads only the named subdirectories instead of the whole tree; pointing `--plugins` at the root can let two plugins register the same option and abort, so split per-author paths with `:` on Linux/macOS (`;` on Windows).

*No corpus evidence — derived from upstream documentation.*
