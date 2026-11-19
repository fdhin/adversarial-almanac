---
name: "objdump2shellcode"
categories:
  - blackarch-binary
version: "28.c2d6120"
homepage: "https://github.com/wetw0rk/objdump2shellcode"
packaged-by: [blackarch]
blackarch-builddate: "2017-10-02"
meta-checked: "2026-11-09"
github-repo: "wetw0rk/objdump2shellcode"
github-stars: 875
github-pushed: "2026-07-05"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 7.3
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 25
gh-activity-checked: "2026-11-09"
---

# objdump2shellcode

> Payload development kit (now "Sickle"): extract, format, disassemble, diff, and execute shellcode.

## What it's for

This project by wetw0rk began as `objdump2shellcode`, a script that pulled
opcodes out of a compiled binary and formatted them as shellcode for a
target language; it has since evolved into Sickle (command `sickle-pdk`), a
modular payload development kit. It formats raw bytecode for many wrappers
(C, C#, Python, PowerShell, Perl, Rust, JavaScript, NASM, ...), linearly
disassembles raw shellcode via Capstone, assembles new shellcode via the
Keystone engine, identifies bad characters and pinpoints the instructions
that contain them, diffs two blobs at the opcode and assembly level, and
wraps bytecode in a runner for quick testing on Linux and Windows. Beyond
formatting your own bytecode it ships ready-made payload modules (reverse
shells, egghunters, reflective loaders) for x86, x64, and aarch64. The
legacy interface — still what the 2017-era BlackArch package installed —
used `-d`/`-rd` to extract from a binary, `-s` for stdin, `-f` to pick a
format, `-b` to flag bad characters, and `-e` to examine shellcode; those
roles map onto today's `-r`, `-f`, and `-b` options.

## Typical use cases

### List payloads, modules, and output formats

```
sickle-pdk -l
```

Outcome: tabular inventory of shellcode payloads (with platform and ring),
development modules (`disassemble`, `format`, `run`, `diff`, `pinpoint`,
`badchar`, `asm_shell`, `handler`), architectures, and output formats —
the value sets accepted by `-p`, `-m`, `-a`, and `-f`.

### Extract and format shellcode for a target language

```
sickle-pdk -r shellcode.bin -f c
```

Outcome: reads the raw bytecode file and emits it as a paste-ready C array;
any use of `-f` activates the format module (`-f python`, `-f powershell`,
`-f rust`, ... for the other wrappers).

### Execute bytecode for a quick test

```
sickle-pdk -m run -r shellcode.bin
```

Outcome: the `run` module wraps and executes the bytecode under the current
operating system and architecture — immediate validation without
hand-writing a test harness.

### Generate a bundled payload with arguments

```
sickle-pdk -p windows/x64/egghunter TAG=w00t
```

Outcome: renders the egghunter payload module with egg `w00t` in the
default format; payload arguments are passed inline as `KEY=value` after
the module name.

### Read a module's or payload's usage card

```
sickle-pdk -m run -i
```

Outcome: prints name, tested platforms, argument list, description, and an
example invocation for that module (`-p windows/x64/egghunter -i` does the
same for a payload).

*No corpus evidence — derived from upstream documentation.*
