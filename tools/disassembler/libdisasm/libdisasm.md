---
name: "libdisasm"
categories:
  - blackarch-disassembler
version: "0.23"
homepage: "http://bastard.sourceforge.net/libdisasm.html"
packaged-by: [blackarch, aur]
blackarch-builddate: "2025-11-30"
build-system: autotools
license: custom:Artistic
meta-checked: "2026-11-09"
github-repo: "pasis/libdisasm"
github-stars: 4
github-pushed: "2012-10-05"
github-archived: false
github-issues: 0
github-match: "search"
gh-checked: "2026-11-07"
worth: 1.8
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# libdisasm

> Classic x86 disassembler library from the SourceForge "bastard" project, with the x86dis command-line front end.

## What it's for

libdisasm is a small C library that decodes IA-32 instruction bytes into instruction objects a program can walk; its public API lives in `libdisasm/libdis.h` (installed under `$prefix/include`), and Perl bindings ship in the source tree. The 0.23 release is the last word from the original project and is of mostly historical interest — new code should generally reach for Capstone instead. The package also builds `x86dis`, a CLI that disassembles arbitrary bytes from a file or STDIN at chosen offsets and can emit Intel, AT&T, native, XML or raw syntaxes. It decodes fixed-position byte ranges; it does not parse executable containers (no ELF/PE headers — you compute file offsets yourself).

## Typical use cases

### Disassemble a raw stream from STDIN

Decode a blob of instruction bytes piped in.

```
echo '55 89 e5 83 EC 08' | perl -ane 'foreach(@F){print pack("C",hex);}' | x86dis -e 0 -s intel
```

Outcome: prints each decoded instruction in Intel syntax with its offset into the stream — the tool's own help-file example for hex-pasted bytes.

### Disassemble a file range or single instruction

Point at a byte range inside a file.

```
x86dis -f sample.bin -r 0x100 32 -s att
```

Outcome: decodes 32 bytes starting at file offset 0x100 in AT&T syntax; `-a 0x100` decodes only the single instruction at that offset, and `-e 0x100` decodes forward to end of file.

### Machine-readable output

Emit disassembly for downstream tooling.

```
x86dis -f sample.bin -e 0 -s xml -o out.xml
```

Outcome: writes the full listing in XML syntax to `out.xml` (`-o` redirects output; `-s raw` prints just instruction bytes per line; `-d` adds a syntax-description header).

### Decode 16-bit code

Switch the decoder to real-mode encodings.

```
x86dis -L -e 0 -f bootsect.bin
```

Outcome: decodes using legacy 16-bit instruction encodings (`-L`), as needed for boot sectors and DOS-era binaries; `-N` additionally skips long runs of NULL padding.

*No corpus evidence — derived from upstream documentation.*
