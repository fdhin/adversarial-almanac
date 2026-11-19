---
technology: "etcd"
kind: bug-bounty-methodology
audience: ai-agent
family: databases
version: "1.1"
last-refresh: "2026-11-19"
source: "corpus-mined (2 writeups — pass 1 wave 4, 2026-11-12) + upstream-verified (etcd.io v2.3 API/members docs, v3.5 dev guide, v3.7 security guide); v1.1 supplement: AI senior-reviewer pass (GPT-6.1 Sol via M365 Copilot) + dual fact-check (agent-side web verification and reviewer-side web re-check)"
provenance: "corpus-grounded: 3 contributing writeups (internal coverage ledger, gate documented in header); upstream CVEs verified against primary sources during enrichment; basis of this page summarized from the unit ledger. v1.1 supplement 2026-11-19 (model gpt-6.1-sol via M365 Copilot senior-review pass; claims re-verified against etcd.io upgrade guides 3.4/3.6/3.7 and changelogs, NVD CVE-2026-33413/33343, kubernetes.io API-concepts and service-account docs, httpd.apache.org logs/configuring pages, etcdctl README/dev guide): v2 branch re-gated legacy-only, failure-class decision points added, --from-key/flat-keyspace semantics, Kubernetes value-encoding decision point (k8s\\0 protobuf envelope), cert-CN vs RBAC separation, per-range permissions, data-dir and WAL-copy corrections, Apache payload caveats, version/CVE gate table, junior-pitfall table, etcdutl tool-map entry, severity rubric replaces fixed ordering. The engagement-specific remco→Apache chain is kept as a case study and is NOT VERIFIED as a general reproduction."
---

# etcd bug-bounty methodology

Companion overview: [[etcd]].

Scope note: strict re-gating kept only two engagements (an etcd→remco→Apache root privesc chain, and 2379 open on a Kubernetes host). The dispatch-time sample of three was marker noise. Everything k8s-specific here is upstream-derived; the Kubernetes unit owns the broader cluster methodology.

Version gate (read first — it changes every phase below):

| Fact | Consequence |
| --- | --- |
| v2 API disabled by default since etcd 3.4 (`--enable-v2=false` default) | A 3.4+ server answering `/version` over HTTP usually will NOT serve `/v2/keys` — a v2 404 is the *default* state, not evidence of protection |
| `--enable-v2` flag removed in 3.6; remaining v2 store machinery removed in 3.7 | v2 is a legacy branch only (≤3.5, or 3.4/3.5 explicitly configured). v3 is the normal path |
| CVE-2026-33413 / CVE-2026-33343 (nested-txn RBAC bypass; auth bypass on several RPCs) patched in 3.4.42 / 3.5.28 / 3.6.9 (2026-03-20) | On auth-enabled clusters in this version band, metadata/RPC access is version- and identity-dependent — check patch level before assuming member-list or status requires admin. 3.6.11 (PrevKv/lease read bypass) and 3.6.14 (single-key watch leak) added further authz fixes |
| Kubernetes ≥1.24 no longer auto-creates permanent SA-token Secrets (GA/gate-removed 1.27); Pods use projected short-lived tokens since 1.22 | Expect fewer long-lived `system:token` style secrets under `/registry/secrets/` on modern clusters; legacy/manual token Secrets can still exist |

## Phase 0 — Recon

```
2379/tcp open  etcd-client
2380/tcp open  etcd-server
nmap -sV -p 2379,2380 198.51.100.x
```

Also check for etcd on a foothold host: `ss -lntp` showing 127.0.0.1:2379 means the walk-in needs no network exposure at all — but loopback binding removes only the network requirement, never the TLS or auth requirement. Record the client tool version separately from the server (`etcdctl version`); both matter for evidence.

Version probe first — it needs no auth, but note what it does and does not prove: `/version` reports server/cluster *versions*, not which APIs are reachable or what the caller may do. A plaintext `/version` answer proves only that one plaintext listener answered one unauthenticated handler.

