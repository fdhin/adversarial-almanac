---
name: "notspikefile"
categories:
  - blackarch-fuzzer
version: "0.1"
homepage: "http://packetstormsecurity.com/files/39627/notSPIKEfile.tgz.html"
packaged-by: [blackarch]
blackarch-builddate: "2018-11-15"
build-system: make
license: GPL
meta-checked: "2026-11-09"
github-repo: "ksdpmx/notSPIKEfile"
github-stars: 0
github-pushed: "2020-10-18"
github-archived: false
github-issues: 0
github-match: "search"
gh-checked: "2026-11-07"
worth: 1.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# notspikefile

> File format fuzzer that walks a seed file byte by byte, substitutes known-nasty values at each position, and replays every mutant through the program that parses it.

*No corpus evidence — derived from upstream documentation.*

## What it's for

notSPIKEfile (Adam Greene, iDefense, 2005 — the "not" distinguishes it from the original SPIKEfile) is a Linux file-format fuzzer for any target that accepts a filename argument. For each byte offset in the base file it substitutes every value from its set of known fuzz values, runs the target command on the mutant, and records crashes, writing per-crash details to `.txt` files beside the fuzzed files. Timeouts, kill signals, concurrency, and byte/fuzz-value ranges are all configurable, so large formats can be fuzzed in slices. Beware shell-script launchers (Acrobat, RealPlayer): fuzz the real binary they exec, not the wrapper.

## Typical use cases

### Fuzz a file parser end to end

Walk an entire seed file with the full fuzz-value set (the upstream example pattern).

```
notspikefile -t 3 -d 1 -m 6 -s SIGKILL -o FUZZY.gif sample.gif "/usr/bin/display -debug %FILENAME%"
```

Outcome: writes `FUZZY.gif.*` mutants of `sample.gif`, executes the command on each (up to 6 concurrent with `-m`), kills processes hung past 3 seconds (`-t`) with SIGKILL (`-s`), and finishes by printing the total crash count; crash details are recorded in `.txt` files next to the mutants.

### Fuzz only a header slice in string mode

Cover a large format's header quickly instead of walking the whole file.

```
notspikefile -S -r 0-512 -o fz.pdf base.pdf "pdftotext %FILENAME% -"
```

Outcome: replaces only bytes 0-512 (`-r`) of the PDF with "fancy string" values (`-S`) and replays each mutant through the parser, so header parsing is covered in a fraction of the full-run time.

### Blob-replace across the whole file

Substitute whole blobs rather than single values.

```
notspikefile -B -o fz.bin firmware.bin "binwalk %FILENAME%"
```

Outcome: generates mutants with blob substitutions (`-B`) at every offset of `firmware.bin` and runs the replay command on each, reporting processes that die abnormally and logging per-crash information for later triage.
