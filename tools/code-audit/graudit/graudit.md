---
name: "graudit"
categories:
  - blackarch-code-audit
version: "738.3317cc2"
homepage: "https://github.com/wireghoul/graudit"
packaged-by: [blackarch, aur]
blackarch-builddate: "2026-10-25"
build-system: autotools
license: GPL
meta-checked: "2026-11-09"
github-repo: "wireghoul/graudit"
github-stars: 1690
github-pushed: "2026-10-13"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 7.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 56
gh-activity-checked: "2026-11-09"
---

# graudit

> Rough source-code auditing with grep: per-language signature databases flag potentially dangerous lines with minimal setup — the lightweight sibling of RATS and flawfinder.

*No corpus evidence — derived from upstream documentation.*

## What it's for

graudit is a small shell script plus signature sets that runs POSIX extended-regular-expression rules over a source tree using grep. It is deliberately low-tech: no parsing, no data-flow analysis, no taint tracking — pattern matching that points a reviewer at lines worth reading, with false positives expected as the price for speed and zero dependencies. It ships with databases for roughly 25 targets (php, java, c, python, js, ruby, go, android, ios, asp, sql, xss, secrets, ...) and every database is a plain regex file you can extend or replace. Reach for it when a full SAST platform is overkill and you want an immediate, greppable list of suspicious sinks.

## Typical use cases

### Quick default audit of a source tree

```
graudit /path/to/source
```

Outcome: runs the default signature database over every file in the tree and prints each match with file name, line number, one line of context and terminal colorization — pipe through `less -R` or redirect to a file for review.

### Target a language-specific database

```
graudit -d php ./src
```

Outcome: scans only with the `php` signature set (swap in `c`, `java`, `python`, `js`, `sql`, `xss`, `secrets`, ...), cutting noise from languages you don't care about. `graudit -l` lists every database visible in the search paths, in precedence order.

### Exclude generated files and widen context

```
graudit -d java -x '*.min.js,*.map' -c 3 ./src
```

Outcome: skips files matching the comma-separated exclusion list and shows three lines of context around each match instead of the default one — useful when judging whether a flagged sink is actually reachable from untrusted input.

### Scan with your own signature file

```
graudit -d /path/to/rules.db ./src
```

Outcome: a full path passed to `-d` loads that file as the signature database instead of a named one, so teams can maintain private rule sets; `-` or `/dev/stdin` reads rules from a pipe, and the `GRDIR` environment variable relocates the whole signature directory.

### Case-insensitive sweep

```
graudit -i -d sql ./src
```

Outcome: matches the `sql` signatures case-insensitively — worth a run on legacy code where the same dangerous call appears under different letter casings.
