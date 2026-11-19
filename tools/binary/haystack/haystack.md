---
name: "haystack"
categories:
  - blackarch-binary
version: "1823.c178b5a"
homepage: "https://github.com/trolldbois/python-haystack"
packaged-by: [blackarch, aur]
blackarch-builddate: "2017-11-19"
build-system: scripts
license: GPL3
meta-checked: "2026-11-09"
github-repo: "trolldbois/python-haystack"
github-stars: 95
github-pushed: "2017-07-26"
github-archived: false
github-issues: 19
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.5
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# haystack

> python-haystack: heap analysis framework that locates known C structures in process memory.

## What it's for

python-haystack finds instances of known C record types in process memory,
either live (ptrace or Frida attach) or from dumps (its own dump format,
Windows minidumps, Rekall and Volatility images, Cuckoo dumps). You describe a
target structure as a Python ctypes record — typically generated from a C
header with ctypeslib — add field constraints (pointer validity is enforced
automatically; fields can be marked NotNull, given value ranges, or ignored),
and the search walks every offset of mapped memory reporting placements that
satisfy the constraints. Typical aims are pulling session structures or
credentials out of a running process, or validating a reversed structure
definition against real heap data. This is a Python 2 project dormant since
2017; the GUI companion no longer works, and automatic structure reversal
lives in the separate python-haystack-reverse extension.

*No corpus evidence — derived from upstream documentation.*

## Typical use cases

### Capture a dump of a live process

```
haystack-live-dump 4242 ./target.dump
```

Outcome: ptraces the process and writes a haystack-format dump — one file per
memory map named by address range plus a `mappings` metadata file — which the
search and show entry points can then open via `dir://` URLs.

### Search a live process for a known structure

```
sudo haystack-live-search --pickled 4242 sslsnoop.ctypes_openssh.session_state search
```

Outcome: searches the live process for the named ctypes record and, with
`--pickled`, writes the matching instances including pointed-to child
structures as a Python pickle that can be reloaded or refreshed later. The
record argument is any importable Python ctypes structure definition.

### Search an existing dump

```
haystack-search dir:///home/user/target.dump myctypes.session_state
```

Outcome: runs the same constrained record search against a dump folder and
prints the addresses of valid placements. Handler URL schemes select the
memory source: `dmp://` (minidump), `live://` and `frida://` (live process),
`rekall://` and `volatility://` (system images). Related entry points:
`haystack-show` to pretty-print one record at a specific address, and
`haystack-find-heap` to report Windows heap attributes (LAL/LFH) from a dump.
