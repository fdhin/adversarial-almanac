---
name: "kubeletctl"
categories:
  - corpus-scanner
homepage: "https://github.com/cyberark/kubeletctl"
origin: "corpus"
github-repo: "cyberark/kubeletctl"
packaged-by: []
meta-checked: "2026-11-09"
github-stars: 916
github-pushed: "2025-08-06"
github-archived: false
github-issues: 7
github-match: "exact"
gh-checked: "2026-11-09"
worth: 4.0
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# kubeletctl

> Kubelet API client — list pods, scan for RCE and run commands inside containers on nodes with an exposed kubelet.

## What it's for

kubeletctl (CyberArk Labs) is a command-line client that implements the kubelet's API
surface on port 10250 — both the documented endpoints and the undocumented ones kubectl
never touches. Against a node whose kubelet port is reachable and permissive
(anonymous auth, or with credentials you hold via `--cert`/`--key` or a service-account
token with `-t`), it enumerates pods, checks which containers allow command execution,
runs commands in any container on the node, and can harvest service-account tokens
from all containers at once. Its syntax mirrors kubectl (subcommands like `pods`,
`exec`, `scan`, `cri`, `logs`) with a `--server`/`--port` pair pointing at the node.
It is an attack-surface prober and post-exploitation aid, not a cluster-wide
vulnerability scanner — it talks to one kubelet at a time (or a CIDR via `--cidr` in
scan mode).

## Typical use cases

### Enumerate pods on a node

```
kubeletctl --server 198.51.100.10 pods
```

Outcome: Queries the kubelet's pod listing API and prints every pod and container running on the node with names, namespaces and container images — the inventory you need before targeting a specific container.

### Find containers that allow command execution

```
kubeletctl --server 198.51.100.10 scan rce
```

Outcome: Walks all containers reported by the kubelet and attempts a harmless probe execution in each, printing a per-container verdict of which ones accept commands — the fast triage that tells you where code execution is possible.

### Run a command inside a specific container

```
kubeletctl --server 198.51.100.10 exec "id" -p nginx -c nginx
```

Outcome: Executes `id` via the kubelet's run/exec API inside pod `nginx`, container `nginx`, and prints the command output with the container's UID/GID; any command works, so this is the beachhead for reading secrets or pivoting from the node.

### Harvest service-account tokens from all containers

```
kubeletctl scan token --server 198.51.100.10
```

Outcome: Reads the mounted service-account token of every container available through the kubelet and prints them, giving you JWT credentials to try against the Kubernetes API server with the privileges of each pod.
