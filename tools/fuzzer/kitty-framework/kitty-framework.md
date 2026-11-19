---
name: "kitty-framework"
categories:
  - blackarch-fuzzer
version: "352.cb07609"
homepage: "https://github.com/cisco-sas/kitty"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-25"
build-system: scripts
license: GPL2
meta-checked: "2026-11-09"
github-repo: "cisco-sas/kitty"
github-stars: 436
github-pushed: "2024-08-04"
github-archived: false
github-issues: 11
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.3
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# kitty-framework

> Python fuzzing framework for building your own stateful, monitored fuzzers — data models, target controllers, and a web UI — rather than an out-of-the-box fuzzer.

*No corpus evidence — derived from upstream documentation.*

## What it's for

Kitty (Cisco Systems; inspired by Sulley and Peach) is a modular library for writing custom fuzzers: rich data models for payload generation, multi-stage stateful protocol sessions, and pluggable monitors and controllers for the target. It is aimed at unusual targets — proprietary protocols over non-TCP/IP channels — where off-the-shelf fuzzers do not fit, and it supports both client-side and server-side fuzzing. By design it is not a fuzzer by itself: it ships no protocol implementations, you write a small Python fuzzing script against the library, and shared model/controller implementations live in the companion project Katnip. The package installs the library plus the `kitty-tool` and `kitty-web-client` console commands.

## Typical use cases

### List the templates defined in a fuzzing script

See which payload data models a Kitty script exposes before generating from it.

```
kitty-tool list my_fuzzer.py
```

Outcome: prints every template defined in the Python file; the template names it prints are what you pass to `generate`.

### Generate mutated files from a template

Produce the mutations a data model would send, as plain files.

```
kitty-tool generate -o out my_fuzzer.py MyTemplate
```

Outcome: writes one file per mutation of `MyTemplate` into `out/` (default naming `template.index.bin`); `-c`/`-s` bound how many are produced. Useful for file-format fuzzing and for inspecting what a model generates without running a full session.

### Drive a running fuzzing session over the web interface

Observe and control a fuzzer that exposes Kitty's web UI.

```
kitty-web-client info --host 127.0.0.1 --port 26000
```

Outcome: queries the web server of a running Kitty fuzzer (defaults `localhost:26000`) and prints current session state; `kitty-web-client pause` / `resume` control the session, and `kitty-web-client reports store <folder>` downloads the stored reports.
