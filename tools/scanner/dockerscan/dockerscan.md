---
name: "dockerscan"
categories:
  - blackarch-scanner
version: "latest.r110.ga34f6d5"
homepage: "https://github.com/cr0hn/dockerscan"
packaged-by: [blackarch]
blackarch-builddate: "2026-10-31"
build-system: go
license: BSD-3-Clause
meta-checked: "2026-11-09"
github-repo: "cr0hn/dockerscan"
github-stars: 1728
github-pushed: "2026-11-07"
github-archived: false
github-issues: 6
github-match: "exact"
gh-checked: "2026-11-07"
worth: 8.1
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 419
gh-activity-checked: "2026-11-09"
---

# dockerscan

> Single-binary Docker image scanner that runs CIS-benchmark, secrets, supply-chain, CVE and runtime-hardening checks against an image and emits JSON/SARIF with CI-friendly exit codes.

*No corpus evidence — derived from upstream documentation.*

## What it's for

Auditing Docker images — local or in a registry — before they ship. DockerScan v2, rewritten in Go from the earlier Python tool, pulls the image you name and runs five scanning modules over it: CIS Docker Benchmark compliance controls, embedded secrets (cloud keys, VCS tokens, AI-service API keys), supply-chain attack indicators (cryptominers, backdoored libraries, imageless malicious containers), CVE matching against a local database built from MITRE cvelistV5, and runtime hardening findings (dangerous Linux capabilities, missing seccomp/AppArmor). Findings land in JSON and SARIF reports, and the process exit code (0 clean / 1 high / 2 critical) makes it usable as a pipeline gate. The offensive modules of the Python v1 (image trojanization, registry manipulation, network discovery) are not part of the v2 command set — this is currently a defensive scanner.

## Typical use cases

### Run the full scan over an image

```
dockerscan myapp:latest
```

Outcome: pulls the image if needed, runs all five scanner modules, prints a findings summary and writes JSON and SARIF reports to the current directory; the exit code reflects the worst severity found, so a CI step can fail the build on it.

### Scan only selected modules

```
dockerscan --scanners secrets,supplychain redis:7
```

Outcome: restricts the run to the named scanners (valid: `cis`, `secrets`, `supplychain`, `vulnerabilities`, `runtime`) — a quick secrets/supply-chain pass that skips the CVE database entirely.

### Refresh the CVE database

```
dockerscan update-db
```

Outcome: downloads or updates the local SQLite CVE database used by the vulnerability scanner; run it on a new host (or periodically) before relying on `--scanners vulnerabilities`.

### Collect reports for an audit

```
dockerscan -q --output /var/reports postgres:14
```

Outcome: suppresses the banner (`-q`) and writes the JSON/SARIF reports under `/var/reports` instead of the working directory; `--only-critical` is the companion flag for trimming findings to the high/critical subset.

### Scan an image in a private registry

```
DOCKER_USERNAME=user DOCKER_PASSWORD=TOKEN dockerscan ghcr.io/example/private-app:v1.0
```

Outcome: authenticates to the registry with the supplied environment variables (or with credentials already present in `~/.docker/config.json`), pulls the private image and scans it exactly like a public one.
