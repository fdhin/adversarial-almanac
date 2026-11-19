---
name: "gore"
categories:
  - reversing
homepage: "https://github.com/goretk/gore"
origin: "hacktricks"
recipe: "upstream"
packaged-by: [aur]
build-system: go
license: BSD-3-Clause
meta-checked: "2026-11-13"
github-repo: "goretk/gore"
github-match: "exact"
github-stars: 546
gh-checked: "2026-11-13"
worth: 5.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-13"
---

# gore

> GoRE — the Go library that recovers packages, functions and types from stripped Go binaries; the analysis core behind tools like redress.

## What it's for

GoRE (package `gore`) is a library for analyzing compiled Go binaries, not a standalone CLI — you import it from Go code, or use it through its companion tool redress (goretk/redress), which wraps the same analysis in commands. From a binary it recovers: the compiler version (by searching for the identifiable version string, e.g. `go1.10.1`, with an API to force an assumption when guessing fails); function information from the pclntab — start and end addresses in the text section, source file, estimated first/last line numbers, and receivers for methods — with every function attributed to a package classified as standard library, third-party or main application; and full type information parsed from the typelink list (Go 1.7+ fully supported, 1.5/1.6 partially, older not at all). Via `Moduledata()` it also exposes module data including the static interface tables, handling both the pre-1.27 pointer-array layout and the contiguous records introduced in Go 1.27. The canonical repository is goretk/gore — copies under other accounts (e.g. the old figurophobia home) circulate in older documentation but are not the current source. Licensing is split: most files are BSD; a few features (GOROOT extraction) are AGPL, so binaries built without the `gore_bsd` build tag must be distributed as AGPL.

## Typical use cases

### Recover the package structure

The first map of an unknown binary.

```go
f, err := gore.Open(fileStr)
pkgs, err := f.GetPackages()
```

Outcome: the binary opens and every recovered package comes back classified as standard library, third-party or main application — the dependency map that tells you what the sample was built from and which parts are the author's own code.

### Dump all types

Match structures against known source.

```go
f, err := gore.Open(fileStr)
typs, err := f.GetTypes()
```

Outcome: all types parsed from the typelink list — struct layouts, names and kinds you can align with a suspect library's source to identify functionality fast; coverage is full for Go 1.7+, partial for 1.5/1.6.

### Walk the static interface tables

Find the concrete types behind interfaces.

```go
f, err := gore.Open(fileStr)
md, err := f.Moduledata()
itabs, err := md.ITabLinksData()
```

Outcome: the addresses of the static interface (itab) tables from the module data, resolving which concrete types implement which interfaces — the call handles both layout generations (pointer array before Go 1.27, contiguous records from 1.27 on), and `GetTypes` includes the types referenced by the new-style records.

No corpus evidence — derived from upstream documentation.
