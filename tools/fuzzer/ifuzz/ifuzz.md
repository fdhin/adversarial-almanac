---
name: "ifuzz"
categories:
  - blackarch-fuzzer
version: "1.0"
homepage: "https://github.com/tylerdurden2010/ifuzz"
packaged-by: [blackarch]
blackarch-builddate: "2016-07-20"
build-system: make
license: GPL2
meta-checked: "2026-11-09"
github-repo: "tylerdurden2010/ifuzz"
github-stars: 2
github-pushed: "2014-05-07"
github-archived: false
github-issues: 0
github-match: "search"
gh-checked: "2026-11-07"
worth: 1.5
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# ifuzz

> Command-line argument and getopt fuzzer that executes every binary in a directory with mutated argv inputs.

## What it's for

iFuzz (from the fuzzing.org toolset) is a small C fuzzer specialized in argument handling: point it at a directory of target binaries and it executes each one repeatedly with mutated arguments — fuzzing `argv[0]`, `argv[1]`, or incrementally fuzzing single and multiple options, including a getopt-driven mode where an optstring steers the generated argument strings. When a target crashes, iFuzz writes a ready-to-compile C reproducer program into `dumps/`; that directory must already exist in the current working directory, or the tool aborts at startup. It builds with a plain `make` and dates from the mid-2000s/2010s fuzzing.org era.

## Typical use cases

### Fuzz argv[0]

Run targets with mutated program names.

```
mkdir -p dumps && ifuzz 0 ./targets/ -s
```

Outcome: every binary in `./targets/` is executed with fuzzed `argv[0]` values (`-s` silences target stdout/stderr); any crash leaves a C reproducer file in `dumps/`.

### Fuzz argv[1]

Mutate the single first argument.

```
ifuzz 1 ./targets/
```

Outcome: targets run one mutated argument per iteration; progress prints to stdout and crash reproducers accumulate in `dumps/`.

### Fuzz options incrementally

Walk a target's option space one option at a time.

```
ifuzz 2 ./targets/
```

Outcome: each run passes a single incrementally fuzzed option to the target; use fuzz type 3 for multiple options per invocation.

### Fuzz getopt-driven programs

Steer argument generation with a getopt optstring.

```
ifuzz 3 ./targets/ -o "ab:c:" -e 2 -s
```

Outcome: fuzzes argument strings against optstring `ab:c:`; `-e` sets the number of extra non-option arguments, `-f`/`-l` bound the first/last argument position, and crashes produce C reproducers in `dumps/`.

*No corpus evidence — derived from upstream documentation.*
