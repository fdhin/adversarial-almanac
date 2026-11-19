---
name: "patchkit"
categories:
  - blackarch-binary
version: "37.95dc699"
homepage: "https://github.com/lunixbochs/patchkit"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-25"
build-system: scripts
license: BSD, custom:unknown
meta-checked: "2026-11-09"
github-repo: "lunixbochs/patchkit"
github-stars: 639
github-pushed: "2023-07-06"
github-archived: false
github-issues: 24
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.4
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# patchkit

> Patches ELF binaries by running small Python patch scripts against them — nopping addresses, injecting assembly or C functions, and hooking entry points.

## What it's for

Use this when you need to modify a compiled ELF binary without recompiling it: disable a check, replace a function, or hook an entry point, for exploit development, CTF-style binary modification, or rapid prototyping of fixes. You write tiny Python files that receive a patch object and apply changes via `pt.patch`, `pt.inject`, `pt.hook`, and `pt.search`; the driver command applies them to the binary. Injection is backed by Keystone (assembly), a C compiler for C snippets, and Unicorn/Capstone for code handling. It targets ELF files; it is not a hex editor for arbitrary formats, and the bundled helper tools (`explore`, `bindiff`) are somewhat x86/CGC-specific.

## Typical use cases

### Apply a directory of patch scripts to a binary

Run every Python patch file in a directory, in alphabetical order, against an ELF binary.

```
patchkit a.out patches/
```

Outcome: each patch script in `patches/` is executed against `a.out` in alphabetical order and the patched binary is written; the command reports the patches applied.

### NOP instructions and hook the entry point

A patch script that stubs out a jump, injects assembly, and diverts the entry point.

```
def simple_patch(pt):
    # nop out a jump at the entry point
    pt.patch(pt.entry, hex='90' * 5)

    # inject assembly into the binary and get its address
    addr = pt.inject(asm='mov eax, 1; ret')

    # hook the entry point to call addr (ret runs the original entry)
    pt.hook(pt.entry, addr)
```

Outcome: when applied, the entry-point jump is replaced with NOPs, the injected `mov eax, 1; ret` stub lives at a fresh `addr` in the binary, and the entry point is hooked so the stub runs first — a minimal skeleton for bypassing an initial check.

### Replace a C function with an injected stub

Swap a library function's body for your own compiled C code.

```
def replace_free(pt):
    # pretend free() is at this address:
    old_free = 0x804fc4

    # inject a function to replace free()
    new_free = pt.inject(c=r'''
    void free_stub(void *addr) {
        printf("stubbed free(%p)\n", addr);
    }
    ''')

    # patch the beginning of free() with a jump to our new function
    pt.patch(old_free, jmp=new_free)
```

Outcome: the injected C stub is compiled and linked into the binary, and the original `free()` at `0x804fc4` is redirected to it, so every call prints a line instead of freeing — a template for instrumenting or disabling arbitrary functions.

*No corpus evidence — derived from upstream documentation.*
