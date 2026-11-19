---
name: "elfparser"
categories:
  - blackarch-binary
version: "7.39d21ca"
homepage: "https://github.com/jacob-baines/elfparser"
packaged-by: [blackarch]
blackarch-builddate: "2019-05-14"
build-system: cmake
license: MIT
meta-checked: "2026-11-09"
github-repo: "jacob-baines/elfparser"
github-stars: 356
github-pushed: "2024-06-04"
github-archived: false
github-issues: 3
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.3
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# elfparser

> Static ELF triage: parses a Linux binary and scores it on observed malware capabilities, with the reasons spelled out.

## What it's for

ELF Parser (jacob-baines) gives a quick first opinion on a suspicious Linux
binary: it statically parses the ELF structures, checks for capability
indicators — network functions, process manipulation, environment-variable
access, shell commands, packing such as UPX, anti-debug tricks, hardcoded
IP addresses and embedded ELF payloads — and outputs a numeric score with
the reasons behind it and the concrete evidence (which imports matched,
where an embedded binary sits). It was written to move ELF malware triage
forward: deciding whether a sample deserves deep reverse engineering, and
where to start. It is a heuristic scorer, not a verdict — the author notes
many hardcoded detections, and ordinary system tools can legitimately
accumulate points for using the same APIs.

## Typical use cases

### Triage a single suspicious ELF

```
elfparser-cli -f suspicious.elf -r -c
```

Outcome: prints the file's score, the scoring reasons (e.g. "Packed",
"Dropper functionality", "Hard coded IPv4 addresses") and the detected
capabilities with their evidence — matched imports like `connect()` or
`execve()`, UPX signatures, embedded ELF file offsets — an immediate read
on how dangerous the sample is and what to look at first.

### Batch-triage a directory of samples

```
elfparser-cli -d /malware/samples -c
```

Outcome: every ELF found in the directory is scored and its capabilities
listed in sequence, giving a quick overview of which samples are packed,
carry droppers or touch the network.

### Dump the parsed ELF structures

```
elfparser-cli -f suspicious.elf -p
```

Outcome: prints the ELF's parsed structures (header, sections, symbols)
as the tool itself interpreted them — useful to spot structural tampering
(e.g. a fake dynamic symbol table) before trusting deeper analysis.

*No corpus evidence — derived from upstream documentation.*
