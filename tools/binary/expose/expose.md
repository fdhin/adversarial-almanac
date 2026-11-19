---
name: "expose"
categories:
  - blackarch-binary
version: "1110.30264af"
homepage: "https://github.com/ExpoSEJS/ExpoSE"
packaged-by: [blackarch, aur]
blackarch-builddate: "2020-05-06"
build-system: node
license: MIT
meta-checked: "2026-11-09"
github-repo: "ExpoSEJS/ExpoSE"
github-stars: 228
github-pushed: "2025-01-19"
github-archived: false
github-issues: 13
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.9
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# expose

> ExpoSE: dynamic symbolic execution for standalone JavaScript and web pages.

## What it's for

ExpoSE (packaged as `expose`) is a dynamic symbolic execution engine for
JavaScript, developed at Royal Holloway on top of Jalangi2 and the Z3 SMT
solver. It executes a Node.js program — or, experimentally, a website in a
bundled Electron-based browser — under instrumentation, explores feasible
execution paths, and emits concrete input test cases that reach them, along
with per-path output and line coverage. Reach for it when you need inputs that
hit a specific branch in JavaScript code (parsers, handlers, regex logic)
rather than as a general-purpose fuzzer. It pins a specific Node.js era and
sees little development. Note the packaging split: the BlackArch `expose`
wrapper launches the analysis GUI (`expoSE ui`); the CLI invocations below use
the `expoSE` entry point installed in the package directory.

*No corpus evidence — derived from upstream documentation.*

## Typical use cases

### Symbolically execute a Node.js script

```
expoSE ./app.js
```

Outcome: runs the script under symbolic execution and prints an analysis
summary listing each explored path, the concrete input generated for it, and
line coverage; a specific input can be re-run later with the `replay`
subcommand.

### Analyze a website

```
expoSE "https://example.com"
```

Outcome: loads the target site in the bundled Electron browser and
symbolatically executes its client-side scripts. This mode additionally
requires python3 and a recent mitmproxy to function.

### Tune an analysis run

```
EXPOSE_MAX_TIME=600000 EXPOSE_PRINT_COVERAGE=1 expoSE ./app.js
```

Outcome: caps total analysis time at ten minutes and prints the files and
lines explored by the analyzer. Other supported knobs: `EXPOSE_TEST_TIMEOUT`
(per-test-case kill timer), `EXPOSE_PRINT_PATHS` (echo each test case's
output), `EXPOSE_MAX_CONCURRENT` and `EXPOSE_LOG_LEVEL` (0–3). The `ahg`
subcommand can auto-generate a generic test harness for a specified NPM
library.
