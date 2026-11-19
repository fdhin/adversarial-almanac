---
name: "radare2-keystone"
categories:
  - blackarch-reversing
version: "941.a2692a2"
homepage: "https://github.com/radare/radare2-extras/tree/master/keystone"
packaged-by: [blackarch]
blackarch-builddate: "2026-10-31"
build-system: autotools
license: GPL3
meta-checked: "2026-11-09"
github-repo: "radare/radare2-extras"
github-stars: 263
github-pushed: "2026-11-04"
github-archived: false
github-issues: 37
github-match: "exact"
gh-checked: "2026-11-07"
worth: 7.0
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 75
gh-activity-checked: "2026-11-09"
---

# radare2-keystone

> Keystone-engine assembler plugins that add `.ks` architectures to radare2 and rasm2.

## What it's for

radare2-keystone packages the Keystone assembler engine as plugins for [[radare2]]. Once installed, the architectures radare2 cannot assemble on its own become available under `<arch>.ks` names — ARM, Hexagon, MIPS, PowerPC, SPARC, SystemZ and x86 — wherever radare2 assembles instructions: the `rasm2` utility and the `asm.arch` selector inside the r2 shell. It is an assembler backend only (disassembly stays with the existing engines), and it earns its place when patching binaries for architectures whose native r2 plugin can disassemble but not emit code. The sibling extras package [[radare2-unicorn]] adds CPU emulation to r2 the same way; the plugins are separate from the main radare2 install.

## Typical use cases

### List the Keystone assembler architectures

Confirm which `.ks` plugins are registered and what bit widths they support.

```
rasm2 -L | grep ks
```

Outcome: filters rasm2's plugin listing to the Keystone engines, showing each `<arch>.ks` name with its 16/32/64-bit support columns — the exact names to pass to `-a` or `asm.arch`.

### Assemble an instruction through the Keystone engine

Emit machine bytes for one instruction from the shell.

```
rasm2 -a x86.ks -b 32 int3
```

Outcome: prints the assembled bytes (`cc` for `int3`); `-b` picks 16, 32 or 64-bit mode within what the plugin supports.

### Patch a binary inside radare2 with Keystone

Use the engine interactively when writing instructions into a file.

```
r2 -w ./target
e asm.arch=x86.ks
wa nop
```

Outcome: opens the target read-write, selects the Keystone x86 engine as the assembler, and `wa nop` writes the assembled bytes at the current seek — the `.ks` names appear among the `asm.arch` choices exactly as in `rasm2 -L`.

*No corpus evidence — derived from upstream documentation.*