```bash
curl -s http://198.51.100.x:2379/version
# {"etcdserver":"3.4.x","etcdcluster":"3.4.x"}   → v3-capable server; v2 API is default-OFF at this generation
```

Distinguish failure classes before concluding anything (a 401 and a refused connection are different findings):

- TCP refusal / timeout → wrong host/port or firewalled.
- TLS handshake / cert-verification failure → transport is on; go to Phase 4.
- HTTP status + body (401/403/404) → route/auth answer; a v2 404 on ≥3.4 is expected default behavior.
- gRPC status (Unauthenticated / PermissionDenied / Unavailable) → v3 gate state.
- HTTP and gRPC can even live on different listeners (`--listen-client-http-urls` splits HTTP off `--listen-client-urls`).

## Phase 1 — Legacy v2 HTTP enumeration (≤3.5 branch only)

The v2 API is an HTTP/JSON *application* protocol — it can be served over HTTP or HTTPS; "v2" says nothing about transport security. If 2379 answers `/version` without a TLS handshake and the server is ≤3.5 (or explicitly configured), try the ladder:

```bash
curl -s http://198.51.100.x:2379/v2/keys/?recursive=true   # v2 keyspace tree
curl -s http://198.51.100.x:2379/v2/members                # cluster member names + peer/client URLs
curl -s http://198.51.100.x:2379/v2/stats/self             # leader/follower state, node names
```

Decision points:
- `?recursive=true` returns the tree → collect the *minimum authorized evidence* that proves impact, not "everything": custom stacks leak DB creds and API keys under application-named prefixes. (Kubernetes data lives in the v3 store — a v2 read enumerates nothing v3; the stores are separate and writes through one do not populate the other.)
- 401 on keys but `/version` works → auth/transport gate; see Phase 4.
- `/v2/members` works even when writes are gated — it maps the cluster (member names, peer URL on 2380) for lateral targeting. A peer URL is targeting *information*, not authorization to test it.

## Phase 2 — v3 dump via etcdctl (the normal path)

etcdctl speaks the v3 gRPC API and needs no server-side auth when none is configured. Record `etcdctl version` (client) next to the server version. `ETCDCTL_API` describes the *client* generation — setting it cannot restore server functionality the server no longer has:

```bash
etcdctl version
ETCDCTL_API=3 etcdctl --endpoints=http://198.51.100.x:2379 get --prefix / --keys-only
ETCDCTL_API=3 etcdctl --endpoints=http://198.51.100.x:2379 get --from-key '' --keys-only --limit=10   # whole flat keyspace, bounded
ETCDCTL_API=3 etcdctl --endpoints=http://198.51.100.x:2379 member list -w table
ETCDCTL_API=3 etcdctl --endpoints=http://198.51.100.x:2379 endpoint health
```

Keyspace semantics (juniors get this wrong constantly):

- v3 is a **flat byte-key namespace**. `--prefix /` matches only keys that *start with* `/` — application keys need no leading slash. `--from-key ''` (empty start key, byte comparison to end of keyspace) is the whole-namespace probe; `--limit` bounds returned records (a limit bounds the *result set*, never the authorization range).
- Empty result ≠ no data. Wrong store (v2 vs v3), wrong prefix, wrong endpoint, or resource-specific backend — check each before writing "no data".
- Successful metadata access (member list, health) proves nothing about key reads; successful reads prove nothing about writes. etcd RBAC grants read/write/readwrite per key, range `[start,end)`, or prefix — test each capability independently.

On a Kubernetes control-plane node hit via tunnel/SOCKS, `--prefix /registry/` yields pods, roles, secrets — with two caveats: `--etcd-prefix` *defaults to* `/registry` but is configurable, and `--etcd-servers-overrides` (format `group/resource#servers`) can route specific resources to a different etcd entirely. An empty default prefix is not evidence of an empty cluster.

Value-encoding decision point — do not base64-loop until output "looks readable":

