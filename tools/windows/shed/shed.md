---
name: "shed"
categories:
  - blackarch-windows
version: "2.0.0"
homepage: "https://github.com/enkomio/shed"
packaged-by: [blackarch, aur]
blackarch-builddate: "2019-02-09"
build-system: windows
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "enkomio/shed"
github-stars: 274
github-pushed: "2019-01-29"
github-archived: false
github-issues: 2
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.8
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# shed

> .NET runtime inspector that attaches to a managed process and dumps its heap, strings and loaded modules.

## What it's for

Shed (by Antonio Parata, @s4tan) is a command-line tool for inspecting the .NET runtime state of a program. It attaches to an existing .NET process, or launches a binary and suspends it, then extracts the objects stored on the managed heap, the strings held in memory and all modules loaded in memory, saving the results for post-processing. Its intended use is malware triage: running a suspicious .NET sample under Shed gives a first general overview of the information the program keeps in memory without having to read its (often obfuscated) code. It can also inject a .NET assembly into a remote managed or unmanaged process and invoke a chosen method inside it. It is not a decompiler or a debugger — it observes runtime state, not code logic.

## Typical use cases

### Inspect the managed heap of a running process

```
shed.exe --pid 2356
```

Outcome: Shed attaches to the target process, dumps heap objects, strings and loaded modules, and prints where the results were written (e.g. `[+] Result saved to C:\Shed\Result\7800`) for offline inspection.

### Launch a suspicious .NET binary and capture its state

```
shed.exe --timeout 2000 --exe sample.exe
```

Outcome: the binary is executed and suspended after 2000 ms (giving it time to initialize its properties), then its heap and modules are dumped to a result directory — a quick triage snapshot of what the sample built in memory.

### Dump only one artifact type

```
shed.exe --pid 2356 --dump-modules
```

Outcome: only the in-memory modules are dumped (`--dump-heap` is the counterpart for heap objects only); heap output can additionally be filtered by placing `blacklist.txt` and `whitelist.txt` type-prefix files next to the binary.

### Inject a .NET assembly into a remote process

```
shed.exe --pid 1234 --exe InjectedAssembly.dll --inject
```

Outcome: the assembly is injected into the target process and activated; adding `--method` with a full method name (e.g. `this.is.my.namespace.class.method`) invokes that specific static method in the injected assembly.

*No corpus evidence — derived from upstream documentation.*
