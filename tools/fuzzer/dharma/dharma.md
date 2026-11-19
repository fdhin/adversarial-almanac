---
name: "dharma"
categories:
  - blackarch-fuzzer
version: "98.6b1e511"
homepage: "https://github.com/MozillaSecurity/dharma"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-12"
build-system: python
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "MozillaSecurity/dharma"
github-stars: 492
github-pushed: "2021-08-13"
github-archived: true
github-issues: 10
github-match: "exact"
gh-checked: "2026-11-07"
worth: 0.6
worth-basis: "historical (archived/gone upstream)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# dharma

> A generation-based, context-free grammar fuzzer from Mozilla Security.

## What it's for

Dharma generates test cases from grammar files instead of mutating existing
input: you describe a language's structure — browser API call sequences,
canvas operations, WebAssembly type combinations — in Dharma's `.dg` grammar
syntax, and it emits well-formed but hostile test cases that stress parsers
and scripting engines; its classic use is browser-engine fuzzing. Cases can
go to stdout, to disk, or over a built-in WebSocket server that feeds a
browser harness a fresh case on every request. Grammar files such as
`canvas2d.dg` ship in `dharma/grammars/` (paths below are relative to a
checkout or install of the repository), and the same engine accepts any
custom grammar you write. Mozilla's repository is archived; development
continues in the posidron/dharma fork.

## Typical use cases

### Generate one test case to stdout

```
dharma -grammars dharma/grammars/canvas2d.dg
```

Outcome: prints a single generated test case built from the canvas2d grammar;
multiple grammar files can be appended to the `-grammars` argument.

### Generate a batch of test cases to disk

```
dharma -grammars dharma/grammars/canvas2d.dg -storage ./tests -count 1000
```

Outcome: generates 1000 test cases into the `./tests` directory, one file per
case, ready to feed to a fuzzing harness in bulk.

### Serve generated cases to a browser harness

```
dharma -grammars dharma/grammars/canvas2d.dg -server -template dharma/grammars/var/templates/html5/default.html
```

Outcome: Dharma starts its WebSocket server; launching the template's
`index.html` in a browser connects the harness, which then keeps requesting
fresh test cases for as long as the server runs.

### Benchmark the generator

```
time dharma -grammars dharma/grammars/canvas2d.dg -count 10000 > /dev/null
```

Outcome: times the generation of 10,000 cases, giving a
test-cases-per-second figure to sanity-check how much input volume a campaign
can push through a slow target.

*No corpus evidence — derived from upstream documentation.*
