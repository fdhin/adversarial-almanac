---
name: "gdb"
categories:
  - forensics
version: "18.1-1"
homepage: "https://www.gnu.org/software/gdb/"
origin: "arch"
arch-repo: "extra"
packaged-by: [arch, kali, nixpkgs]
arch-builddate: "2026-09-27"
build-system: autotools
meta-checked: "2026-11-09"
github-repo: "gnutools/binutils-gdb"
github-stars: 26
github-pushed: "2026-11-09"
github-archived: false
github-issues: 0
github-match: "search"
gh-checked: "2026-11-09"
worth: 7.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 3740
gh-activity-checked: "2026-11-09"
---

# gdb

> The GNU Debugger — an interactive, scriptable debugger for native binaries and the standard workbench for exploit development and crash analysis on Linux.

## What it's for

gdb runs a program (or attaches to a live process, or loads a core dump) under debugger control: stop it at breakpoints, inspect registers, memory and the stack, and patch state before continuing. In binary exploitation it is the default dynamic-analysis tool — locating crash offsets, verifying corrupted pointers, resolving runtime addresses of functions and strings across the main binary and its shared libraries, and steering control flow by rewriting the instruction pointer. It works interactively, through command files (`-x`), chained one-shot `-ex` invocations, or an embedded Python interpreter; the exploit extensions peda, GEF and pwndbg are gdb add-ons that layer cyclic-pattern generation/offset search and heap visualization on top of the same prompt. It is not a static disassembler or decompiler — it disassembles the live image on demand. Upstream development lives in the sourceware binutils-gdb repository; GitHub: gnutools/binutils-gdb is a read-only mirror of https://sourceware.org/git/binutils-gdb.git (the formerly widely used GitHub mirror bminor/binutils-gdb has been deleted).

## Typical use cases

### Break on main and disassemble a function

Start interactive analysis of a binary at its entry logic.

```
gdb -q ./target
(gdb) set disassembly-flavor intel
(gdb) break main
(gdb) run
(gdb) disassemble main
```

Outcome: `-q` skips the startup banner, the target loads and stops at the first instruction of `main`, and `disassemble` prints the function's instructions with addresses (Intel syntax when set first). Breakpoints also accept raw addresses (`break *0x401234`) and offset expressions (`break *main+314`).

### Run the target with arguments

Pass input arguments to the debuggee without leaving gdb.

```
(gdb) run arg1 arg2 arg3
(gdb) set args arg1 arg2 arg3
```

Outcome: `run` restarts the process with those argv values; `set args` fixes them for every subsequent run. Arguments may embed shell substitutions in backticks, so generated input (long cyclic strings, encoded payloads) can be fed straight onto the command line.

### One-shot analysis from the shell

Answer a single question without an interactive session.

```
gdb -q ./target -ex 'disassemble main'
gdb -q ./target -ex run -ex 'info registers'
```

Outcome: each `-ex` executes one gdb command in order after the binary loads; the second form runs the process and prints register state at the first stop or fault. Add `-batch` to exit after the last command, which makes the invocation scriptable.

### Triage a crash

Find where control landed after a segfault.

```
gdb -q ./target
(gdb) run
(gdb) info registers
(gdb) x/i $pc
```

Outcome: the program runs until it faults; `info registers` dumps every register — a program counter or saved return address pointing into your input reveals the overwrite offset directly — and `x/i $pc` shows the faulting instruction.

### Examine memory at an address

Verify what a pointer or address actually contains.

```
(gdb) x/s 0x6010c0
(gdb) x/x $rcx
(gdb) x/20gx $rsp
```

Outcome: `x` inspects memory at an address with format and size suffixes — `x/s` prints a NUL-terminated string, `x/x $rcx` one hex word at the address held in a register, and `x/20gx $rsp` prints twenty 8-byte giant words of stack upward from the stack pointer.

### Resolve runtime addresses of functions

Find where a function lives in the loaded image.

```
(gdb) info functions encrypt
(gdb) print system
```

Outcome: `info functions` lists matching symbols with addresses from the binary's symbol table; `print system` evaluates the symbol against the loaded shared libraries and prints its resolved address — the value needed to confirm a leaked library base plus offset at runtime.

### Attach to a running process

Debug a service that is already running.

```
gdb -p $(pidof target) -x commands.gdb
```

Outcome: gdb attaches to the PID (long form `--pid=`) and stops the process, then executes the commands in `commands.gdb` — set breakpoints, inspect or write memory (`set {long}0x402000 = 0x9090909090909090`), then `continue` or `detach` from the script. Needs ptrace permission on the target.

### Follow a child across fork

Keep debugging after a daemon forks.

```
gdb ./service -ex 'set follow-fork-mode child' -ex 'break handle_request' -ex 'run'
```

Outcome: by default gdb keeps the parent after a fork; `set follow-fork-mode child` detaches from the parent and follows the forked child so breakpoints apply inside it — the standard setup for network services that fork a handler per connection.

### Redirect execution at a breakpoint

Change control flow by rewriting the instruction pointer.

```
(gdb) break main
(gdb) run
(gdb) set $rip = 0x555555555195
(gdb) continue
```

Outcome: execution stops at `main`, then `set $rip` (`set $eip` on 32-bit) points the instruction pointer at any code address of your choice; `continue` resumes there — a quick way to skip logic or reach a function without crafting input.

### Debug a remote target or kernel

Connect to a gdbserver or emulator stub.

```
gdb ./vmlinux -ex 'target remote :1234' -ex 'continue'
```

Outcome: gdb loads the ELF symbols from `./vmlinux`, attaches to a remote debug stub listening on port 1234 (QEMU's `-s -S` gdbstub, a remote gdbserver, and similar) and continues; breakpoints now trap inside the remote target. Use `target remote HOST:PORT` for network-attached stubs.
