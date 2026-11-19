---
name: "codeql"
categories:
  - blackarch-automation
version: "2.22.4"
homepage: "https://github.com/codeql"
packaged-by: [blackarch, aur, nixpkgs]
blackarch-builddate: "2025-08-30"
build-system: scripts
license: custom
meta-checked: "2026-11-09"
github-repo: "github/codeql"
github-stars: 10325
github-pushed: "2026-11-06"
github-archived: false
github-issues: 1523
github-match: "search"
gh-checked: "2026-11-07"
worth: 8.8
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 7522
gh-activity-checked: "2026-11-09"
---

# codeql

> CLI for GitHub CodeQL: build queryable databases from source and run variant-analysis queries against them.

## What it's for

The CodeQL CLI turns a codebase into a relational database and runs QL queries against it — the same engine GitHub's code scanning uses. The core workflow is two commands: `codeql database create` traces a build (or parses sources directly for interpreted languages) to extract every entity into a database, and `codeql database analyze` runs a query pack, suite, directory, or single query file against it, interpreting raw results into alerts/paths written as SARIF, CSV, and other formats. Security researchers use it for variant analysis — write one query for a bug class (or pull one from the standard `codeql/<lang>-queries` packs shipped with the CLI) and sweep every repository of interest. It is analysis-only: it finds and reports issues, it does not exploit them. The database path argument to `create` must not already exist (unless `--overwrite` is given), and its parent directory must.

## Typical use cases

### Create a database for a compiled language

Trace the real build so every compilation unit is extracted.

```
codeql database create db --language=java --command='mvn -B clean package'
```

Outcome: runs the build command under extraction tracing and writes the database to `db/`; subsequent `analyze` runs target that directory. If the project has no build step to trace, `--no-tracing` and the `--build-mode` options adjust extraction.

### Create a database for an interpreted language

Python, JavaScript and friends need no build command — just the source root.

```
codeql database create db --language=python --source-root=./app
```

Outcome: parses the source tree rooted at `./app` into `db/` without invoking any build system.

### Recreate a database over an old one

Iterating on extraction settings without hunting for stale directories.

```
codeql database create db --language=go --command='make' --overwrite
```

Outcome: deletes the existing database at `db/` (which must look like a database) and rebuilds it with the new options, instead of failing on the existing directory.

### Analyze with a standard query pack

Sweep a database with maintained security and quality queries.

```
codeql database analyze db --format=sarif --output=results.sarif codeql/java-queries
```

Outcome: runs the standard Java query pack against `db/` and writes interpreted alert results to `results.sarif` — the format GitHub code scanning ingests; CSV output (`--format=csv --output=results.csv`) gives the same alerts in tabular form.

### Analyze local queries with resource tuning

Run your own query files with explicit CPU/memory bounds.

```
codeql database analyze db --format=csv --output=results.csv --threads=4 --ram=8192 ./queries
```

Outcome: executes every query in the `./queries` directory against the database, capped at 4 threads and 8 GB RAM, and writes the merged results to `results.csv`.

### Create a multi-language database cluster

Build one database per language from a polyglot repository.

```
codeql database create codeql_db --db-cluster --source-root .
```

Outcome: instead of a single database, the path becomes a parent directory holding one sub-database per language detected under the source root (`codeql_db/javascript`, `codeql_db/python`, ...), each of which is passed to `database analyze` individually (`codeql database analyze codeql_db/javascript ...`). Restrict which languages are extracted with a comma-separated list, e.g. `--language "javascript,python"`.

### List installed query packs

Find the pack names `database analyze` accepts.

```
codeql resolve qlpacks
```

Outcome: prints every installed QL pack with its filesystem path — the quick inventory of the `codeql/<lang>-queries` packs available locally before one is referenced by name in an analyze run.

*Command patterns sourced from HackTricks pages (CC BY-NC reference; rewritten); flags verified against the CodeQL CLI manual.*
