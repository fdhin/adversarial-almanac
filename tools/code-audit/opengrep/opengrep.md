---
name: "opengrep"
categories:
  - code-audit
version: "1.16.0"
homepage: "https://opengrep.dev"
origin: "kitploit"
recipe: "upstream"
packaged-by: [aur]
build-system: ocaml
license: LGPL-2.1
meta-checked: "2026-11-09"
github-repo: "opengrep/opengrep"
github-stars: 3314
github-pushed: "2026-11-10"
github-archived: false
github-issues: 92
github-match: "exact"
gh-checked: "2026-11-09"
worth: 8.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 1583
gh-activity-checked: "2026-11-09"
---

# opengrep

> Open-source static-analysis engine for semantic code search and taint tracking — the LGPL fork of Semgrep with the commercial-licence features kept open.

*No corpus evidence — derived from upstream documentation.*

## What it's for

Finding security-relevant code patterns across large codebases fast: you write rules as small YAML pattern definitions (or reuse existing Semgrep rules and rulesets unchanged), and `opengrep scan` reports every semantic match with file, line and rule message. Forked from Semgrep v1.100.0 by an AppSec consortium when Semgrep moved features behind a commercial licence; since the fork it has added stronger intrafile taint analysis and language coverage beyond Semgrep CE (Visual Basic, Apex, Elixir). It is a code-audit and CI tool — it reads source, it does not run or exploit anything. See also [[semgrep]], the upstream it stays rule-compatible with.

## Typical use cases

### Scan a codebase with a custom rule

Write `rules/demo-rust-unwrap.yaml`:

```yml
rules:
- id: unwrapped-result
  pattern: $VAR.unwrap()
  message: "Unwrap detected - potential panic risk"
  languages: [rust]
  severity: WARNING
```

```
opengrep scan -f rules code/rust
```

Outcome: every semantic match of the pattern is printed with its rule id, message and the exact source line (`9┆ let result = divide(10, 0).unwrap();`), plus a scan summary counting findings per rule — the artifact you triage findings from.

### Emit SARIF for CI and code-scanning platforms

```
opengrep scan --sarif-output=sarif.json -f rules code
```

Outcome: `sarif.json` is written in SARIF 2.1.0 format (rule metadata, precise start/end line/column regions, stable match fingerprints) — drop it into GitHub code scanning or any SARIF-consuming gate.

### Trace intra-file taint flows

```
opengrep scan --taint-intrafile -f taint-rules code
```

Outcome: with a taint-mode ruleset, sources-to-sinks are tracked within files — constructor and field assignments, inter-method flows and higher-order functions (map/filter/reduce) across the 12 supported taint languages — surfacing tainted-data paths a pure pattern match cannot see.

### Run existing Semgrep rules unchanged

```
opengrep scan -f semgrep-rules/ code
```

Outcome: a directory of rule files written for [[semgrep]] parses and fires as-is (the engines are rule-compatible) — migration is a binary swap, with findings reported in the same shapes (text table, JSON, SARIF).
