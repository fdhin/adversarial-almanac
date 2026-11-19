---
name: "phpstan"
categories:
  - blackarch-code-audit
version: "15555.ba954c395"
homepage: "https://github.com/phpstan/phpstan"
packaged-by: [blackarch, nixpkgs]
blackarch-builddate: "2026-10-31"
build-system: scripts
license: MIT
meta-checked: "2026-11-09"
github-repo: "phpstan/phpstan"
github-stars: 14152
github-pushed: "2026-11-07"
github-archived: false
github-issues: 1098
github-match: "exact"
gh-checked: "2026-11-07"
worth: 8.7
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 2533
gh-activity-checked: "2026-11-09"
---

# phpstan

> Static analysis for PHP that finds bugs by reasoning about types across the whole codebase without running it, with strictness dialed in through rule levels 0-10.

*No corpus evidence — derived from upstream documentation.*

## What it's for

PHPStan catches whole classes of bugs before the code ever executes: wrong argument counts, calls to unknown classes/functions/methods, always-undefined variables, impossible return types. It moves PHP closer to compiled languages in that the correctness of each line can be checked before that line runs. It works best on modern, object-oriented, typehinted code — the more type information you give it, the more it can check — and its checks are cumulative across eleven rule levels, so adoption can start loose and ratchet up. It is a correctness analyzer, not a vulnerability scanner: it reports code that is wrong on types, and security issues surface only insofar as they are type errors, so pair it with dedicated taint and dependency scanners for security coverage. Composer installs the executable as `vendor/bin/phpstan`; distro packages put a `phpstan` binary on `PATH`.

## Typical use cases

### First analysis of a codebase

Run the entry-point analysis on the code you own.

```
vendor/bin/phpstan analyse src tests
```

Outcome: analyzes the given directories at the default rule level 0 (basic checks: unknown classes and functions, wrong argument counts, always-undefined variables) and prints each error it finds with file, line and a description. Only analyze your own code, not `vendor/` — third-party dependencies are discovered through autoloading rather than scanned.

### Raise strictness with a rule level

Turn on stricter checks once the baseline is clean.

```
vendor/bin/phpstan analyse -l 8 src tests
```

Outcome: `-l|--level` selects one of eleven cumulative levels (0 loosest, 10 strictest): level 6 reports missing typehints, level 8 reports calls on nullable types, level 10 is fully strict about `mixed`. `--level max` always tracks the highest level, so the analysis keeps getting stricter with each upgrade — the intended workflow is to start low and increase.
