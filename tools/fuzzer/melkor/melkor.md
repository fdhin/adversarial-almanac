---
name: "melkor"
categories:
  - blackarch-fuzzer
version: "1.0"
homepage: "http://packetstormsecurity.com/files/127924/Melkor-ELF-Fuzzer.0.html"
packaged-by: [blackarch]
blackarch-builddate: "2018-11-15"
build-system: make
license: GPL2
meta-checked: "2026-11-09"
github-repo: "IOActive/Melkor_ELF_Fuzzer"
github-match: "exact"
worth: 3.0
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
gh-checked: "2026-11-09"
github-stars: 157
github-pushed: "2023-12-18"
github-archived: false
github-issues: 2
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# melkor

> Knowledge-based ELF file format fuzzer that corrupts chosen ELF metadata of a sample into malformed "orcs" for crashing ELF parsers.

*No corpus evidence — derived from upstream documentation.*

## What it's for

Melkor (Alejandro Hernández "nitr0us", 2014, written in C) is a hybrid mutation/generation fuzzer for the ELF format. Instead of flipping bytes at random, it applies a knowledge base of fuzzing rules — ELF specification violations and semi-valid boundary values — to the metadata structures you select: ELF header, section/program header tables, symbols, relocations, dynamic section, notes, and string tables. You give it any ELF file as a template and it writes a directory of malformed "orcs"; choose structures according to the parser class under test (program headers for OS loaders, section headers and header for antivirus engines, symbols/relocations for linkers). It does not run or triage the orcs itself — that is done with the parser you are testing; the upstream distribution additionally ships `test_fuzzed.sh`, which automates replaying orcs through a chosen program.

## Typical use cases

### Fuzz one metadata structure of a sample ELF

Target exactly the structure a specific parser reads.

```
melkor -S ./libfoo.so -n 200 -l 50
```

Outcome: applies the section-header-table rules to `libfoo.so`, writing 200 orc files (`-n`) into an `orcs_*` directory under the current directory; `-l 50` makes each fuzzing rule fire with 50% likelihood, trading run size for rule coverage.

### Autodetect the structures to fuzz from the file type

Let the tool pick appropriate metadata for an object, executable, or library.

```
melkor -a ./sample.o -n 500
```

Outcome: fuzzes the metadata structures appropriate to the sample's `e_type` (all structures except the ELF header), producing 500 orcs split across per-structure `orcs_*` directories.

### Fuzz everything except the ELF header

Maximize rule coverage across all structures at once.

```
melkor -B ./libfoo.so -n 1000 -q
```

Outcome: applies rules across every supported structure except the ELF header, writing 1000 orcs; `-q` suppresses the per-rule output that normally prints each executed rule.

### Replay the orcs through the parser under test

Triage which mutated files crash the target parser.

```
for f in orcs_*/*; do readelf -a "$f" >/dev/null 2>&1 || echo "crashed: $f"; done
```

Outcome: runs the ELF parser over every orc and lists the inputs whose process died abnormally; the upstream `test_fuzzed.sh` wraps this same loop (and can also fuzz environment variables), and after loader-focused runs `dmesg` shows which library faulted and where.
