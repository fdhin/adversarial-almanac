---
name: "deen"
categories:
  - blackarch-crypto
version: "601.fd9aebe"
homepage: "https://github.com/takeshixx/deen"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-12"
build-system: python
license: Apache
meta-checked: "2026-11-09"
github-repo: "takeshixx/deen"
github-stars: 47
github-pushed: "2025-03-29"
github-archived: true
github-issues: 11
github-match: "exact"
gh-checked: "2026-11-07"
worth: 0.6
worth-basis: "historical (archived/gone upstream)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# deen

> Qt workbench and CLI for applying encodings, compression and hashing to data.

## What it's for

deen applies chained data transformations from a plugin set: codecs (Base16/32/64/85, hex, URL, HTML entities, JWT, ROT13, EBCDIC, uuencode, Unix timestamps), compressions (gzip, bz2, lzma), hashes (MD, SHA, BLAKE2, bcrypt, NTLM, MySQL), formatters, assembly/disassembly helpers (with optional keystone/capstone) and multi-step assembly plugins. The same loader drives a PyQt5 GUI for interactive conversion work and a subcommand-style CLI for pipelines. Reach for it during payload or protocol triage when data must be repeatedly re-encoded between formats; it is a transformer, not a cracker — it will not break passwords or keyed ciphers.

## Typical use cases

### List the available plugins

```
deen -l
```

Outcome: prints every loaded plugin grouped by category, plus a section for plugins that failed to load (e.g. missing optional dependencies) — this is where exact plugin names come from.

### Encode data on the command line

```
deen base64 "example data"
```

Outcome: writes the transformed bytes to stdout (`ZXhhbXBsZSBkYXRh`); every plugin is exposed as a subcommand (`hex`, `url`, `gzip`, `md5`, ...).

### Decode by reverting a plugin

```
deen base64 -r "aGVsbG8="
```

Outcome: prints `hello`. The `-r`/`--revert` flag runs the plugin's unprocess direction; prefixing the subcommand with a dot (`.base64`) is an equivalent alias.

### Transform a file or stream

```
deen hex -f payload.bin
```

Outcome: hex-encodes the file's bytes to stdout; `-f -` (or piping with no positional data) reads stdin, so deen can sit inside a pipeline.

### Open the GUI

```
deen
```

Outcome: starts the PyQt5 window with plugin buttons and a live input/output preview; `--fullscreen` opens it maximized, and the status console reports any plugins that did not load.

*No corpus evidence — derived from upstream documentation.*