1. `etcdctl -w json` base64-encodes returned byte fields — decode that outer layer once.
2. Identify what's inside: Kubernetes stored objects are normally a Protobuf envelope with the 4-byte magic `k8s\0` (0x6b 0x38 0x73 0x00); JSON serialization is also possible; an opaque blob with a `k8s:enc:` prefix means API-server encryption-at-rest — stop, that is a separate control and a separate finding.
3. Only after identifying the envelope, decode Secret `.data` fields (a *second* base64 layer inside the Secret JSON).

## Phase 3 — etcd as a privesc lever (the config-consumer chain — case study, not a portable recipe)

When you can *write* a key but lack root, enumerate what reads the keyspace as root. Observed chain (engagement-specific; versions/escaping not independently reproducible from the writeup — treat as a class, not a script):

```
web app: system("ETCDCTL_API=3 /usr/bin/etcdctl put /customers/<user>/url " . $input)
   ↓ (value fully attacker-controlled; NB the system() call is itself a candidate command-injection finding — verify separately)
remco template manager (root) watches /customers/*, renders values into
/etc/apache2/sites-enabled/010-customers.conf, restarts apache2
   ↓
etcdctl put /customers/cust-1a2b3c4d/url 'foo.customer.example
ErrorLog "|/usr/bin/<canary command>" #'
   → Apache pipe-ErrorLog directive executes when the piped-log program starts → root (conditional, see below)
```

Three facts gate the Apache sink, all verified against upstream httpd docs:

- Apache config **does not support trailing inline comments** — comments may not appear on the same line as a directive. The trailing `#` trick is not a reliable suffix-neutralizer and can invalidate the whole config (config syntax rejected → chain dies silently).
- Piped-log programs are spawned by the **parent httpd process and inherit its userid** — typically root, not guaranteed root. Prove the parent's identity, don't assume it.
- Ordinary piped logging starts the program directly, no shell — payload must account for that.

Before writing anything: enumerate the consumers — remco configs (`/etc/remco/`, `resource.backend.etcd` blocks with `watch = true`), confd, systemd units, cron scripts calling etcdctl — then establish the watched key/store, the exact template and its escaping, the rendered output location, reload mechanism, and the consumer's real privilege. Any write test needs an approved canary location (a watcher consuming a whole prefix makes "random new key" unsafe), ownership check, and cleanup criteria. Prefer a non-persistent, owner-agreed execution canary over modifying access control (authorized_keys copies are persistence-changing evidence — use only with explicit approval).

## Phase 4 — TLS / client-cert-gated clusters

Separate three gates that juniors conflate: transport (TLS), authentication (client cert / CN identity), authorization (etcd RBAC).

- `--client-cert-auth --trusted-ca-file` → plain HTTP fails the handshake; try HTTPS (`curl -k https://...`) **knowing what -k does**: it disables *server*-certificate verification. It neither supplies a client cert nor defeats mTLS. Label -k evidence as transport-only and re-run with the proper CA/hostname before trust conclusions.
- TLS without client auth = encryption without authentication — still worth reporting, weaker than it looks in a port scan.
- Client cert found on a foothold host: possession proves nothing alone. You need the matching private key, a valid/unexpired cert trusted by the cluster's CA, connectivity, and — if etcd authentication is enabled — the CN-mapped user's RBAC permissions (which may be range-limited). Locate the real apiserver config (`--etcd-certfile/--etcd-keyfile/--etcd-cafile`) rather than assuming every node holds them:
  ```bash
  ETCDCTL_API=3 etcdctl --endpoints=https://198.51.100.x:2379 \
    --cacert=ca.crt --cert=etcd-client.crt --key=etcd-client.key get --prefix /
  ```
- Disk access instead of network: upstream default data dir is `${name}.etcd` (e.g. `default.etcd`); `/var/lib/etcd` is a packaging convention, not the upstream default. The bbolt file (`member/snap/db`) has no native etcd-layer encryption, but (a) Kubernetes encryption-at-rest values stay encrypted in a copied backend, and (b) a raw backend copy can miss data still only in the WAL — prefer an online snapshot unless offline is the point.

