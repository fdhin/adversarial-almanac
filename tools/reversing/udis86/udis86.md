---
name: "udis86"
categories:
  - blackarch-reversing
version: "1.7.2"
homepage: "http://udis86.sourceforge.net/"
packaged-by: [blackarch, aur, nixpkgs]
blackarch-builddate: "2026-01-14"
build-system: autotools
license: BSD
meta-checked: "2026-11-09"
github-repo: "vmt/udis86"
github-stars: 1076
github-pushed: "2023-05-10"
github-archived: false
github-issues: 70
github-match: "search"
gh-checked: "2026-11-07"
worth: 3.8
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# udis86

> Minimalist disassembler library (libudis86) for x86 and x86-64, with a small command-line front end called udcli.

*No corpus evidence — derived from upstream documentation.*

## What it's for

Udis86 (Vivek Mohan, 2-clause BSD) decodes streams of raw x86/x86-64 bytes into structured instruction objects from C: a clean `ud_init`/`ud_disassemble` API, Intel (NASM-like, default) or AT&T (GAS-like) syntax, all general-purpose and system instructions plus MMX, FPU/x87, 3DNow, SSE–SSE4.2, AES, AMD-V, Intel-VMX and SMX. It is re-entrant with no dynamic allocation and takes input from files, memory buffers or callback hooks, which makes it a common embedded disassembler engine for debuggers, fuzzers and binary-analysis tools — not an interactive RE suite. `udcli` is the packaged quick-look CLI; the fully documented API ships as texinfo/HTML at the homepage.

## Typical use cases

### Disassemble hex bytes piped on stdin

The upstream README example — quick mnemonic lookup for a handful of opcodes.

```
echo "65 67 89 87 76 65 54 56 78 89 09 00 90" | udcli -32 -x
```

Outcome: one line per decoded instruction (offset, raw hex, Intel-syntax assembly) for the byte sequence interpreted as 32-bit x86; `-64`/`-16` switch mode, `-att` switches to AT&T syntax.

### Disassemble a raw binary blob with a fake load address

Inspect shellcode or a dropped payload without loading it into anything.

```
udcli -64 -att -o 0x401000 -s 0x100 -c 64 ./payload.bin
```

Outcome: decodes 64 bytes (`-c`) starting 0x100 bytes into the file (`-s`) as x86-64 in AT&T syntax, printing instruction addresses as if the blob were mapped at `0x401000` (`-o`); `-noff`/`-nohex` drop the offset/hex columns for clean listing output.

### Embed disassembly in a C program

Link against libudis86 (`-ludis86`) and decode any buffer or stream.

```c
#include <stdio.h>
#include <udis86.h>

int main(void) {
    ud_t u;
    ud_init(&u);
    ud_set_input_file(&u, stdin);
    ud_set_mode(&u, 64);
    ud_set_syntax(&u, UD_SYN_INTEL);
    while (ud_disassemble(&u)) {
        printf("\t%s\n", ud_insn_asm(&u));
    }
    return 0;
}
```

Outcome: compiles with `cc demo.c -ludis86` and prints one Intel-syntax assembly line per instruction read from stdin — the upstream README example loop (`ud_set_input_buffer` is the equivalent for in-memory data).
