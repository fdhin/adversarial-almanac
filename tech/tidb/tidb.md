---
technology: TiDB
kind: overview
audience: offensive-security
status: upstream-only
family: databases
layer: data store
language: Go
homepage: https://pingcap.com/tidb
github-repo: https://github.com/pingcap/tidb
provenance: "upstream-only; zero corpus evidence by design (owner-approved new-unit wave 2026-11-13) — port table and status/PD API endpoints verified against docs.pingcap.com (TiDB monitoring API, port requirements, component TLS) 2026-11-13; exposure classes cross-checked against the NCC Group TiDB security assessment public report + rewritten from HackTricks (src/network-services-pentesting/4000-10080-pentesting-tidb.md), CC BY-NC 4.0, accessed 2026-11-13"
---

# TiDB

> A MySQL-compatible SQL engine whose real attack surface is everything EXCEPT the SQL port — an unauthenticated HTTP status port, a cluster-brain API on 2379, Go pprof on every component, and component traffic that talks plaintext by default.

## What it is

TiDB (PingCAP, Go) is a distributed SQL database marketed as MySQL-compatible and horizontally scalable. It is not one process but a stack: the **TiDB** SQL layer speaks the MySQL wire protocol on **4000/tcp** and is the only port a conventional database assessment expects; behind it, **Placement Driver (PD)** coordinates the cluster, **TiKV** stores the data, and a Prometheus/Grafana monitoring stack watches all of it — each with its own HTTP listeners. The design assumption is that everything behind 4000 lives on a trusted cluster network.

That assumption is the vulnerability class. TiDB's own **status port 10080/tcp** serves `/status`, `/config`, `/info`, schema metadata, metrics, and Go `pprof` handlers without authentication; PD's API on **2379/tcp** (`/pd/api/v1/`) enumerates stores, schedulers, and configuration and accepts cluster-administration operations; TiKV's status port **20180/tcp** does the same for storage nodes. None of it requires database credentials — reachability is the only gate. A 2026 third-party assessment of a stock deployment (NCC Group, public report) confirmed the pattern: component TLS supported but not enforced out of the box, management interfaces exposed on the cluster network, plaintext component traffic observable on-path — an internal-reconnaissance and management-plane-abuse boundary rather than a demonstrated RCE chain.

## The anatomy that matters on an engagement

    app/DBA → 4000/tcp (MySQL wire) → SQL auth → data        ← the "expected" door, see [[mysql]]
    internal-recon doors (no DB credentials needed):
      10080 TiDB status  /status /config /info /info/all /schema_storage /ddl/history /metrics
                          /debug/pprof/* (cmdline, goroutine, heap, ...)
      2379  PD API       /pd/api/v1/stores|schedulers|operators|config|cluster  (+ mutators)
      2380  PD peer      PD-node-to-PD-node
      20160 TiKV         storage service traffic
      20180 TiKV status  /status /config /engine_type /region /resource_groups (+ pprof paths)
      9090  Prometheus   /api/v1/targets, query API   (deployment-specific, not a TiDB port)

Three structural properties drive findings: (1) every component ships an HTTP status plane that trusts the network; (2) Go services publish `pprof` by default, leaking argv and runtime state; (3) the status/config disclosures are self-enumerating — `/config` names the PD endpoints and directories that lead to the rest of the cluster.

## Where you'll meet it

- **Cloud-native and hybrid platforms running TiDB Operator on Kubernetes** — the whole port set lands on pod networks where a single compromised pod or SSRF reaches the control plane; see [[kubernetes]].
- **Data-platform segments** where 4000 is firewalled to app subnets but the "internal" ports (10080/2379/20180) are open across the cluster VLAN, VPN, or after a container escape.
- **Monitoring hosts** aggregating Prometheus/Grafana for the cluster — target inventories in Prometheus name every TiDB/PD/TiKV instance; see [[grafana]].

## Reading the framework's mind

1. MySQL on 4000 answers like MySQL — the differentiating surface is HTTP on 10080; probe it before anything else (see [[tidb-bug-bounty]] Phase 1).
2. `/status` returns version and git hash unprompted; `/config` returns advertised addresses, PD endpoints, socket/file paths — a complete cluster map in one GET.
3. `pprof`'s `cmdline` returns NUL-delimited process arguments (binary + config paths); goroutine dumps expose function names; heap profiles expose addresses — useful for exploit development and confirming a separate compromise.
4. PD on 2379 is the cluster brain: read-only GETs prove disclosure, mutating routes (operators, schedulers, placement rules, store state) are cluster-admin actions — fingerprint, don't fire.
5. Component TLS is supported but not enforced by default — on-path capture distinguishes TLS handshakes from plaintext on 2379/2380/10080/20160/20180; keep SQL-client TLS on 4000 conceptually separate from component TLS. Port 2379 is also etcd's conventional port — check which one you are actually talking to ([[etcd]]).

## Where to go next

The companion methodology: [[tidb-bug-bounty]] — listener mapping, status/PD/TiKV sweeps, pprof triage, plaintext-traffic detection, and the honesty boundary between disclosure and compromise.