## Phase 5 — Reporting

Severity is a rubric, not a fixed ordering — score by demonstrated access, privilege, affected systems, and credential validity: a narrowly scoped secret disclosure can outweigh a keyspace read of dead creds; host-root execution can outweigh both. Report demonstrated capability separately from supported inference and unverified downstream impact.

Minimum evidence per finding: endpoint + vantage + timestamp, client and server versions, credential/transport-verification state, exact operation and approved key/prefix, HTTP/gRPC status and exit code (redacted output), demonstrated read/write scope *without extrapolation*, value serialization/encryption status, cleanup outcome.

Remediation guidance: bind client/peer listeners only to required interfaces (not "behind a firewall" as the control), mTLS (`--client-cert-auth`, `--peer-client-cert-auth`) **combined with** least-privilege etcd RBAC where auth applies (cert identity ≠ authorization), protected keys and backups, current patch level (≥3.4.42/3.5.28/3.6.9 for CVE-2026-33413/33343; later 3.6.x authz fixes), and Kubernetes API-server encryption-at-rest for sensitive resources (note: enabling it does not rewrite already-stored objects).

## Junior-tester pitfalls (top failure modes)

| Likely mistake | Consequence |
| --- | --- |
| v2 404 read as "etcd is protected" | Miss the exposed v3 store (v2 is default-off since 3.4) |
| `/version`, member list, or health read as proof of key access | Overstated finding; each capability needs its own test |
| `--prefix /` assumed to enumerate everything | Miss keys without a leading slash in the flat keyspace |
| Base64-decoding in a loop until output looks readable | Misread Protobuf or encrypted blobs as "corrupt data" |
| Found `.crt` assumed usable | No key / expired / wrong purpose / range-limited role |
| Tunnel assumed to work identically for curl and gRPC | Routing/HTTP2 problems misdiagnosed as auth failures |
| Copying the Apache payload literally | Trailing-`#` comment invalidates config; chain dies |
| `authorized_keys` copy as default root proof | Unnecessary persistence change; evidence discipline breach |
| Empty result read as "no data" | Wrong store/prefix/endpoint/backend never checked |
| Following advertised peer URLs | Tests outside authorized scope |

## Tool map (prose)

nmap for port/version fingerprint; curl for the v2 HTTP ladder; etcdctl (from the etcd release tarball, no server needed) for v3 gRPC — plus **etcdutl** since the 3.6 split: `etcdctl snapshot save` remains online, but `snapshot status`/`snapshot restore` live in etcdutl (etcdutl itself predates 3.6; 3.6 removed the overlapping etcdctl commands); bbolt parsers (boltdb readers in Go) for offline `member/snap/db`; grep/find for client certs and watcher configs on foothold hosts. Kubernetes-side post-exploitation belongs to the kubernetes unit.

## References (v1.1 verification trail)

- etcd.io upgrade guides 3.4 (`--enable-v2=false` default), 3.6 (flag removed), 3.7 (v2 machinery removed); CHANGELOG-3.6 (etcdutl command split, 3.6.11/3.6.13+ authz fixes); blog "Announcing etcd v3.6".
- NVD CVE-2026-33413 and upstream advisory CVE-2026-33343 (patched 3.4.42 / 3.5.28 / 3.6.9).
- etcd.io configuration reference (`--data-dir` default `${name}.etcd`; `--listen-client-http-urls`), dev-guide/interacting_v3 (`--from-key`, `--limit`), RBAC guide (per-key/range/prefix read-write grants), security FAQ (no native disk encryption), recovery guide (WAL vs snapshot copy warning).
- kubernetes.io API concepts (Protobuf wrapper 4-byte magic), service-account docs (projected tokens 1.22+, auto Secret creation stopped 1.24/GA 1.27), kube-apiserver reference (`--etcd-prefix`, `--etcd-servers-overrides`).
- httpd.apache.org docs/2.4 logs.html (piped logs inherit parent userid, no shell) and configuring.html (no comments on directive lines).
