---
name: "darm-git"
categories:
  - reversing
version: "416.cadffa7"
homepage: "https://github.com/jbremer/darm"
origin: "archstrike"
recipe: "archstrike-pkgbuild"
packaged-by: [archstrike]
build-system: make
license: custom
meta-checked: "2026-11-09"
github-repo: "jbremer/darm"
github-stars: 220
github-pushed: "2016-04-07"
github-archived: false
github-issues: 11
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# darm-git

> Lightweight ARMv7/Thumb/Thumb-2 disassembler-and-assembler library in C, packaged as libraries and headers only — no command-line tool.

## What it's for

darm decodes and encodes ARMv7, Thumb and Thumb-2 instructions in a small, dependency-free C library intended for embedding in analysis tooling; the source tree also carries optional Python (`import darm`, `darm.disasm_armv7(...)`) and Ruby bindings. The distro package installs no CLI and none of the upstream build utilities: it ships `libdarm.a`/`libdarm.so`, the public headers (`darm.h` plus the generated table headers) and a pkg-config entry, so the expected use is linking it into your own code — include `darm.h`, call `darm_armv7_disasm()` on an instruction word and render it with `darm_str2()`. The project has been unmaintained since 2016 and its own README recommends Capstone as the live alternative.

*No corpus evidence — derived from upstream documentation.*
