---
name: "dependency-check"
categories:
  - blackarch-code-audit
version: "10.0.2"
homepage: "https://dependency-check.github.io/DependencyCheck"
packaged-by: [blackarch]
blackarch-builddate: "2024-07-25"
build-system: scripts
license: APACHE
meta-checked: "2026-11-09"
github-repo: "dependency-check/DependencyCheck"
github-stars: 7765
github-pushed: "2026-11-09"
github-archived: false
github-issues: 196
github-match: "exact"
gh-checked: "2026-11-09"
worth: 8.5
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 444
gh-activity-checked: "2026-11-09"
---

# dependency-check

> OWASP Dependency-Check — software composition analysis that inventories a project's dependencies, maps them to CPE identifiers and reports the CVEs published for them.

*No corpus evidence — derived from upstream documentation.*

## What it's for

Dependency-Check is an SCA (software composition analysis) tool: it takes the third-party libraries a project depends on, determines whether each has a Common Platform Enumeration (CPE) identifier, and generates a report linking to the associated CVE entries. It is not a source-code analyzer — it judges dependencies, not your code. Since 9.0.0 the vulnerability data comes from the NVD API, and an NVD API key is strongly recommended: without one the database update is extremely slow. Reports come in HTML, XML, CSV, JSON, JUNIT, SARIF, JENKINS and GITLAB formats. Ships as a CLI (`dependency-check.sh`, `dependency-check.bat` on Windows) plus Maven, Gradle and Ant integrations; Java 11 or higher is required for 11.0.0+.

## Typical use cases

### Scan a project's dependencies

```
dependency-check.sh --project "My App" --scan ./src --out ./reports
```

Outcome: inventories dependencies under `./src`, updates the local NVD database, and writes an HTML report under `./reports` listing each dependency with its CPE matches and linked CVEs. The first run downloads a large portion of the NVD; later runs are incremental. On Windows the entry point is `dependency-check.bat`.

### Speed up the NVD sync with an API key

```
dependency-check.sh --project "My App" --scan ./src --nvdApiKey "$NVD_API_KEY"
```

Outcome: authenticates NVD database downloads against a key obtained from nvd.nist.gov/developers, avoiding the unauthenticated throttle that makes updates extremely slow — in CI, share one key with a caching strategy to stay under the NVD rate limit.

### Produce machine-readable reports

```
dependency-check.sh --project "My App" --scan ./src --format SARIF --out ./reports
```

Outcome: writes the report as SARIF for code-scanning tooling; JSON, XML, CSV, JUNIT, JENKINS, GITLAB and HTML are the other format values, and when a single format is selected `--out` may name the report file directly.

### Suppress verified false positives

```
dependency-check.sh --project "My App" --scan ./src --suppression ./suppressions.xml
```

Outcome: findings matched by the suppression XML are dropped from the report — the mechanism for dependencies you have verified are not the CPE the analyzer guessed. The flag can be repeated to layer multiple suppression files.

### Gate a build on vulnerability severity

```
dependency-check.sh --project "My App" --scan ./src --failOnCVSS 7
```

Outcome: the process exits non-zero when any finding has a CVSS score of 7 or higher, turning the scan into a CI pass/fail gate; without it the run exits 0 even with CVEs in the report.

### Scan against a pre-built database

```
dependency-check.sh --project "My App" --scan ./src --noupdate --out ./reports
```

Outcome: skips the automatic NVD, hosted-suppressions and RetireJS data updates and analyzes with whatever database is already on disk — the pattern for CI runners that pre-bake or share the data directory. `--purge` is the opposite: it deletes the local NVD copy to force a full re-download.
