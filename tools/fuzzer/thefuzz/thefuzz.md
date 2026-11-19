---
name: "thefuzz"
categories:
  - blackarch-fuzzer
version: "160.b4c2c80"
homepage: "https://github.com/droberson/thefuzz"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-26"
build-system: scripts
license: MIT
meta-checked: "2026-11-09"
github-repo: "droberson/thefuzz"
github-stars: 11
github-pushed: "2018-02-14"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 1.9
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# thefuzz

> droberson's Python fuzzer suite for CLI programs — drive a target's arguments and environment variables from a simple @@-substitution script.

## What it's for

thefuzz fuzzes command-line programs: you describe how to invoke the target in a `.fuzz` script where `@@` marks the position replaced in turn by each naughty string, and the harness runs the target repeatedly, one mutation per line. A bundled LD_PRELOAD shim (`ld_preload-get-vars.so`) reveals which flags and environment variables a program actually reads, which is how you author those scripts; glibc's `free()` aborts on heap overflows can be silenced with `MALLOC_CHECK_=0`. The package additionally ships FuzzTCPServer and FuzzTCPClient classes that reuse the same script notation to fuzz network clients and servers (for example a mock IRC server feeding mutated lines to a client). It is an early-stage, Python 2-era project.

## Typical use cases

### Fuzz a program's arguments and environment

```
thefuzz target_binary target.fuzz
```

Outcome: runs the target once per mutation line with each `@@` replaced by the fuzzing strings in turn; watch exit statuses and core dumps for crashes.

### Keep glibc from masking heap overflows

```
MALLOC_CHECK_=0 thefuzz target_binary target.fuzz
```

Outcome: disables glibc's `free()` diagnostic abort so a heap overflow surfaces as a plain segfault attributable to the fuzz input, instead of a libc warning exit.

### Discover what to fuzz first

```
LD_PRELOAD=./ld_preload-get-vars.so target_binary
```

Outcome: prints the target's getopt() string and every getenv() lookup (e.g. `getopt(abc:e:g:G:hi:klno:p:q:rs:T:tuvw:zC)` / `getenv(HOSTALIASES)`), telling you exactly which flags and variables deserve `@@` markers in the fuzz script.

*No corpus evidence — derived from upstream documentation.*
