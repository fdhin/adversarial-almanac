---
name: "wcc"
categories:
  - blackarch-binary
version: "v0.0.11.r34.gdced7e9"
homepage: "https://github.com/endrazine/wcc"
packaged-by: [blackarch, aur, nixpkgs]
blackarch-builddate: "2025-09-20"
build-system: rust
license: MIT, BSD-2-Clause, custom:unknown
meta-checked: "2026-11-09"
github-repo: "endrazine/wcc"
github-stars: 2015
github-pushed: "2026-11-06"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 7.0
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 13
gh-activity-checked: "2026-11-09"
---

# wcc

> The Witchcraft Compiler Collection: reworks ELF binaries into libraries you can load, script and call like native APIs.

## What it's for

WCC is a set of binary-manipulation tools for ELF (and, via libbfd, PE/COFF) executables. `wcc` "unlinks" an executable into a relocatable object file as if it had come out of a compiler; `wld` transforms an executable into a shared library; `wsh` loads such binaries into its own address space and exposes their symbols to an embedded Lua interpreter ("Punk-C"), so any function inside a binary — symbols resolved at runtime — becomes directly callable. This lets you invoke hidden routines, reuse a stripped binary's code (crypto, parsers) from scripts, or analyze an opaque binary interactively. Rebuilding relocations is currently supported only for Intel x86_64 ELF binaries; other formats can be transformed with limits.

*No corpus evidence — derived from upstream documentation.*

## Typical use cases

### Unlink an executable into a relinkable object file

```
wcc -c /bin/ls -o /tmp/ls.o
gcc /tmp/ls.o -o /tmp/ls.so -shared
```

Outcome: creates /tmp/ls.o, a relocatable object reproducing the original's mappings, then links it into a shared library that can be loaded by wsh or dlopen'd.

### Turn an executable into a shared library in place

```
cp /bin/ls /tmp/ls.so
wld -libify /tmp/ls.so
```

Outcome: rewrites /tmp/ls.so's ELF headers so it can be loaded as a shared library; `-noinit` drops constructors/destructors from the output.

### Call functions inside a binary interactively

```
wsh /usr/sbin/apache2
> a = ap_get_server_banner()
> print(a)
Apache/2.4.7
```

Outcome: loads apache2 into the wsh process and makes its symbols Lua-callable; here the binary's own function returns its banner string. `help` inside the interpreter lists introspection functions (`symbols`, `grep`, `hexdump`, `libcall`, ...).

### Generate C headers from a binary

```
wcch /usr/sbin/apache2 > /tmp/apache2.h
```

Outcome: prints declarations for all the binary's exported functions and global variables as a C header, ready to include when compiling code that links against it.

### Recover the gcc link flags for a binary

```
wldd /bin/ls
```

Outcome: prints the `-l` flags needed to relink the binary (e.g. `-lselinux -lacl -lc ...`), reversing what the linker originally consumed.
