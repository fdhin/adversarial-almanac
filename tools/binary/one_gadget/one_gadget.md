---
name: "one_gadget"
categories:
  - binary
homepage: "https://github.com/david942j/one_gadget"
origin: "hacktricks"
recipe: "upstream"
packaged-by: [arch]
build-system: ruby
license: MIT
meta-checked: "2026-11-13"
github-repo: "david942j/one_gadget"
github-match: "exact"
github-stars: 2366
gh-checked: "2026-11-13"
worth: 8.1
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-13"
---

# one_gadget

> Finds the magic offsets in a libc that jump straight to `execve("/bin/sh", ...)` — with the exact register constraints each one needs.

## What it's for

OneGadget answers the standard pwn question "where in this libc can I jump
to get a shell?" without hand-reading disassembly. It walks the
control-flow graph backward from `execve`/`posix_spawn` calls, symbolically
executing the code around each candidate so it can print not just the
offset but the constraints that must hold (`[r15] == NULL`, `rsp & 0xf ==
0`, ...) for the call to survive — and the shipped gadget database is
verified by actually running each gadget in a live process. Give it a libc
file or just a BuildID. It covers i386, amd64, aarch64, arm, riscv64 and
MIPS. Its output feeds ret2libc/ROP exploits where you control RIP and
stack alignment but not much else; when no gadget's constraints are
satisfiable you fall back to a full ROP chain.

*No corpus evidence — derived from upstream documentation.

## Typical use cases

### Find gadgets in a libc file

Point it at the target's libc.

```
one_gadget ./libc.so.6
```

Outcome: prints each magic offset (e.g. `0xe3b31 execve("/bin/sh", r15,
rdx)`) with its constraint list, best-probability gadgets first — pick the
one whose constraints your register/stack state already satisfies at the
moment RIP is taken over.

### Look up gadgets by BuildID

Work from just the leaked build ID.

```
one_gadget -b b417c0ba7cc5cf06d1d1bed6652cedb9253c60d0
```

Outcome: answers from the built-in database (every supported Ubuntu LTS
glibc) or fetches the matching libc from the project's gadget database —
no libc file needed on disk; `--info <BuildID>` identifies the libc
version instead.

### Show more candidate gadgets

When the easy gadgets' constraints don't fit.

```
one_gadget --level 1 ./libc.so.6
```

Outcome: level 1 lists every gadget not strictly harder than another
(one line each, with constraints); level 2 (`--level 2`) dumps everything
found including addresses reachable under multiple constraint sets —
level 0 (default) shows only the few most likely to work.

### Find gadgets near a GOT entry

Enable partial-overwrite attacks when libc's base is unknown.

```
one_gadget ./libc.so.6 --near exit,mkdir --raw
```

Outcome: orders gadgets by distance from the named functions (regular
expressions like `'write.*'` work, and passing an ELF via `--near
vuln.elf` uses every GOT function in it) and prints bare offsets — the
closest gadget to a GOT entry you can partially overwrite needs the fewest
nibbles guessed.

### Try every gadget against your exploit

Let the tool brute-force the offset choice.

```
one_gadget -s exploit.sh ./libc.so.6
```

Outcome: runs `exploit.sh <offset>` once per candidate gadget with the
offset substituted, so a driver script (connect, hijack control flow,
check for a shell) sweeps all possibilities automatically; pair with
`--base 0x7f0000000000` when your script wants absolute addresses.
