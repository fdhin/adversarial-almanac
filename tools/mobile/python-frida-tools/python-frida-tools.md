---
name: "python-frida-tools"
categories:
  - blackarch-mobile
version: "14.11.0"
homepage: "https://pypi.org/project/frida-tools/#files"
packaged-by: [blackarch, nixpkgs]
blackarch-builddate: "2026-10-04"
build-system: python
license: custom:unknown
aliases: ["python2-frida-tools"]
meta-checked: "2026-11-09"
worth: 0.8
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# python-frida-tools

> Host-side CLI suite for Frida: the `frida` REPL, `frida-trace`, `frida-ps` and friends.

## What it's for

frida-tools is the pip/PyPI package that puts Frida's user-facing command
lines on the analysis host. It ships 17 console scripts — among them `frida`
(an interactive REPL that injects JavaScript into a target process), `frida-ps`
(list processes and apps), `frida-trace` (function tracing that generates
stub handlers), `frida-ls-devices`, `frida-kill`, `frida-ls`/`frida-rm`,
`frida-pull`/`frida-push` (device file transfer), `frida-discover`,
`frida-strace`/`frida-itrace`, `frida-join`, `frida-create`, `frida-compile`,
`frida-pm` and `frida-apk`. The tools talk to `frida-server` running on a USB
or remote device; the server itself is a separate artifact that must already
be on the target. Front-ends like [[frida-ios-dump]] and objection are built
on this suite. It drives instrumentation — it is not a static analyzer.

## Typical use cases

### Attach the REPL to a spawned app over USB

Inject an instrumentation script into an Android app at startup.

```
frida -U -f com.example.app -l hook.js
```

Outcome: spawns `com.example.app` on the USB device, loads `hook.js`, drops
into the interactive REPL (`%resume` continues past the spawn gate, `q` quits);
`-e 'CODE'` evaluates snippets without a script file, and `-H` replaces the
USB channel with a remote `frida-server` host.

### List apps on a device

Enumerate what can be instrumented before attaching.

```
frida-ps -Uai
```

Outcome: prints the applications running on the USB device; `-a` restricts the
list to applications (not bare processes), `-i` includes all installed
applications (running or not), `-j` emits JSON instead of a table.
`frida-ls-devices` lists the devices themselves.

### Trace native function calls

Watch calls to matching libc functions as they happen.

```
frida-trace -U -f com.example.app -i "open*" -i "getaddrinfo"
```

Outcome: generates `__handlers__/` stub scripts on the host, spawns the app,
and prints each matching call with arguments and backtrace; `-x` excludes
patterns, `-I`/`-X` filter whole modules, `-m`/`-M` cover Objective-C methods
and `-j`/`-J` Java methods (e.g. `frida-trace -U -f com.example.app -j
'com.example.Crypto!*'`).

### Push and pull files from a device

Move artifacts between host and instrumented device.

```
frida-pull -U /data/local/tmp/dump.bin dump.bin
```

Outcome: transfers the remote file to the host over the Frida channel;
`frida-push` works in the opposite direction, and `frida-ls -U` /
`frida-rm -U` list and delete device files without a separate shell on the
target.

*No corpus evidence — derived from upstream documentation.*
