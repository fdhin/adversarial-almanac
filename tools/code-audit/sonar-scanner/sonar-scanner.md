---
name: "sonar-scanner"
categories:
  - blackarch-code-audit
version: "6.2.1.4610"
homepage: "https://docs.sonarqube.org/latest/analysis/scan/sonarscanner/"
packaged-by: [blackarch, aur]
blackarch-builddate: "2024-11-27"
build-system: scripts
license: LGPL3
meta-checked: "2026-11-09"
worth: 0.8
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# sonar-scanner

> SonarScanner CLI — the generic build-time scanner that analyzes a project and pushes the results to a SonarQube Server or SonarQube Cloud instance.

*No corpus evidence — derived from upstream documentation.*

## What it's for

This is SonarQube's catch-all scanner for projects that have no dedicated build-system integration: it reads the project's configuration, analyzes the sources and uploads the report to a SonarQube server, where the quality-gate evaluation and issue browsing actually live. Projects built with Maven, Gradle or .NET should use their dedicated SonarScanners instead — running this CLI there degrades the analysis — and C#/VB.NET projects are not supported by this scanner at all. Project settings come from a `sonar-project.properties` file in the project root (`sonar.projectKey` must be unique on the target server; `sonar.sources`, `sonar.sourceEncoding` and friends tune the scan) or from `-D` flags on the command line; server URL and token can also come from the `SONAR_HOST_URL` and `SONAR_TOKEN` environment variables.

## Typical use cases

### Analyze a configured project and upload the report

Push an analysis to your server.

```
sonar-scanner -Dsonar.token=myAuthenticationToken -Dsonar.host.url=http://sonar.example.com
```

Outcome: picks up `sonar-project.properties` from the current directory, runs the analysis and uploads it to the SonarQube instance at the given URL using the token for authentication; the results then appear as a project snapshot on the server, ready for quality-gate checks.

### Define the analysis entirely on the command line

Skip the properties file for a one-off scan.

```
sonar-scanner -Dsonar.projectKey=corp-webservice -Dsonar.sources=src -Dsonar.sourceEncoding=UTF-8
```

Outcome: `-D,--define` sets any `sonar.*` property inline — the same keys `sonar-project.properties` uses — here establishing the project key (unique per server), the source directory and the encoding without needing a config file.

### Verify the installation

Sanity-check the scanner before wiring it into CI.

```
sonar-scanner -h
```

Outcome: prints the usage block (`usage: sonar-scanner [options]`, including `-D,--define <arg>`), confirming the scanner is on `PATH` and runnable — this is the upstream-recommended post-install check.
