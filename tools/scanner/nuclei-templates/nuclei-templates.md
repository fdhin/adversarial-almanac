---
name: "nuclei-templates"
categories:
  - blackarch-scanner
version: "v10.5.1.r86.gf579019"
homepage: "https://github.com/projectdiscovery/nuclei-templates"
packaged-by: [blackarch, aur, nixpkgs]
blackarch-builddate: "2026-10-31"
build-system: scripts
license: MIT
meta-checked: "2026-11-09"
github-repo: "projectdiscovery/nuclei-templates"
github-stars: 13300
github-pushed: "2026-11-07"
github-archived: false
github-issues: 133
github-match: "exact"
gh-checked: "2026-11-07"
worth: 8.8
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 16111
gh-activity-checked: "2026-11-09"
---

# nuclei-templates

> The community-maintained YAML template library that powers the nuclei scanner — CVEs, exposed panels, misconfigurations, exposures and workflows.

## What it's for

This repository is the data half of the [[nuclei]] engine: thousands of community-reviewed detection templates organized by directory (`http/cves/`, `exposed-panels/`, `misconfiguration/`, `vulnerabilities/`, `network/`, `workflows/`, `dns/`, `token-spray/`, and more). The templates are plain YAML — each defines requests to send, matchers to detect a condition, metadata (tags, severity, references) — so you can read any check to see exactly what it matches on, tune it, or copy it as the starting point for your own. Distro packages install the tree under `/usr/share/nuclei-templates`, and the engine keeps it current with `nuclei -ut`. Upstream also publishes `TEMPLATES-STATS.md`/`TEMPLATES-STATS.json` with per-tag/author/directory/severity counts for integration.

## Typical use cases

### Inventory the installed template set

See what a scan would actually run without scanning.

```
nuclei -tl -tags cve
```

Outcome: lists every installed template matching the current filters (ID, name, severity, tags) — a quick pre-flight check that the library you expect is present and current.

### Run a subtree of the library

Point the engine at a specific template directory.

```
nuclei -u https://example.com -t /usr/share/nuclei-templates/http/cves/
```

Outcome: only the templates in that directory load, which keeps scans fast and focused when you only care about one class of checks (see [[nuclei]] for the engine's full flag set).

### Search the library for a product or CVE

Find every check that mentions a target technology.

```
grep -ril drupal /usr/share/nuclei-templates/http/cves/
ls /usr/share/nuclei-templates/exposed-panels/ | grep -i gitlab
```

Outcome: file paths of every template referencing the product, so you can run exactly those checks, review their matchers, or cite the detection in a report.

### Adapt and validate a template

Copy a bundled check, tweak it, and check your edit.

```
cp /usr/share/nuclei-templates/http/cves/2023/CVE-2023-XXXXX.yaml ./my-check.yaml
nuclei -t my-check.yaml -validate
```

Outcome: `-validate` parses the YAML and reports syntax, matcher or extractor errors without touching a target — the standard loop for authoring custom checks.

### Keep the library current

```
nuclei -ut
nuclei -ud /opt/nuclei-templates
```

Outcome: `-ut` pulls the latest template release into the configured directory; `-ud` sets a custom install/update directory for the library (useful for pinned, air-gapped, or per-project copies).

*No corpus evidence — derived from upstream documentation (README of projectdiscovery/nuclei-templates; install path from the BlackArch PKGBUILD, which packages the tree to `/usr/share/nuclei-templates`).*
