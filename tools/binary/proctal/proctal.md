---
name: "proctal"
categories:
  - blackarch-binary
version: "482.67bf7e8"
homepage: "https://github.com/daniel-araujo/proctal"
packaged-by: [blackarch]
blackarch-builddate: "2019-11-16"
build-system: autotools
license: GPL3
meta-checked: "2026-11-09"
github-repo: "daniel-araujo/proctal"
github-stars: 18
github-pushed: "2026-08-23"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 5.5
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 13
gh-activity-checked: "2026-11-09"
---

# proctal

> Modifies other programs at runtime from Linux: allocate, read, write, search, and watch memory, run injected code, and pause the target — a command-line Cheat Engine backed by a C library.

## What it's for

Use this to poke at a running process without attaching a debugger: reserve memory in it, patch values or text, execute your own assembly inside its context, or search its address space. Every subcommand takes `--pid` (or `--name`) to select the target, and accessing another process's memory normally requires root, so run it as root for anything outside your own processes. Besides the CLI it ships a C API (`proctal_open`, `proctal_write`, ...) for building the same manipulations into your own tools. Assembly/disassembly features need Capstone/Keystone, and the project is currently only tested on x86-64 Linux. The full command set (`read`, `search`, `pattern`, `watch`, `dump`, `measure`, `pause`, `assemble`, `execute`, `allocate`, `deallocate`, `write`) is listed by `proctal -h` and documented in `man 1 proctal`.

## Typical use cases

### Allocate memory in the target

Reserve a writable block inside another process.

```
proctal allocate --pid=1234 -rw 14
```

Outcome: allocates a 14-byte read-write block in PID 1234 and prints its address (e.g. `7F78FDA9C000`) for use with `write` or `execute`.

### Write data to a target address

Store text or typed values at a known address.

```
proctal write --pid=1234 --address=7F78FDA9C000 --type=text 'Hello, world!'
```

Outcome: writes the string into the target's memory at the given address; `--type` also accepts typed binary values so integers can be patched the same way.

### Execute assembly in the target's context

Run your own instructions inside the process.

```
proctal execute --pid=1234
```

Outcome: reads assembly from standard input (for example a `mov rax, 1` / `syscall` write sequence referencing an address you allocated) and executes it inside PID 1234 — input and output happen with the target's file descriptors.

### Free the allocated block

Clean up after injecting.

```
proctal deallocate --pid=1234 7F78FDA9C000
```

Outcome: releases the previously allocated block in the target so nothing leaks once you are done.

### Discover the command surface

List every manipulation available.

```
proctal --help
```

Outcome: prints the list of commands (read, search, pattern, watch, dump, measure, pause, and the ones shown above); `proctal COMMAND -h` and `man 1 proctal` document each command's options.

*No corpus evidence — derived from upstream documentation.*
