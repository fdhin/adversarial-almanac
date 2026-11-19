---
name: "slither"
categories:
  - blackarch-code-audit
version: "4851.f9710ded1"
homepage: "https://github.com/crytic/slither"
packaged-by: [blackarch, aur]
blackarch-builddate: "2026-01-13"
build-system: python
license: AGPL3
meta-checked: "2026-11-09"
github-repo: "crytic/slither"
github-stars: 6411
github-pushed: "2026-11-04"
github-archived: false
github-issues: 515
github-match: "exact"
gh-checked: "2026-11-07"
worth: 8.5
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 326
gh-activity-checked: "2026-11-09"
---

# slither

> Static analysis framework for Solidity (and Vyper) smart contracts — dozens of built-in vulnerability detectors plus structural printers, built on the compiler's AST.

*No corpus evidence — derived from upstream documentation.*

## What it's for

Slither compiles a contract project through its compilation layer (crytic-compile, which drives Hardhat, Foundry, Truffle, Dapp, Brownie or plain `solc`) and then runs detectors for the recurring smart-contract bug classes: reentrancy, dangerous `delegatecall` and proxy-upgrade pitfalls, uninitialized state variables, variable shadowing, suicidal contracts and more, each finding carrying severity, confidence and source references. It is purely static — it never executes a contract or touches a chain. The hard requirement is that the code must compile: Slither works from the solc AST, so if the project's own compile command fails, Slither cannot run. Beyond detectors, "printers" surface structure (inheritance and call graphs, function summaries), the sibling utilities (`slither-check-erc`, `slither-flat`, `slither-read-storage`, `slither-interface`, `slither-check-upgradeability`) automate review tasks, and a Python API supports custom analyses.

## Typical use cases

### Audit an entire project

Run the full detector suite over a buildable project.

```
slither .
```

Outcome: from the project root (the parent of `contracts/`, with `node_modules/` alongside), compiles via the detected framework and runs every detector, printing findings grouped by detector with severity, impact, confidence and the exact source locations. Use project mode whenever the code has dependencies; `--detect`/`--exclude` select or drop specific detectors and `--exclude-informational` trims noise.

### Analyze a dependency-free file

Check one self-contained contract.

```
slither contracts/token.sol
```

Outcome: compiles and analyzes the single file directly. This only works when the file imports no dependencies — a contract with imports fails AST generation and must be scanned in project mode instead.

### Export results as JSON

Get a diffable, tool-consumable report.

```
slither . --json results.json
```

Outcome: writes all detector findings to `results.json`; passing `-` as the filename streams the JSON to stdout. Useful for keeping audit artifacts alongside a repo or feeding findings into other tooling.

### Map contract structure with printers

Understand an unfamiliar codebase before reading findings.

```
slither . --print human-summary,inheritance-graph
```

Outcome: runs the requested printers (comma-separated list) instead of only detectors — here a human-readable contract summary plus the inheritance graph exported as `.dot` files; `call-graph`, `function-summary` and `entry-points` are other useful choices for review preparation.
