---
name: "unstrip"
categories:
  - blackarch-disassembler
version: "13.05e00c2"
homepage: "https://github.com/pzread/unstrip"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-14"
build-system: scripts
license: GPL3
meta-checked: "2026-11-09"
github-repo: "pzread/unstrip"
github-stars: 106
github-pushed: "2015-08-19"
github-archived: false
github-issues: 1
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.4
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# unstrip

> Recovers function names in stripped, statically linked ELF binaries by fingerprint-matching their code against unstripped static libraries.

## What it's for

A statically linked binary stripped with `gcc -s` still contains the machine code of every library function it calls — only the names are gone. unstrip rebuilds them in two phases: first it fingerprints the object files inside unstripped static archives (`libc.a`, `libpthread.a`, or your own toolchain's `.a` files) into a database; then it matches basic blocks of the target binary against those fingerprints and writes an annotated copy. The result is not a full symbols-plus-debuginfo restoration — matching is heuristic and the tool is 2015-era Python 2 code — but it turns anonymous `callq 0x408bf0` sites into `callq 408bf0 <puts>` for objdump, which makes reading stripped firmware or servers far easier. The input binary is never modified; output lands in a new `.mark` file.

## Typical use cases

### Build the fingerprint database

Index static-library object files once per archive set.

```
mkdir archobj
cp /usr/lib/libc.a /usr/lib/libpthread.a archobj/
unstrip gendb
```

Outcome: fingerprints every object file in archobj/ and stores them in `fin.db`. The step opens thousands of files at once, so first raise the open-file limit (`ulimit -n 65536`); regenerate the database whenever your toolchain's libraries change.

### Unstrip a statically linked binary

Annotate a stripped executable with recovered names.

```
unstrip example.stripped
```

Outcome: writes `example.stripped.mark` — a copy of the binary with matched functions labeled. Running `objdump -d example.stripped.mark` now shows headers like `000000000040105e <main>:` and annotated call targets such as `<puts>` and `<system>` instead of bare addresses; the original file is left untouched.

*No corpus evidence — derived from upstream documentation.*
