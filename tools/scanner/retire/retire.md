---
name: "retire"
categories:
  - blackarch-scanner
version: "5.4.3.r20.g71713f8"
homepage: "http://retirejs.github.io/retire.js/"
packaged-by: [blackarch]
blackarch-builddate: "2026-07-05"
build-system: node
license: Apache
meta-checked: "2026-11-09"
github-repo: "RetireJS/retire.js"
github-stars: 4205
github-pushed: "2026-11-09"
github-archived: false
github-issues: 2
github-match: "exact"
gh-checked: "2026-11-09"
worth: 8.3
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 175
gh-activity-checked: "2026-11-09"
---

# retire

> Retire.js command line scanner — detects JavaScript libraries and Node modules with known vulnerabilities inside a web or Node project.

## What it's for

Finds use of known-vulnerable JavaScript library versions, including libraries
that were simply downloaded and committed rather than declared in a package
manifest. The CLI walks a project tree, fingerprints JS files and Node modules
against Retire.js's vulnerability repository, and reports each component with
its known CVEs and advisories — the OWASP "Using Components with Known
Vulnerabilities" check in scanner form. It does not fix anything or scan a
running site by URL (for crawling a live site Retire.js ships a separate headless
site scanner and browser extensions); it inspects files on disk.

## Typical use cases

### Scan the current project

Run it from the root of a web app or Node project.

```
retire
```

Outcome: walks the working directory, prints each identified component with a
known vulnerability together with its version, issue identifiers and severity,
and exits with code 13 when at least one vulnerability is found — the non-zero
exit is the signal a CI job or pre-deploy hook checks.

### Scan a specific folder

Point the scanner at a deployable tree instead of the repository root.

```
retire --path ./public
```

Outcome: same identification and reporting as above, restricted to `./public`;
add `--verbose` to also list every identified file that has no known
vulnerabilities, and `--ext` to widen the scanned extensions beyond the default
`js`.

### Generate a CycloneDX SBOM

Produce a machine-readable component inventory as part of a build pipeline.

```
retire --outputformat cyclonedxJSON1_6 --outputpath sbom.json
```

Outcome: writes a CycloneDX 1.6 JSON SBOM of the detected JavaScript components
to `sbom.json` (the `_VEX` output formats additionally embed a vulnerabilities
section); the file feeds dependency-tracking and SBOM-auditing tooling
directly.

### Fail a build only above a severity threshold

Tune the CI gate so low-severity findings do not block releases.

```
retire --severity high
```

Outcome: the process only exits non-zero (still code 13 by default) when a
detected vulnerability is `high` or worse; `--exitwith 0` disables the failure
exit entirely for report-only runs, and `.retireignore` /
`.retireignore.json` files suppress accepted findings by component, version, or
path.

*No corpus evidence — derived from upstream documentation (RetireJS/retire.js
README and the node scanner's own usage/options listing).*
