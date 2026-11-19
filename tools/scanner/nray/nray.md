---
name: "nray"
categories:
  - blackarch-scanner
version: "99.f5eeb37"
homepage: "https://github.com/nray-scanner/nray"
packaged-by: [blackarch, nixpkgs]
blackarch-builddate: "2025-07-05"
build-system: go
license: GPL3
meta-checked: "2026-11-09"
github-repo: "nray-scanner/nray"
github-stars: 158
github-pushed: "2025-07-04"
github-archived: false
github-issues: 5
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.8
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# nray

> Distributed port and application-layer scanner built on a server/nodes model — one YAML config, many vantage points, streaming event results.

## What it's for

nray splits scanning into a server that owns configuration, target generation and result collection, and nodes that do the probing — so a scan runs faster and from multiple vantage points at once. Almost everything is configured server-side in a YAML file (target generators: plain ranges, certificate transparency, LDAP; scanner configs: TCP, UDP, and zgrab2 application grabs for SSH/HTTP; event sinks: terminal, JSON file, Elasticsearch) rather than via flags, and results stream as events you can process live with `jq`. Pure Go with no libpcap dependency, so one static binary serves both roles. TLS and node pooling are built in for larger deployments. It is a discovery scanner — it reports open ports and service banners, not vulnerabilities.

## Typical use cases

### Start the scanning server

```
nray server -c /etc/nray/config.yml
```

Outcome: the server reads the YAML configuration (targets, scan settings, event sinks), starts listening for nodes, and begins distributing work as nodes connect; scan results appear on the configured sinks (terminal and/or JSON file, Elasticsearch).

### Attach a scan node

```
nray node -s 198.51.100.10 -p 8601
```

Outcome: the node registers with the server at the given address/port and executes the scan tasks delegated to it — run several nodes on different hosts to scan from multiple vantage points in parallel.

### Collect results as structured events

Configure the `json-file` event sink in the server config, then tail it during the scan.

```
tail -f /path/to/results.json | jq '.'
```

Outcome: every open port and grab result arrives as a discrete JSON event while the scan runs, so downstream tooling (dashboards, Elasticsearch, custom triage) can react without waiting for the scan to finish.

*No corpus evidence — derived from upstream documentation (README and the Usage chapter of nray-scanner.org).*
