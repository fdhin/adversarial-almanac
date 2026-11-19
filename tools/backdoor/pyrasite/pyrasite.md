---
name: "pyrasite"
categories:
  - blackarch-backdoor
version: "2.0"
homepage: "https://pypi.org/project/pyrasite/#files"
packaged-by: [blackarch]
blackarch-builddate: "2022-08-29"
build-system: python
license: GPL3
meta-checked: "2026-11-09"
github-repo: "lmacken/pyrasite"
github-stars: 2876
github-pushed: "2025-04-07"
github-archived: false
github-issues: 46
github-match: "search"
gh-checked: "2026-11-07"
worth: 4.4
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# pyrasite

> Injects and executes arbitrary Python code inside a running Python process by attaching gdb to it.

## What it's for

pyrasite runs code inside an already-running Python process — to inspect live state, dump thread stacks, force garbage collection, profile object memory, or hot-patch a misbehaving service — without restarting it and losing that state. Mechanically it attaches gdb (7.3+ required) to the target PID and drives the interpreter, and it crosses interpreter versions: you can run pyrasite under Python 3 and inject into a 2.x process, or vice versa. Three entry points ship: the `pyrasite` injector (takes a payload file path or a named built-in payload), `pyrasite-shell` for an interactive REPL executing in the target, and `pyrasite-memory-viewer` for object-size profiling. You need the same privileges as the target process, and the process must be attachable. It is a debugging/introspection tool first — the "code injection" framing is the same mechanism viewed offensively.

## Typical use cases

### Run a payload file inside a live process

```
pyrasite --output localterm 12345 dump_stacks.py
```

Outcome: dump_stacks.py executes inside PID 12345; `--output localterm` pipes whatever the payload prints back to your terminal, while the default `procstreams` sends it to the target process's own stdout/stderr.

### Open a REPL inside the target

```
pyrasite-shell 12345
```

Outcome: a Python prompt whose statements run inside PID 12345 — read globals, call functions, mutate live objects — with no restart of the target.

### Discover and run a built-in payload

```
pyrasite --list-payloads
pyrasite 12345 <payload-name>
```

Outcome: the first command names the payloads bundled in `pyrasite/payloads` (thread-stack dumps, loaded-module listing, forced garbage collection, object memory statistics, reverse shells); the second runs one of them in the target without writing a file.

### Profile what is eating memory

```
pyrasite-memory-viewer 12345
```

Outcome: reports the largest objects held by the target process, pointing at the allocation responsible for memory growth.

*No corpus evidence — derived from upstream documentation.*
