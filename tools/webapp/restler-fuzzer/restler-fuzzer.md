---
name: "restler-fuzzer"
categories:
  - blackarch-webapp
version: "419.1f1d59c"
homepage: "https://github.com/microsoft/restler-fuzzer"
packaged-by: [blackarch]
blackarch-builddate: "2025-02-17"
build-system: scripts
license: MIT
meta-checked: "2026-11-09"
github-repo: "microsoft/restler-fuzzer"
github-stars: 2967
github-pushed: "2026-06-10"
github-archived: false
github-issues: 300
github-match: "exact"
gh-checked: "2026-11-07"
worth: 5.8
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 1
gh-activity-checked: "2026-11-09"
---

# restler-fuzzer

> Stateful REST API fuzzer that drives a live service from its OpenAPI/Swagger specification.

## What it's for

RESTler (from Microsoft Research) is the first stateful REST API fuzzer:
instead of replaying canned requests it compiles an OpenAPI/Swagger
definition into a grammar of requests, infers producer-consumer
dependencies between responses and later requests (e.g. an id returned by
POST used by GET), and then explores the API breadth-first with mutated
values to surface security and reliability bugs — 404/500s, authz gaps,
resource leaks. Workflow: `compile` the spec into a grammar, `test` as a
smoke test, then `fuzz-lean` (one pass per endpoint) or full `fuzz` with a
time budget. It targets services you own or are authorized to test; the
full fuzz mode is aggressive and can destabilize a poorly implemented
backend. It is not a web-UI fuzzer and does nothing without a spec.

## Typical use cases

### Compile an OpenAPI spec into a fuzzing grammar

Turn the API definition into the artifacts RESTler needs.

```
restler-fuzzer compile --api_spec swagger.json
```

Outcome: generates `grammar.py`, a dictionary skeleton and config files in a
`Compile` sub-directory; fill the dictionary with realistic values (ids,
names) before fuzzing so dependency chains can resolve.

### Quick bug pass over every endpoint

Fuzz-lean exercises each endpoint+method once with default checkers.

```
restler-fuzzer fuzz-lean --grammar_file Compile/grammar.py --dictionary_file Compile/dict.json
```

Outcome: sends one fuzzed request per endpoint and writes bug reports and
coverage files under `FuzzLean/RestlerResults/`; check the bug logs for
500s and error leaks first.

### Deep stateful fuzzing with a time budget

Full fuzz mode explores producer-consumer sequences in breadth-first order.

```
restler-fuzzer fuzz --grammar_file Compile/grammar.py --dictionary_file Compile/dict.json --time_budget 2
```

Outcome: runs the smarter (deeper) search for 2 hours, appending new bug
reports and per-endpoint coverage under `Fuzz/RestlerResults/`; add
`--token_refresh_command '<auth command>' --token_refresh_interval 60` when
the API requires auth tokens that expire.

*No corpus evidence — derived from upstream documentation (microsoft/restler-fuzzer README and docs/user-guide: Compiling.md, Fuzzing.md, QuickStart.md; packaged as `/usr/bin/restler-fuzzer` → engine/restler.py).*
