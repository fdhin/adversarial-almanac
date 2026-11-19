---
name: "tetragon"
categories:
  - defensive
version: "0.10.0-0parrot1"
homepage: "https://github.com/cilium/tetragon"
origin: "parrot"
recipe: "parrot-repo"
packaged-by: [parrot, nixpkgs]
build-system: go
meta-checked: "2026-11-09"
github-repo: "cilium/tetragon"
github-stars: 5162
github-pushed: "2026-11-07"
github-archived: false
github-issues: 316
github-match: "exact"
gh-checked: "2026-11-07"
worth: 8.5
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 2119
gh-activity-checked: "2026-11-09"
---

# tetragon

> eBPF-based security sensor from Cilium that observes process, syscall and I/O events cluster-wide and can enforce policy (down to killing a process) in the kernel in real time.

## What it's for

Tetragon (cilium/tetragon) provides realtime security observability and runtime enforcement using eBPF: its sensors hook kernel-level process execution, syscall activity and file/network I/O, emitting JSON events enriched with Linux and Kubernetes metadata (pod, namespace, container, binary) — so detection rules can target individual workloads. Beyond observability, TracingPolicy definitions attach selectors and actions to those hooks, including `Sigkill`, so a violating process is stopped inside the kernel before it causes damage. It runs as a Kubernetes DaemonSet (deployed via the Helm chart) or as a standalone Docker container, and is queried with the `tetra` CLI. It is a runtime sensor for hosts and clusters — not a static code or infrastructure scanner. Deploying the daemon and installing the CLI is covered by `install.md`.

## Typical use cases

### Stream process-execution events for a workload

Watch what executes inside a specific pod in real time.

```
kubectl exec -ti -n kube-system ds/tetragon -c tetragon -- tetra getevents -o compact --pods xwing
```

Outcome: runs `tetra getevents` inside the Tetragon daemon pod and prints a live, compact one-line-per-event stream (binary, arguments, pod/container metadata) filtered to the named pod; trigger a process inside it and the corresponding `process_exec` event appears immediately. Drop `--pods` to see events cluster-wide. On a standalone Docker deployment the equivalent is `docker exec tetragon tetra getevents -o compact`.

### Enforce a runtime policy that kills violating processes

Move from observing to blocking, enforced in the kernel.

```
kubectl apply -f fd-install.yaml
```

with `fd-install.yaml` containing a TracingPolicy that hooks `fd_install` and acts on matches:

```yaml
apiVersion: cilium.io/v1alpha1
kind: TracingPolicy
metadata:
  name: "fd-install"
spec:
  kprobes:
  - call: "fd_install"
    syscall: false
    args:
    - index: 0
      type: "int"
    - index: 1
      type: "file"
    selectors:
    - matchArgs:
      - index: 1
        operator: "Equal"
        values:
        - "/tmp/tetragon"
      matchActions:
      - action: Sigkill
```

Outcome: Tetragon loads the policy into its eBPF programs; any process on the node that then creates a file descriptor matching the selector is sent SIGKILL, and the enforcement appears in the event stream. Swap the hook (`kprobes`, `tracepoints`, `uprobes`), selectors and action (e.g. `Post`) to trace or enforce other behavior.
