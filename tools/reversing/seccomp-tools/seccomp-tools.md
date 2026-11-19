---
name: "seccomp-tools"
categories:
  - blackarch-reversing
version: "1.6.1"
homepage: "https://github.com/david942j/seccomp-tools"
packaged-by: [blackarch, aur]
blackarch-builddate: "2025-09-21"
build-system: scripts
license: MIT
meta-checked: "2026-11-09"
github-repo: "david942j/seccomp-tools"
github-stars: 1149
github-pushed: "2026-11-04"
github-archived: false
github-issues: 2
github-match: "exact"
gh-checked: "2026-11-07"
worth: 7.5
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 78
gh-activity-checked: "2026-11-09"
---

# seccomp-tools

> Dump, disassemble, write, emulate and audit Linux seccomp-BPF syscall filters.

## What it's for

seccomp-tools is a Ruby-based CLI for every angle of seccomp analysis: recovering the BPF filter an executable installs, rendering raw filter bytes as a readable conditional table, assembling hand-written rules back into filter bytes (or ready-to-compile C), emulating a filter against a given syscall invocation, summarizing the effective per-syscall policy, and auditing filters for sandbox escapes. It was built for analyzing seccomp sandboxes in exploitation challenges but works the same on real-world filters, and it understands multiple architectures (aarch64, amd64, i386, riscv64, s390x) — set `-a` when the filter targets a non-host architecture. It is an analysis toolkit only: it never modifies the target's filter.

## Typical use cases

### Dump the seccomp filter of an executable

Recover the BPF program a sandboxed binary installs at startup.

```
seccomp-tools dump ./target
```

Outcome: runs the target under `ptrace`, intercepts its seccomp syscall, and prints the installed filter as a `line CODE JT JF K` disassembly table with syscall names. `-f raw` emits the filter bytes instead (pipe to a file for later `disasm`), `-o file.bpf` writes the output, `-c './target > /dev/null'` launches it via sh, and `-p PID` dumps from an already-running process (needs root). The executable actually executes — run untrusted binaries with care.

### Summarize the policy a filter enforces

See at a glance which syscalls are allowed, killed or errored.

```
seccomp-tools explain ./target
```

Outcome: prints the per-action policy — the syscalls ending in `ALLOW`, `KILL` and `ERRNO(n)`, plus argument constraints (e.g. `open when flags == 0x0`) and per-architecture behavior such as x32-ABI kill guards. Accepts a dumped `.bpf` file or an executable (dumped first, same options as `dump`).

### Audit a filter for escape routes

Find the weaknesses a sandbox reviewer or escape writer cares about.

```
seccomp-tools audit filter.bpf
```

Outcome: scans the filter across supported architectures and reports weaknesses with severities — missing architecture or x32 guards, permissive denylist defaults, equivalent-syscall gaps (e.g. `execve` blocked but `execveat` forgotten), reachable dangerous syscalls, open/read/write chains.

### Disassemble a dumped filter

Turn raw BPF bytes into the readable table — and back.

```
seccomp-tools disasm filter.bpf
```

Outcome: renders the filter file as the conditional disassembly listing; `--asm-able` instead emits the same filter in the text syntax `asm` accepts, enabling disassemble → edit → reassemble round trips.

### Assemble hand-written rules

Write seccomp policy as readable code instead of BPF hex.

```
seccomp-tools asm rules.asm -f c_source
```

Outcome: compiles the rule syntax (jump labels, syscall names, `args[0]` references) into raw BPF; `-f` selects the output form — `raw` bytes, `inspect` string, `c_array`, a complete `c_source` with the `prctl` installer function, or an `assembly` stub — and `-a` targets a foreign architecture.

### Test one syscall against a filter

Check what a specific call would do without running the target.

```
seccomp-tools emu filter.bpf write 0x3
```

Outcome: walks the BPF program for `write(fd=3)`, printing the executed path and the verdict (e.g. `return ALLOW at line 0009`); further positional arguments pass `arg0`..`arg5`, and `-q` prints only the result.

*No corpus evidence — derived from upstream documentation.*
