---
name: "grammarinator"
categories:
  - blackarch-fuzzer
version: "512.3a6a745"
homepage: "https://github.com/renatahodovan/grammarinator"
packaged-by: [blackarch]
blackarch-builddate: "2026-10-31"
build-system: python
license: MIT
meta-checked: "2026-11-09"
github-repo: "renatahodovan/grammarinator"
github-stars: 437
github-pushed: "2026-11-06"
github-archived: false
github-issues: 14
github-match: "exact"
gh-checked: "2026-11-07"
worth: 7.1
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 83
gh-activity-checked: "2026-11-09"
---

# grammarinator

> ANTLR v4 grammar-based test generator and fuzzer with Python and C++ backends.

## What it's for

Grammarinator (Renata Hodovan and Akos Kiss) generates random test cases from an ANTLR v4 grammar, so the large public collection of existing grammars can be reused as fuzzing specifications. Besides generating from scratch, it can parse existing inputs into derivation trees and then mutate or recombine them, and its C++ backend integrates with libFuzzer and AFL++ for coverage-guided, grammar-aware fuzzing (including afl-tmin minimization). Grammars encode syntax only — semantic rules need custom generator subclasses or transformers. Three commands cover the workflow: `grammarinator-process` (grammar to generator), `grammarinator-generate` (produce tests), `grammarinator-parse` (prepare seed populations).

## Typical use cases

### Build a generator from grammars

Turn ANTLR v4 grammar files into a test generator module.

```
grammarinator-process examples/grammars/HTMLLexer.g4 examples/grammars/HTMLParser.g4 -o examples/fuzzer/ --no-actions
```

Outcome: writes a Python generator (e.g. `HTMLGenerator.py`) into `examples/fuzzer/`; `--no-actions` strips inlined parser actions and conditions that usually prevent generation when reusing parser grammars.

### Generate test cases

Produce a batch of syntactically valid inputs from the generator.

```
grammarinator-generate HTMLCustomGenerator.HTMLCustomGenerator -r htmlDocument -d 20 -o examples/tests/test_%d.html -n 100 -s HTMLGenerator.html_space_serializer --sys-path examples/fuzzer/
```

Outcome: creates 100 HTML files (`test_0.html` ... `test_99.html`), each a parse tree of the grammar bounded by derivation depth 20; `-s` applies a serializer to join tokens (a built-in `grammarinator.runtime.simple_space_serializer` ships with the tool).

### Evolve existing inputs by mutation and recombination

Prepare a population of parsed seeds, then let the generator mutate and recombine them.

```
grammarinator-parse -g grammar/JSON.g4 -r json -o corpus/ seeds/*.json
grammarinator-generate JSONGenerator.JSONGenerator -r json -d 10 -o corpus/tests/test_%d.json -n 50 --population corpus/
```

Outcome: the first command stores derivation trees (`.grt` files) of the seed inputs in `corpus/`; with `--population` set, each new test case is produced by generation, mutation or recombination chosen at random (disable unwanted strategies with `--no-generate`, `--no-mutate`, `--no-recombine`).

### Use the C++ backend

Generate and build a high-performance standalone generator.

```
grammarinator-process examples/grammars/HTMLLexer.g4 examples/grammars/HTMLParser.g4 -o examples/fuzzer/ --no-actions --language hpp
python3 grammarinator-cxx/dev/build.py --clean --generator HTMLGenerator --serializer HTMLSpaceSerializer --includedir examples/fuzzer/ --generate
```

Outcome: produces a compiled generator under `grammarinator-cxx/build/Release/bin/` that takes the same `-r`/`-d`/`-o`/`-n` options; the C++ backend can also serve as a libFuzzer or AFL++ custom mutator.

*No corpus evidence — derived from upstream documentation.*
