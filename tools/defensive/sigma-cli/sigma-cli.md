---
name: "sigma-cli"
categories:
  - defensive
version: "0.7.7-0parrot1"
homepage: "https://github.com/SigmaHQ/sigma-cli"
origin: "parrot"
recipe: "parrot-repo"
packaged-by: [parrot, nixpkgs]
build-system: python
meta-checked: "2026-11-09"
github-repo: "SigmaHQ/sigma-cli"
github-stars: 220
github-pushed: "2026-10-28"
github-archived: false
github-issues: 6
github-match: "exact"
gh-checked: "2026-11-07"
worth: 7.1
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 95
gh-activity-checked: "2026-11-09"
---

# sigma-cli

> pySigma-based command line interface that manages, lists and converts Sigma rules into SIEM query languages.

*No corpus evidence — derived from upstream documentation.*

## What it's for

sigma-cli provides the `sigma` command, built on the pySigma library: it converts Sigma rule YAML into queries for targets such as Splunk, Elasticsearch, Microsoft Defender and many others, and manages the pySigma backend/pipeline plugins that supply those targets. Conversion power comes entirely from installed plugins — the CLI discovers any pySigma backend and processing-pipeline packages present in its Python environment, and `sigma plugin` installs them by name. This is the current-generation tool that supersedes the legacy `sigmac` converter shipped in the [[sigma]] package: same idea, plugin-based architecture, different flags.

## Typical use cases

### Install a conversion backend plugin

Add support for the query language you need.

```
sigma plugin install splunk
sigma plugin list
```

Outcome: installs the pySigma splunk backend into the CLI's Python environment; `sigma plugin list` then shows it among the installed plugins alongside anything else available.

### Discover targets, pipelines and output formats

Find the identifiers the converter accepts.

```
sigma list targets
sigma list pipelines
sigma list formats splunk
```

Outcome: prints the installed backends' target identifiers (for `-t`), their processing pipelines (for `-p`, e.g. `sysmon` for Sysmon field mappings) and each backend's output formats (for `-f`).

### Convert rules into a query language

Bulk-translate a detection directory.

```
sigma convert -t splunk -p sysmon sigma/rules/windows/process_creation
```

Outcome: converts every rule in the directory to a Splunk query, applying the `sysmon` pipeline's field mappings, and prints the queries in order; parse or conversion errors are reported per rule.

### Emit an importable product config

Produce a file you can load straight into the target SIEM.

```
sigma convert -t splunk -f savedsearches -p sysmon -o savedsearches.conf sigma/rules/windows/process_creation
```

Outcome: writes `savedsearches.conf` containing all converted rules as Splunk saved searches (`-f` selects the backend's output format, `-o` the output file).

### One output file per rule

Keep converted rules as individual files for version control or selective deployment.

```
sigma convert -t esql -p ecs_windows --output-dir translated/ rules/
```

Outcome: writes each converted rule as a separate file under `translated/`; `--output-filename-template '{path}/{stem}.esql'` mirrors the source directory structure, and `{index}` handles rules that produce multiple queries.
