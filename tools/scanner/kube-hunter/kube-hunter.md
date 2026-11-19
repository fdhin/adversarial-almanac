---
name: "kube-hunter"
categories:
  - blackarch-scanner
version: "703.bc47f08"
homepage: "https://github.com/aquasecurity/kube-hunter"
packaged-by: [blackarch, nixpkgs]
blackarch-builddate: "2026-01-13"
build-system: python
license: Apache
meta-checked: "2026-11-09"
github-repo: "aquasecurity/kube-hunter"
github-stars: 5090
github-pushed: "2024-03-19"
github-archived: false
github-issues: 83
github-match: "exact"
gh-checked: "2026-11-07"
worth: 4.1
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# kube-hunter

> Hunts for security weaknesses in Kubernetes clusters from remote, in-network or in-pod vantage points.

## What it's for

kube-hunter (Aqua Security) probes Kubernetes clusters for security weaknesses from an
attacker's perspective: open kubelet/detected services, insecure capabilities, exposed
Dashboards and etcd, weak RBAC and known vulnerabilities, each reported with a
Vulnerability ID (VID) you can look up in the kube-hunter knowledge base. It requires
no authentication by design — it mimics an attacker's early stages. Scans can be run
remote (`--remote`), against all local network interfaces (`--interface`), against a
CIDR (`--cidr`), or inside a pod (`--pod`) to show what a compromised application pod
could reach; `--k8s-auto-discover-nodes` enumerates cluster nodes via the API.
Active hunting (`--active`) exploits found issues to uncover follow-on weaknesses and
can change cluster state — do not use it on clusters you do not own. Upstream is no
longer actively developed and recommends Trivy's Kubernetes misconfiguration/KBOM
scanning as the successor, but the tool remains packaged and useful for quick posture
checks.

## Typical use cases

### Scan a cluster from outside

```
kube-hunter --remote target.example.com
```

Outcome: Runs the passive hunter suite against the remote endpoint, printing each discovered vulnerability and misconfiguration with its VID and severity, ending with a summary table of findings by severity.

### Sweep a network segment for cluster services

```
kube-hunter --cidr 198.51.100.0/24
```

Outcome: Discovers hosts in the CIDR and probes only the ports used by Kubernetes services, reporting which cluster-facing endpoints (API server, kubelet, etcd, Dashboard) are exposed and what each exposure permits.

### Map discovered nodes without hunting

```
kube-hunter --cidr 198.51.100.0/24 --mapping
```

Outcome: Skips vulnerability checks and outputs only the network map of Kubernetes nodes found in the range — a quick inventory of the cluster's footprint.

### Escalate to active exploitation of findings

```
kube-hunter --remote target.example.com --active
```

Outcome: Findings are additionally exploited (e.g. accessing an open kubelet, abusing an exposed service) to detect secondary weaknesses reachable only through the initial issue; state-changing operations are attempted, so run this only against disposable clusters.

### List available checks

```
kube-hunter --list
```

Outcome: Prints every passive hunter and vulnerability kube-hunter can test (add `--active` to include active-only tests, `--raw-hunter-names` for class names usable with `--custom`), letting you select a subset via `kube-hunter --custom HunterName`.

*No corpus evidence — derived from upstream documentation.*
