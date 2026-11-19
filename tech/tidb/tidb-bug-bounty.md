---
technology: TiDB
kind: bug-bounty-methodology
audience: ai-agent
status: upstream-only
family: databases
version: "1.0"
source: "upstream-only; zero corpus evidence by design (owner-approved new-unit wave 2026-11-13) — endpoints/ports verified against docs.pingcap.com TiDB monitoring API and port requirements 2026-11-13; exposure framing cross-checked against the NCC Group TiDB assessment public report + rewritten from HackTricks (src/network-services-pentesting/4000-10080-pentesting-tidb.md), CC BY-NC 4.0, accessed 2026-11-13"
provenance: "hybrid: corpus-grounded (1 ledger rows, internal coverage ledger) + HackTricks enrichment 2026-11-13; credit in source line"
---

# TiDB bug-bounty methodology

Companion overview: [[tidb]].

Scope: the TiDB HTTP status port 10080, PD API 2379, TiKV status 20180, monitoring exposure, and component-transport checks. The MySQL-compatible listener on 4000 is standard SQL surface — method there is [[mysql]]. Honesty boundary: the demonstrated classes are information disclosure, management-plane abuse, and on-path interception; an open status endpoint is not by itself a host compromise — never inflate it into one.

## Phase 0 — Map every listener, not just 4000

```bash
# on a cluster host
ss -lntp
# remotely — scan the full component set
nmap -sV -Pn -p 2379,2380,4000,9090,10080,20160,20180 198.51.100.10
```

Test HTTP and HTTPS separately per port; treat 9090 as Prometheus-when-deployed, not a TiDB protocol port. An mTLS boundary announces itself: a TLS alert demanding a client certificate is not a closed API, and a rejected self-signed client cert proves trust validation. With an authorized cluster cert, retest (`curl --cert client.pem --key client-key.pem --cacert ca.crt ...`).

## Phase 1 — TiDB status port (10080)

`GET /` on many builds renders a route index from the named routes — the cheapest possible endpoint inventory; exact routes vary by release.

```bash
base=http://198.51.100.10:10080
curl -si "$base/"
curl -s  "$base/status"           | jq .     # connections, version, git_hash
curl -s  "$base/config"           | jq .     # addresses, PD endpoints, dirs, sockets
curl -s  "$base/info"             | jq .
curl -s  "$base/info/all"         | jq .
curl -s  "$base/schema_storage"   | jq .     # table storage metadata
curl -s  "$base/ddl/history"      | jq .
curl -s  "$base/metrics"          | head
```

An unauthenticated `/config` discloses advertised addresses, PD endpoints, Unix sockets, storage/temporary directories, log and slow-query filenames, operational limits — feed the cluster graph from it. The same router can expose control handlers (`/ddl/owner/resign`, `/upgrade/{op}`, table scatter/stop-scatter): confirm their presence from the route index and the version's source, but do not invoke state-changing routes outside an explicitly authorized disposable environment.

## Phase 2 — Go pprof triage

TiDB and sibling Go services publish `net/http/pprof` below `/debug/pprof/`:

```bash
base=http://198.51.100.10:10080
curl -s "$base/debug/pprof/"                            # index of profiles
curl -s "$base/debug/pprof/cmdline" | tr '\0' ' '; echo # NUL-delimited argv
curl -s "$base/debug/pprof/goroutine?debug=1" | head -n 100
curl -s "$base/debug/pprof/heap" -o heap.pb.gz && go tool pprof -top heap.pb.gz
```

Triage with the cheap endpoints: `cmdline` (binary + config paths), short textual goroutine samples (function names, execution state), heap (memory addresses). CPU profiles and traces actively consume the target while collecting — avoid on production. Repeat against every discovered Go management listener; sidecar services carry the same surface (e.g. an ng-monitoring server exposing `cmdline` on its own port — deployment-specific, find it via Prometheus targets, don't assume a fixed number). An SSRF primitive reaches these even when they are not directly routable.

## Phase 3 — PD API (2379)

```bash
pd=http://198.51.100.20:2379/pd/api/v1
curl -s "$pd/stores"          | jq .    # every TiKV node, capacities, versions
curl -s "$pd/schedulers"      | jq .
curl -s "$pd/operators"       | jq .
curl -s "$pd/config"          | jq .
curl -s "$pd/cluster/status"  | jq .
```

The audited route set also carried state-changing methods (operator/scheduler creation and deletion, configuration and placement-rule updates, store state/labels/weights, tombstone removal). A successful unauthenticated GET proves disclosure only — whether mutators pass through the deployment's proxies/middleware needs explicit authorization and a rollback plan. Port 2379 is also etcd's home port — distinguish the two before reporting ([[etcd]]).

## Phase 4 — TiKV status (20180)

Implemented in Rust, several paths merely resemble Go pprof:

```bash
tikv=http://198.51.100.30:20180
curl -si "$tikv/status"
curl -s  "$tikv/config"          | jq .
curl -s  "$tikv/engine_type"; echo
curl -s  "$tikv/resource_groups" | jq .
curl -s  "$tikv/debug/pprof/cmdline" | tr '\0' ' '; echo
curl -s  "$tikv/metrics"         | head
```

Destructive siblings on the same router (`POST /config`, `PUT /config/reload`, log-level changes, `PUT /pause_grpc`/`resume_grpc`): fingerprint from source/allowed methods; do not execute on production to "prove" the read side.

## Phase 5 — Monitoring stack

```bash
prom=http://198.51.100.40:9090
curl -s "$prom/api/v1/status/buildinfo" | jq .
curl -s "$prom/api/v1/targets" | jq '.data.activeTargets[] | {scrapeUrl,health,labels}'
curl -sG "$prom/api/v1/query" --data-urlencode 'query=up' | jq .
```

Prometheus targets are a free inventory of the whole deployment (component names, addresses, roles); Grafana and exporters on the same host extend the surface. Claiming admin impact requires verifying a state-changing endpoint (admin API/lifecycle) is actually enabled — an open query API is recon, not compromise.

## Phase 6 — Component-transport verification

TLS between components is supported but not enforced in the stock configuration. From an authorized observation point:

```bash
sudo tcpdump -i eth0 -nn -s0 -w tidb.pcap \
  'tcp port 2379 or tcp port 2380 or tcp port 10080 or tcp port 20160 or tcp port 20180'
tshark -r tidb.pcap -Y 'tls.handshake or http.request' \
  -T fields -e ip.src -e ip.dst -e tcp.dstport -e http.request.full_uri
tshark -r tidb.pcap -Y 'tls.handshake.type == 1'   # ClientHello presence check
```

Absence of decoded TLS alone proves nothing — confirm the capture holds complete new connections, then look for the ClientHello or probe each listener with `openssl s_client`. Report SQL-client TLS (4000) and component TLS separately; a cluster can enforce one and not the other.

## Phase 7 — Hardening checks to report

Status/control listeners bound to loopback or a dedicated management network; firewalls scoping 10080/20180/2379/2380/20160 and monitoring to required peers; client certificates for remote management with the CA/CN policy verified; pprof disabled or restricted where profiling is not operationally required. After TLS enablement, validate 10080/2379/20160 with the intended CA and again WITHOUT a client cert — a correct mTLS endpoint rejects it rather than silently falling back.

## Appendix — tools in prose

nmap (component-port sweep), curl and jq (status/API sweeps), openssl s_client (transport probing), go tool pprof (heap analysis), tcpdump/tshark (component-traffic capture), ss/netstat (host-side listener mapping).
