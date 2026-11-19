---
technology: "etcd"
kind: overview
audience: offensive-security
family: databases
layer: data store
language: Go
homepage: https://etcd.io
github-repo: https://github.com/etcd-io/etcd
provenance: "corpus-grounded (2 writeups after strict re-gate; structure-pass marker estimate was 4 — two sample files had no etcd trace, one was an SID-hex substring) + upstream-verified (etcd.io v2.3 API + members API docs, v3.7 transport security model, v3.5 dev guide)"
---

# etcd

> The distributed key-value store that holds the Kubernetes control plane's entire brain — and by its own docs' admission ships with no authentication and no transport security since 2013, so an exposed 2379 is a cluster takeover, not a database leak.

## What it is

etcd is a strongly consistent, replicated key-value store written in Go, created at CoreOS and now a CNCF project, that distributed systems use as their shared source of truth: configuration, cluster state, service discovery. Kubernetes is the deployment that made it ubiquitous — every object, secret, and ConfigMap in the cluster lives under keys in etcd, and the API server is just a gatekeeper in front of it. It listens on **2379** for clients (the port nmap calls `etcd-client`) and **2380** for peer replication (`etcd-server`).

The operator's core insight: **the data plane is the whole game**. etcd's own security guide states it plainly — RBAC and transport authentication are *not enabled by default*, a default unchanged since 2013, and "an etcd cluster which doesn't enable security features can expose its data to any clients." There is no query language and no injection surface; the attack is always access — an unauthenticated 2379, a readable client cert, or a foothold on a host where some root-running config consumer (Kubernetes, Apache via a template manager, confd-style reloaders) trusts whatever lands in the keyspace.

The second insight: **etcd is rarely the target, it's the lever**. Because its job is to feed configuration to privileged consumers, a write primitive into etcd (web app shelling out to etcdctl, a compromised node, a mis-scoped client cert) becomes code execution as whatever daemon watches the keys. One observed engagement is the canonical shape: a PHP signup page ran `etcdctl put /customers/<user>/url <attacker-string>`; the remco template manager, running as root, rendered that value into an Apache vhost config and restarted Apache — one `ErrorLog "|/usr/bin/cp ..."` line later, root.

## The anatomy that matters on an engagement

```
app / admin CLI / kubectl ──► API server ──TCP 2379──► etcd (★ default: no auth, no TLS, plaintext JSON)
                              (k8s path)                    ├─ v2: /v2/keys/... hierarchical tree (HTTP GET = read)
  web app ──system()──► etcdctl put (★ app-embedded writes: attacker controls the value)      │
                                                            ▼
                                              config consumers running as root:
                                              kube-apiserver, remco/confd templates (★ the escalation)
```

Structural properties that drive findings:

1. **The v2 API is plain HTTP** — `/version` fingerprints, `/v2/keys/?recursive=true` dumps the entire keyspace, `/v2/members` maps the cluster. No client needed beyond curl.
2. **Two API generations coexist** — v2 (HTTP/JSON) and v3 (gRPC, reached via etcdctl or `/v3/*` gRPC-gateway). Keys written through one are invisible to the other; know which side the target's data lives on before concluding a keyspace is empty.
3. **Kubernetes secrets are base64 in etcd** — whatever the cluster stores (Secrets, tokens, ConfigMaps) is one recursive GET away from plaintext once you can reach 2379 directly.

## Where you'll meet it

- **Kubernetes control-plane nodes** — 2379 bound to the node IP (or 0.0.0.0) instead of localhost with client-cert auth off: full cluster state, including every Secret, to any network peer. One observed engagement is exactly this shape — 2379/2380 open on the scan, etcd the pivot into the cluster's internals.
- **Config-management stacks** — remco, confd, and similar watchers consuming etcd keys as root (field-observed). The etcd write is the privesc; the template engine is the executor.
- **Embedded in appliances / custom backends** — any Go or cloud-native stack that needs "a consistent store" and gets installed with defaults.
- **Post-foothold localhost** — etcd bound to 127.0.0.1:2379 on a box you have a shell on: the walk-in is one etcdctl or curl away, no credentials checked.

## Reading the daemon's mind

- No auth, no TLS, no RBAC by default (upstream's stated position since 2013) — a 2379 that answers `/version` is worth fully probing before assuming a gate exists.
- v2 surface: `GET /version`, `GET /v2/keys/?recursive=true` (whole tree), `GET /v2/members` (cluster topology), `GET /v2/stats/self` (leader/follower, peer names).
- v3 via etcdctl: `ETCDCTL_API=3 etcdctl get --prefix /` dumps everything; etcdctl ≥3.4 defaults to v3 API, ≤3.3 defaults to v2 — set the env var explicitly.
- Hardening uses client certs, not passwords: `--client-cert-auth --trusted-ca-file` gate 2379; when auth is enabled, the cert CN *is* the username. Finding a readable client cert + CA on a compromised node is the key to the cluster.
- Data at rest is unencrypted (upstream FAQ) — grab the data-dir (`member/snap/db`, a bbolt file) for offline parsing when you have disk access but the port is properly gated.

## Where to go next

- [[etcd-bug-bounty]] — full method: port recon → unauthenticated v2 HTTP enum → v3 etcdctl dump → Kubernetes secret extraction → cert-gated clusters → etcd-as-config-backend privesc chains.
