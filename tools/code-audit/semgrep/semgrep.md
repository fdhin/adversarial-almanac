---
name: "semgrep"
categories:
  - blackarch-code-audit
version: "1.164.0"
homepage: "https://pypi.org/project/semgrep/"
packaged-by: [blackarch, nixpkgs]
blackarch-builddate: "2026-10-31"
build-system: scripts
license: LGPL-2.0-only
meta-checked: "2026-11-09"
github-repo: "semgrep/semgrep"
github-stars: 17536
github-pushed: "2026-11-06"
github-archived: false
github-issues: 980
github-match: "search"
gh-checked: "2026-11-07"
worth: 8.8
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 1030
gh-activity-checked: "2026-11-09"
---

# semgrep

> Rule-driven static analysis that matches code the way it is written — fast source scans in many languages, backed by a large public ruleset registry.

*No corpus evidence — derived from upstream documentation.*

## What it's for

Semgrep finds bugs and vulnerabilities by matching syntax-aware patterns against source code: a rule looks like the code it targets — no abstract syntax trees or regex wrestling — and rules can also express taint flows from source to sink. It runs locally, covers most languages, and ships with a registry of thousands of community rules for security, correctness and best-practice enforcement, which makes it a default reach for auditing unfamiliar source. The open-source engine covers ad-hoc scanning; `semgrep login`/`semgrep ci` optionally connect it to the Semgrep AppSec Platform for Pro rules, supply-chain and secrets products. It is a code scanner, not a dependency (SCA) or runtime (DAST) tool.

## Typical use cases

### Quick pattern search without a rule file

Hunt a bug pattern interactively.

```
semgrep -e '$X == $X' --lang=py src/
```

Outcome: every Python self-comparison (a classic typo bug) is printed with its file, line and the matched snippet. `$X` metavariables match any expression, and `--lang` pins the language, so one-liners like this cover a large share of day-to-day source audits without writing any YAML.

### Scan with a registry ruleset

Pull in curated rules by name.

```
semgrep scan --config "p/default" .
```

Outcome: fetches the named ruleset from the Semgrep registry (the first use downloads it), scans the working tree, and reports findings grouped by rule with severity plus a summary. Any registry ruleset ID or a local rule file path works in place of `p/default`.

### Emit findings for CI tooling

Produce machine-readable artifacts.

```
semgrep ci --json --json-output=semgrep.json
```

Outcome: runs the project's CI scan configuration and writes every finding to `semgrep.json` for downstream tooling; `--sarif --sarif-output=semgrep.sarif` emits SARIF instead for code-scanning integrations such as GitHub security views.
