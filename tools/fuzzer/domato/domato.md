---
name: "domato"
categories:
  - blackarch-fuzzer
version: "133.fadff39"
homepage: "https://github.com/googleprojectzero/domato"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-12"
build-system: scripts
license: Apache
meta-checked: "2026-11-09"
github-repo: "googleprojectzero/domato"
github-stars: 1786
github-pushed: "2024-11-26"
github-archived: false
github-issues: 3
github-match: "exact"
gh-checked: "2026-11-07"
worth: 4.3
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# domato

> A generation-based DOM/HTML/CSS/JS fuzzer by Ivan Fratric (Google Project Zero).

## What it's for

Domato generates HTML documents that stress browser DOM, layout and script
engines. A context-free grammar engine (`grammar.py`) expands the shipped
`html.txt`, `css.txt` and `js.txt` grammar files into documents full of
deeply nested elements, unusual CSS and JavaScript hammering DOM interfaces —
the input class behind many renderer memory-corruption finds. The generator
only produces files; detecting bugs requires feeding the output to a target
browser or engine, in practice under a crash-catching harness (sanitizer
build, debugger). The grammar engine itself is application-agnostic and can
drive generators for formats that have nothing to do with the DOM.

## Typical use cases

### Generate a single sample

```
domato-generator --file sample.html
```

Outcome: writes one generated HTML document to `sample.html` (upstream
equivalent from a checkout: `python3 generator.py --file sample.html`); open
it in the engine under test to see how it copes.

### Generate a sample from a custom template

```
domato-generator --file sample.html --template mytemplate.html
```

Outcome: the generated fuzzed DOM is embedded into your template document
instead of the default skeleton — useful for aiming the generated content at
a specific page structure or harness page.

### Generate a large batch efficiently

```
domato-generator --output_dir samples/ --no_of_files 1000
```

Outcome: writes 1000 documents named `fuzz-00001.html`, `fuzz-00002.html`,
... into `samples/`; batching is faster than looping `--file` because the
grammar files are loaded and parsed only once for the whole run.

### Use the grammar engine for your own format

```python
from grammar import Grammar

my_grammar = Grammar()
my_grammar.parse_from_file('input_file.txt')
result_string = my_grammar.generate_symbol('symbol_name')
```

Outcome: `generate_symbol` returns one expansion of the named symbol from
your grammar file, so Domato's engine can drive a generator for any
text-based format when run from a checkout where `grammar.py` is importable.

*No corpus evidence — derived from upstream documentation.*
