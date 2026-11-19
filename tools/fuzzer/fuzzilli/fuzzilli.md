---
name: "fuzzilli"
categories:
  - fuzzer
version: "git (2026-11-02)"
homepage: "https://github.com/googleprojectzero/fuzzilli"
origin: "kitploit"
recipe: "upstream"
packaged-by: []
build-system: make
meta-checked: "2026-11-09"
github-repo: "googleprojectzero/fuzzilli"
github-stars: 2382
github-pushed: "2026-11-02"
github-archived: false
github-match: "exact"
gh-checked: "2026-11-09"
github-commits-year: 721
gh-activity-checked: "2026-11-09"
worth: 8.0
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
license: see repository
github-issues: 75
---

# fuzzilli

> Google Project Zero's coverage-guided fuzzer for dynamic-language interpreters, built on a custom intermediate language.

## What it's for

Fuzzilli (by Samuel Groß) fuzzes JavaScript and other dynamic-language engines, where semantic correctness of generated programs matters: instead of mutating the AST or raw text, it mutates programs written in FuzzIL — an intermediate language over control and data flow — and lifts the result to JavaScript for execution. Samples run in the target engine through REPRL (read-eval-print-reset-loop), an execution mode in which the patched engine executes a script fed over pipes or shared memory and then resets its state, avoiding process-creation overhead. Interesting samples are kept in a corpus by coverage feedback; crashes are minimized automatically. Supported targets (patches and build instructions under `Targets/`) include V8, JavaScriptCore, SpiderMonkey, JerryScript, Duktape, QuickJS, njs, XS, Serenity, and QTJS. Prerequisites: a coverage-instrumented build of the patched engine (clang ≥ 4) and Swift to build the fuzzer itself. It has an extensive public bug record (JSC, SpiderMonkey, V8, JerryScript, Hermes CVEs). It is an engine fuzzer, not a website or HTTP fuzzer.

*No corpus evidence — derived from upstream documentation.*

## Typical use cases

### Start a fuzzing run against a patched engine

Launch the CLI against an instrumented shell built from a target's patch set.

```
swift run -c release FuzzilliCli --profile=spidermonkey /path/to/jsshell
```

Outcome: Fuzzilli loads a minimal corpus, mutates FuzzIL programs, and executes them in the shell via REPRL, printing coverage and crash statistics. Crashing programs are collected (and minimized) so each one can be replayed against the engine directly. The profile name selects the target engine's environment; see `Targets/` for each engine's build instructions.

### List available profiles and CLI options

Inspect the full option surface before a long run.

```
swift run FuzzilliCli --help
```

Outcome: prints every profile plus all fuzzer options (corpus handling, job/instance counts, storage paths, network synchronization) — the authoritative list, since the README only shows the basic invocation. Multiple instances can synchronize corpora over a TCP-based protocol to scale across cores and machines.

### Study how the fuzzer works and extend it

The library entry point doubles as documentation.

```
sed -n '1,120p' Sources/FuzzilliCli/main.swift
```

Outcome: prints the documented usage example of the Fuzzilli library — how the fuzzer instance is assembled from mutators, environment, corpus, and evaluator — the recommended starting point for writing custom mutators or embedding Fuzzilli in another harness.
