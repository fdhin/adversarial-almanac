---
name: "fusil"
categories:
  - blackarch-fuzzer
version: "1.5"
homepage: "https://github.com/devdanzin/fusil"
packaged-by: [blackarch]
blackarch-builddate: "2016-12-31"
build-system: scripts
license: GPL2
meta-checked: "2026-11-09"
github-repo: "devdanzin/fusil"
github-stars: 41
github-pushed: "2026-09-06"
github-archived: false
github-issues: 20
github-match: "search"
gh-checked: "2026-11-07"
worth: 7.1
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 362
gh-activity-checked: "2026-11-09"
---

# fusil

> Multi-agent Python fuzzing framework, originally by Victor Stinner, revived to fuzz CPython itself.

## What it's for

Fusil is a Python library for writing fuzzing programs, built as a small multi-agent system: agents communicate via asynchronous messages and a per-session score drives the fuzzer's adaptive aggressivity. Each session generates a standalone test script, runs it under the target interpreter as a sandboxed child process (memory/CPU/process limits, dropped privileges, redirected output), and watches the child for crash signals such as `segmentation fault`, `Fatal Python error`, or AddressSanitizer output. The original framework fuzzed applications like Firefox, PHP, and mplayer; the current revival actively develops and tests only the Python path — CPython itself, C-extension modules, the CPython Tier-2 JIT, and out-of-memory error paths. The legacy network, file-mangling, and X11 subsystems are kept for reference under `notworking/` directories and are not maintained. For network protocol fuzzing, use a dedicated protocol fuzzer instead.

*No corpus evidence — derived from upstream documentation.*

## Typical use cases

### Fuzz a standard-library module

Run the threaded Python fuzzer against one or more CPython stdlib modules for a fixed number of sessions.

```
fusil-python-threaded --unsafe --modules json --sessions 5
```

Outcome: five sessions fuzz the `json` module; each generates standalone test scripts, executes them as resource-limited child processes, and reports any crash signals with the offending script kept for reproduction. `--unsafe` runs children as the current user; without it, fusil drops to a dedicated unprivileged `fusil` user, which is the safe default for long runs.

### Fuzz the CPython JIT

Target the Tier-2 JIT specifically rather than the plain interpreter path.

```
fusil-python-threaded --unsafe --jit-fuzz --sessions 10
```

Outcome: sessions generate JIT-exercising programs and flag crashes or assertion failures in compiled code; the same surface also accepts `--oom-fuzz` / `--oom-seq` to fuzz allocation-failure paths and `--only-c` to restrict fuzzing to C-level operations.

### Run from a checkout without installing

Execute the fuzzer entry point directly out of a source tree.

```
PYTHONPATH=$PWD python fuzzers/fusil-python-threaded --unsafe --sessions 5
```

Outcome: the fuzzer runs without a pip install, using the checkout's `fusil` package — useful for hacking on generators or decay strategies before packaging. The full option list lives in `fusil/python/__init__.py` (`createFuzzerOptions`).
