---
name: "horusec"
categories:
  - blackarch-code-audit
version: "v2.9.0.beta.2.r1.g873d4104"
homepage: "https://github.com/ZupIT/horusec"
packaged-by: [blackarch]
blackarch-builddate: "2023-08-19"
build-system: go
license: Apache
meta-checked: "2026-11-09"
github-repo: "ZupIT/horusec"
github-stars: 1344
github-pushed: "2026-11-06"
github-archived: false
github-issues: 113
github-match: "exact"
gh-checked: "2026-11-07"
worth: 4.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# horusec

> SAST orchestrator that fans one command out to a battery of underlying security tools across ~18 languages, merging findings and hunting leaked secrets — including in git history.

*No corpus evidence — derived from upstream documentation.*

## What it's for

Horusec (Zup's open-source project) is not a single analysis engine: it orchestrates around 20 third-party security tools, each in its own Docker container, over a project and merges everything into one report. Supported languages include C#, Java, Kotlin, Python, Ruby, Go, Terraform, JavaScript, TypeScript, Kubernetes manifests, PHP, C, HTML, JSON, Dart, Elixir, Shell and Nginx. Beyond code it searches the project's files and git history for leaked secrets. Docker is the recommended runtime; running with Docker disabled loses much of the analysis power. Suits both developer CLI use and CI/CD pipelines — `horusec version` confirms an installation.

## Typical use cases

### Full analysis of the current project

```
horusec start -p .
```

Outcome: copies the project into a `.horusec` working directory (add it to `.gitignore`), runs the configured tool set over it and prints a merged vulnerability report; each finding carries severity, file/line and the tool that produced it. First run pulls the analysis tool images.

### Run without a Docker daemon

```
horusec start -p . -D true
```

Outcome: disables the Docker dependency so the scan runs entirely locally — fewer analyzers and less coverage, but it unblocks machines without Docker installed.

### Attribute findings to commit authors

```
horusec start -p . -G true
```

Outcome: enables commit-author lookup (adds a git dependency), annotating findings — especially secrets recovered from history — with the author of the offending commit, which speeds up remediation routing.

### Scan through the official Docker image

```
docker run -v /var/run/docker.sock:/var/run/docker.sock -v "$(pwd)":/src horuszup/horusec-cli:latest horusec start -p /src -P "$(pwd)"
```

Outcome: runs the analysis inside the `horuszup/horusec-cli` image with the project mounted at `/src`; `-p` is the project path inside the container and `-P` the host-side path so findings reference your actual checkout.
