---
name: "calicoctl"
categories:
  - defensive
version: "3.26.0-0parrot1"
homepage: "https://github.com/projectcalico/calico"
origin: "parrot"
recipe: "parrot-repo"
packaged-by: [parrot, aur, nixpkgs]
build-system: go
meta-checked: "2026-11-09"
github-repo: "projectcalico/calico"
github-stars: 7413
github-pushed: "2026-11-06"
github-archived: false
github-issues: 299
github-match: "exact"
gh-checked: "2026-11-07"
worth: 8.7
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 4338
gh-activity-checked: "2026-11-09"
---

# calicoctl

> Command line interface for creating, inspecting and modifying Calico networking and network-policy resources.

## What it's for

calicoctl manages Calico — the CNI networking and network-policy layer used with Kubernetes — through declarative resources: network policies, IP pools, BGP peers and configurations, nodes, and workload/host endpoints. It offers kubectl-like verbs (create, apply, replace, patch, delete, get, label, convert, validate) plus an ipam subsystem for IP address management, and talks either to the Kubernetes API or directly to an etcdv3 datastore. It configures and troubleshoots a running Calico cluster; it does not install Calico itself. Resource type names accept short aliases (gnp for globalnetworkpolicy, ippool, wep for workloadendpoint, no for node, and so on).

## Typical use cases

### List Calico nodes and policies

Survey what Calico sees in the cluster.

```
calicoctl get nodes -o wide
calicoctl get networkpolicy -A
```

Outcome: ps-style tables of Calico node resources (with extra columns in wide mode) and all network policies across namespaces; -o yaml or -o json emit the resource definitions for reuse.

### Apply a policy or configuration file

Create-or-update Calico resources from YAML or JSON, kubectl-style.

```
calicoctl apply -f policy.yaml
```

Outcome: each resource in the file is created if absent and replaced if it already exists, which makes re-runs idempotent; the output names the resources written.

### Export, edit and replace low-level configuration

Change Felix or BGP settings that are managed as resource definitions.

```
calicoctl get felixconfiguration default -o yaml --export > felix.yaml
# edit felix.yaml
calicoctl replace -f felix.yaml
```

Outcome: the live configuration is dumped as YAML with cluster-specific fields stripped by --export; after editing, replace pushes the modified definition back to the datastore.

### Inspect IP address management usage

See how Calico's IP pools are being consumed.

```
calicoctl ipam show
```

Outcome: overall IP usage per pool (and details for a Calico-assigned address), the first check when workloads fail scheduling with address exhaustion; related subcommands audit datastore integrity (ipam check) and free addresses (ipam release).

### Target a different cluster context

Run calicoctl against another kubeconfig context in a multi-cluster setup.

```
calicoctl --context cluster-b get ippool -o yaml
```

Outcome: calicoctl connects using the named kubeconfig context and lists IP pools of that cluster rather than the current one.

*No corpus evidence — derived from upstream documentation.*
