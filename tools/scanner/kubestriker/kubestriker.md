---
name: "kubestriker"
categories:
  - blackarch-scanner
version: "v1.2.0.r2.ge1776ea"
homepage: "https://github.com/vchinnipilli/kubestriker"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-13"
build-system: scripts
license: Apache-2.0
meta-checked: "2026-11-09"
github-repo: "vchinnipilli/kubestriker"
github-stars: 1001
github-pushed: "2024-04-06"
github-archived: false
github-issues: 18
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.5
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# kubestriker

> Platform-agnostic Kubernetes security auditor with an interactive CLI, covering self-hosted, EKS, AKS and GKE clusters.

## What it's for

Kubestriker (Vasant Chinnipilli) audits Kubernetes clusters for misconfigurations
across platforms — self-hosted, Amazon EKS, Azure AKS and Google GKE. It is an
interactive, menu-driven Python program with no command-line flags: on start it asks
how to reach the cluster (endpoint URL/IP, a kubeconfig file, or an IP range/CIDR),
then runs checks against the API server (secure and insecure ports), kubelet
(read-only and read-write endpoints), exposed services and RBAC posture, writing a
per-host text report of findings. A companion web application covers the same checks
for continuous scanning, and the CLI is designed for CI/CD pipeline integration.
It is a point-in-time misconfiguration auditor, not an exploitation framework — it
does not attempt the issues it reports. The project is dormant (last push 2024).

## Typical use cases

### Audit a cluster by endpoint

```
kubestriker
```

Outcome: Starts the interactive audit; choose `url or ip` at the first menu and enter the Kubernetes endpoint. The tool validates reachability, runs its service-discovery and misconfiguration checks against that cluster, and writes results to a `<host>.txt` report file while printing progress bars per check.

### Audit every cluster in a kubeconfig

```
kubestriker
```

Outcome: Choose `configfile` at the first menu (accept the default kubeconfig path or enter a custom one), then pick one of the discovered clusters from the list. The same check suite runs against the selected cluster and the report is written to `<host>.txt`; choosing `iprange or cidr` instead sweeps a range such as `198.51.100.1-9` or `198.51.100.0/28` host by host.

*No corpus evidence — derived from upstream documentation and the packaged entry point (`python -m kubestriker` menu flow).*
