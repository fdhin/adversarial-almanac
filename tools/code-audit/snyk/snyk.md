---
name: "snyk"
categories:
  - blackarch-code-audit
version: "1.1283.0"
homepage: "https://github.com/snyk/cli"
packaged-by: [blackarch, aur, nixpkgs]
blackarch-builddate: "2024-03-14"
build-system: scripts
license: Apache
meta-checked: "2026-11-09"
github-repo: "snyk/cli"
github-stars: 5701
github-pushed: "2026-11-06"
github-archived: false
github-issues: 141
github-match: "exact"
gh-checked: "2026-11-07"
worth: 8.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 1998
gh-activity-checked: "2026-11-09"
---

# snyk

> Snyk CLI — tests open-source dependencies, source code, container images and infrastructure-as-code for known vulnerabilities, with fix guidance and continuous monitoring.

*No corpus evidence — derived from upstream documentation.*

## What it's for

The Snyk CLI ties a working copy to Snyk's vulnerability intelligence: point it at a project and it reports known vulnerabilities in the dependency tree, each with severity, a link to the full advisory, the path by which the vulnerable module entered, and guidance on fixing it. Beyond open-source scanning it covers static analysis of source (`snyk code test`), container images (`snyk container test`), Terraform and Kubernetes files (`snyk iac test`), and `snyk monitor` snapshots a project so Snyk keeps re-scanning it as new advisories are published. It requires a Snyk account and authenticated machine (a free tier exists), the project's package manager must be installed and on `PATH` (npm, Maven, Gradle and friends), and most open-source projects must be built before testing.

## Typical use cases

### Authenticate the machine

One-time setup before any test.

```
snyk auth
```

Outcome: opens a browser-based login flow that ties this CLI install to your Snyk account; `snyk auth <YOUR_API_TOKEN>` authenticates non-interactively with a token instead. `snyk --help` afterwards confirms the install works.

### Test a project's open-source dependencies

The core dependency scan.

```
cd /path/to/project && snyk test
```

Outcome: reads the manifest in the current directory (`package.json`, `pom.xml`, `composer.lock` and other supported formats), then lists every known-vulnerable dependency with severity, advisory link, dependency path and fix advice. A bare public package name also works as the target — `snyk test ionic` — which doubles as a quick installation check.

### Static-analysis scan of source

Look for issues in first-party code, not just dependencies.

```
snyk code test
```

Outcome: Snyk Code (the SAST product) analyzes the source in the current directory and reports security findings in your own code rather than its dependencies.

### Test a container image

Check a Docker image by reference.

```
snyk container test ubuntu:18.04
```

Outcome: tests the image named by tag (the example is a public base image; local or registry references work) and lists the vulnerabilities it contains with severity and remediation guidance, such as upgrading the base image.

### Test infrastructure as code

Scan deployment definitions for misconfigurations.

```
snyk iac test deploy/kubernetes.yaml
```

Outcome: analyzes the given Kubernetes or Terraform file and reports security misconfigurations and insecure defaults by severity.

### Keep watching for newly disclosed vulnerabilities

Hand the dependency tree to Snyk for continuous checks.

```
snyk monitor
```

Outcome: uploads a snapshot of the current dependency tree; the output includes an "Explore this snapshot at https://app.snyk.io/..." link, and Snyk subsequently alerts you by email as new advisories affect those dependencies. `snyk container monitor` does the same for images.
