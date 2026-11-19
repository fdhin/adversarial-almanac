---
name: "gostringsr2"
categories:
  - blackarch-reversing
version: "1.1.2"
homepage: "https://github.com/CarveSystems/gostringsr2"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-12"
build-system: python
license: MIT
meta-checked: "2026-11-09"
github-repo: "CarveSystems/gostringsr2"
github-stars: 55
github-pushed: "2019-09-16"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.3
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# gostringsr2

> Recovers a Go binary's string table with virtual addresses and code references, using radare2.

## What it's for

Go binaries do not store strings as classic NUL-terminated bytes; they live in a packed string table with lengths and pointers from code, so plain `strings` output has no addresses and no link to the functions that use each string. `gostringsr2` drives radare2 through r2pipe to locate the Go string table, resolve the code references into it, and print each string with its virtual address and decoded length — the bridge between a Go sample's messages and the code that handles them. It works on ELF, Mach-O and PE Go binaries for x86 (32/64-bit) and ARM (32/64-bit), and needs radare2 installed to run.

## Typical use cases

### Extract strings with addresses and lengths

```
gostringsr2 -v -n 8 ./example-go-bin
```

Outcome: verbose mode prints file metadata (architecture, OS, stripped status), the string-table range it found, and every recovered string as `0xaddress : [length] : string` — directly citable when annotating a disassembly.

### Quick string listing

```
gostringsr2 -n 4 ./example-go-bin
```

Outcome: prints every recovered string of at least the given length (default 4) from the Go string table — the tool's plain-strings mode.

### Extract UTF-8 strings

```
gostringsr2 -u -n 8 ./example-go-bin
```

Outcome: strings are decoded as UTF-8 instead of ASCII, catching non-ASCII message text that the default mode skips.

### Annotate radare2 with the recovered strings

```
gostringsr2 -s strings.r2 -n 8 ./example-go-bin
```

Outcome: writes an r2 script that adds a string cross-reference (`axs`), a comment (`CCu`) and a `str.go.*` flag at each code location that references a string; load it in radare2 with `. strings.r2` from the session prompt and the Go strings become navigable (flags, comments, `axt` references) in the disassembly.

*No corpus evidence — derived from upstream documentation.*
