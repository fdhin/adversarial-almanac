---
name: "zarn"
categories:
  - blackarch-code-audit
version: "0.0.9.r145.gefccc91"
homepage: "https://github.com/htrgouvea/zarn"
packaged-by: [blackarch, aur]
blackarch-builddate: "2026-11-01"
build-system: perl
license: MIT
meta-checked: "2026-11-09"
github-repo: "htrgouvea/zarn"
github-stars: 59
github-pushed: "2026-10-26"
github-archived: false
github-issues: 3
github-match: "exact"
gh-checked: "2026-11-07"
worth: 6.7
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 62
gh-activity-checked: "2026-11-09"
---

# zarn

> Lightweight SAST for modern Perl: parses each file to an AST, matches risky tokens against a YAML rule file, then runs taint tracking to judge whether user input can actually reach them — with SARIF output for CI.

*No corpus evidence — derived from upstream documentation.*

## What it's for

Auditing Perl code bases — applications and CPAN modules — for the vulnerability classes that show up in this ecosystem: code injection via `eval`/`system`/`exec`/`qx`, path traversal via `open`, weak crypto (`md5`), predictable randomness (`rand`), timing attacks, and hygiene issues like error suppression or leftover debug hooks. Detection is rule-driven and transparent: rules are plain YAML (`id`, `type: presence/absence`, `category`, `message`, and the token list to match), so adding a house rule is a few lines, not a plugin. Analysis is per-file — an AST is built and taint-tracked within each file, but there is no cross-file call graph yet, so entry points in other modules are out of scope. Output is one console line per finding plus an optional SARIF file, which is the intended CI integration (the project ships a GitHub Action that uploads the SARIF to code scanning).

## Typical use cases

### Scan a project with the default rule set

```
zarn -s /srv/app
```

Outcome: every Perl file under the source directory is parsed and checked against `rules/default.yml`; each finding prints as one line prefixed `[vuln]` or `[warn]` with the file path and finding name (e.g. `Potential: Path Traversal`). Running with no `-s` prints the command help.

### Scan with a focused or custom rule file

```
zarn -s /srv/app -r rules/quick-wins.yml
```

Outcome: only the named rule file is loaded — `quick-wins.yml` ships a high-signal subset — and your own YAML rule file (same `id`/`type`/`category`/`sample` schema) drops in the same way for project-specific banned functions.

### Emit SARIF for CI consumption

```
zarn -s /srv/app -srf report.sarif
```

Outcome: findings are additionally written as a SARIF document to `report.sarif`, directly consumable by GitHub code scanning or any SARIF-aware dashboard; pair it with the project's GitHub Action for per-PR scanning.

### Exclude vendored or legacy paths

```
zarn -s /srv/app -i /srv/app/local
```

Outcome: the given file or directory is dropped from file enumeration, so vendored, bundled or intentionally-frozen legacy trees do not flood the findings with noise.
