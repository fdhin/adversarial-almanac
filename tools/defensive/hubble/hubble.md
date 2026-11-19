---
name: "hubble"
categories:
  - defensive
version: "1.18.5-0parrot1"
homepage: "https://github.com/cilium/hubble"
origin: "parrot"
recipe: "parrot-repo"
packaged-by: [parrot, nixpkgs]
build-system: go
meta-checked: "2026-11-09"
github-repo: "cilium/hubble"
github-stars: 4381
github-pushed: "2026-11-06"
github-archived: false
github-issues: 45
github-match: "exact"
gh-checked: "2026-11-07"
worth: 7.8
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 91
gh-activity-checked: "2026-11-09"
---

# hubble

> CLI for eBPF-based flow, DNS, HTTP and network-policy observability in Kubernetes clusters running Cilium.

## What it's for

Hubble is the observability plane of the Cilium eBPF stack: every connection in the cluster — TCP/UDP packets, DNS queries, HTTP requests, Kafka traffic — is captured as a flow record, and the `hubble` CLI queries and filters that stream. Use it to troubleshoot microservice connectivity live (why can a pod not reach a service — DNS, the app, or a network policy), to audit which flows were dropped and by which policy, and to export JSON flows into monitoring pipelines. It needs a Cilium-enabled cluster with the Hubble server (and Relay for cluster-wide views) running; with no Cilium there are no flows to observe.

## Typical use cases

### Check connectivity to the Hubble server

Confirm the CLI can reach a flow source before querying.

```
hubble status
```

Outcome: reports the Hubble server's version, uptime and flow statistics, confirming the local socket (or a target given with `--server`) is live.

### Watch one pod's traffic live

Follow a single workload's connections as they happen.

```
hubble observe --from-pod default/my-app --follow
```

Outcome: streams every flow involving that pod in real time — TCP flags, DNS lookups, HTTP requests — so a failing connection, DNS error or policy drop is visible as it occurs.

### Review traffic dropped by network policy

See what a policy denied, and between which endpoints.

```
hubble observe --verdict DROPPED --since 5m
```

Outcome: lists flows from the last five minutes carrying a DROPPED verdict, each line showing source, destination and the drop reason — the input for deciding whether a policy is blocking legitimate traffic.

### Pipe flows into analysis tooling

Feed structured flow data to jq or a monitoring stack.

```
hubble observe --since=1m -t l7 -o json | jq 'select(.l7.dns.rcode==3) | .destination.namespace + "/" + .destination.pod_name'
```

Outcome: emits L7 flows as JSON and filters them with jq — this example surfaces pods that recently received NXDOMAIN DNS answers, i.e. failing name resolution.

### Reuse a saved filter set

Capture a complex filter once and rerun it verbatim.

```
hubble observe --print-raw-filters --allowlist '{"source_label":["k8s:k8s-app=my-app"]}' > filters.yaml
```

Outcome: writes the JSON-encoded allowlist to filters.yaml; `hubble observe --config filters.yaml` later replays the same filtered query without retyping the filter expression.

*No corpus evidence — derived from upstream documentation.*
