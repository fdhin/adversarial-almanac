---
name: "local-php-security-checker"
categories:
  - blackarch-code-audit
version: "v2.1.3.r1.g1d1fdac"
homepage: "https://github.com/fabpot/local-php-security-checker"
packaged-by: [blackarch, aur]
blackarch-builddate: "2024-08-04"
build-system: go
license: AGPL3
meta-checked: "2026-11-09"
github-repo: "fabpot/local-php-security-checker"
github-stars: 1177
github-pushed: "2024-08-02"
github-archived: true
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 0.8
worth-basis: "historical (archived/gone upstream)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# local-php-security-checker

> Standalone Go binary that checks a Composer lock file against the FriendsOfPHP security-advisories database — no PHP, no Composer, no project dependencies required.

*No corpus evidence — derived from upstream documentation (upstream repository is archived; its README now points to `composer audit` as the successor).*

## What it's for

A single static binary that reads a PHP project's `composer.lock` and reports which packages have known security advisories, using the FriendsOfPHP/security-advisories database that it downloads and caches on first use. "Local" is the point: after the database is cached it needs neither PHP nor Composer nor network access, which made it a common choice on build servers. Exit codes double as CI signals: 1 means at least one vulnerability was found, 2 an invalid format option, 127 a missing lock file or unloadable database. The upstream repository is archived and its README now recommends `composer audit` instead, so this tool is mainly useful on legacy CI where Composer itself is not available.

## Typical use cases

### Check the project in the current directory

```
local-php-security-checker
```

Outcome: locates `composer.lock` in the current working directory, refreshes the advisory database if it changed since the last run, and prints each vulnerable dependency with its advisories; the exit code (1 on findings) is a ready-made CI condition.

### Check a specific project or lock file

```
local-php-security-checker --path=/path/to/php/project
```

Outcome: runs the same audit against another directory — or point `--path` directly at a `composer.lock` file — so one binary on a CI runner can sweep many projects.

### Emit machine-readable output

```
local-php-security-checker --format=json
```

Outcome: prints findings as JSON instead of terminal-styled output; `ansi`, `markdown`, `junit` and `yaml` are the other supported formats for whichever downstream tool consumes the results.

### Ignore development dependencies

```
local-php-security-checker --no-dev
```

Outcome: skips packages listed under `require-dev`, restricting the audit to dependencies that would ship to production.

### Run against the cached database only

```
local-php-security-checker --local
```

Outcome: skips the HTTP round-trip that checks for a database update and uses the cached copy as-is — the offline/locked-down-build pattern. Run `local-php-security-checker --update-cache` separately beforehand to refresh the cache deliberately.
