---
name: "binflow"
categories:
  - blackarch-binary
version: "5.7fb02a9"
homepage: "https://github.com/elfmaster/binflow"
packaged-by: [blackarch]
blackarch-builddate: "2018-11-18"
build-system: make
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "elfmaster/binflow"
github-stars: 111
github-pushed: "2018-11-14"
github-archived: false
github-issues: 6
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.5
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# binflow

> Breakpoint-based tracer for ELF binaries that logs function calls with resolved arguments, much faster than single-stepping tracers.

## What it's for

binflow (by Ryan O'Neill, elfmaster) is a rework of his ftrace utility tracer: instead of single-stepping every instruction under ptrace it plants breakpoints at function entries, which makes tracing much faster and lets it resolve and print arguments — strings, pointers, integers — at both PLT (library) and local call sites. It can launch a binary or attach to an already-running PID, optionally follow threads (-t), and log control flow at every branch (-c). Set expectations honestly: the author describes it as a roughly 30%-complete prototype (return values are not yet resolved), so treat it as a quick-look tracer rather than a hardened ltrace replacement.

*No corpus evidence — derived from upstream documentation.*

## Typical use cases

### Trace library and local calls with argument resolution

```
binflow -s -b ./target
```

Outcome: executes ./target under the tracer and prints one line per call in the form `PLT_call@0x4006f3: strncpy(<stack address>, "hello", 0x8)` or `LOCAL_call@0x400725: f(0x3, 0x2, 0x1)`, with -s enabling string argument display.

### Log every branch (control-flow tracing)

```
binflow -sc -b ./target
```

Outcome: adds control-flow analysis (-c) so every branch instruction taken is logged alongside the call trace — use it to follow which paths through the target actually execute, not just which functions are entered.

### Attach to an already-running process

```
binflow -fe -p 1234
```

Outcome: attaches to PID 1234 and traces it live, using .eh_frame exception-handling data for function identification (-f) and printing extended ELF metadata about the target image (-e) before the call trace begins.
