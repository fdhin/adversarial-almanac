---
name: "qsreplace"
categories:
  - blackarch-misc
version: "3.0b053d2"
homepage: "https://github.com/tomnomnom/qsreplace"
packaged-by: [blackarch, nixpkgs]
blackarch-builddate: "2021-05-14"
build-system: go
license: MIT
meta-checked: "2026-11-09"
github-repo: "tomnomnom/qsreplace"
github-stars: 883
github-pushed: "2022-11-23"
github-archived: false
github-issues: 16
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# qsreplace

> Rewrites every query-string value in a stream of URLs with a value you choose, printing each parameter combination only once.

## What it's for

Reads URLs on stdin, replaces all query string parameter values with a user-supplied value (or appends to them), and outputs each unique combination of host, path, and parameter names only once. This is the canonical preprocessing step before parameter fuzzing or injection testing: it collapses a crawled URL list whose entries differ only in parameter values into one canonical line per parameter set, ready to feed to a fuzzer or scanner. Written in Go, pipe-friendly, and fast on very large URL lists.

## Typical use cases

### Canonicalize URLs by replacing all parameter values

Collapse a URL list to one line per parameter combination with a marker value.

```
cat urls.txt | qsreplace FUZZ
```

Outcome: every URL prints with all query values replaced by `FUZZ` (e.g. `https://example.com/path?one=FUZZ&two=FUZZ`), and URLs differing only in their values — including reordered parameters — collapse to a single line per host, path, and parameter set.

### Append to existing parameter values

Detect reflection of original values plus a suffix.

```
cat urls.txt | qsreplace -a XSS
```

Outcome: each parameter value gets the suffix appended (e.g. `?one=1XSS&two=2XSS`) while the original value is preserved, so a reflected `1XSS` in a response proves the original value reaches the output.

### Deduplicate parameter combinations without changing values

Only emit the first URL seen for each unique parameter set.

```
cat urls.txt | qsreplace -a
```

Outcome: values are left untouched and each host, path, and parameter-name combination is printed once — a pure deduplication pass over a duplicated URL list.

*No corpus evidence — derived from upstream documentation.*
