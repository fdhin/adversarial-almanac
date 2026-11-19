---
name: "bfuzz"
categories:
  - blackarch-fuzzer
version: "60.fdaefc0"
homepage: "https://github.com/RootUp/BFuzz"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-25"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "RootUp/BFuzz"
github-stars: 318
github-pushed: "2022-12-05"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# bfuzz

> Browser fuzzer that loops fresh Chrome/Firefox instances over domato-generated HTML test cases — generation-based replay, no mutation.

## What it's for

BFuzz (RootUp) automates input-based browser fuzzing: bundled Google Project Zero domato grammars generate a corpus of HTML test cases into the `recurve/` folder, and BFuzz repeatedly opens a new browser instance on a random case, lets it render for about 12 seconds, closes it, waits 3 seconds and repeats, logging each case to the terminal. It explicitly does not mangle or mutate test cases itself — all structure comes from domato's HTML/CSS/JS grammars, so refreshing the corpus means regenerating it. Use it as a cheap unattended crash-hunting loop for Chrome and Firefox; a testcase of its lineage was credited to CVE-2018-11396 (Epiphany). There are no CLI flags: after generation it prompts interactively for the browser to drive.

## Typical use cases

### Generate a fresh test-case corpus

Regenerate the domato cases the replay loop will use.

```
./generate.sh
```

Outcome: runs the bundled domato generator and fills `recurve/` with new grammar-derived HTML test cases.

### Run the replay loop against a browser

Start the fuzzing loop.

```
python BFuzz.py
```

Outcome: prompts for the browser (1: Chrome, 2: Firefox), then loops — new instance, random test case from `recurve/`, ~12 s run, close, 3 s pause, repeat — with the current case printed to the terminal, so the last case shown before a browser crash is the one to save.

*No corpus evidence — derived from upstream documentation.*
