---
technology: "Cassandra"
kind: overview
audience: offensive-security
family: databases
layer: data store
language: Java
homepage: https://cassandra.apache.org
github-repo: https://github.com/apache/cassandra
provenance: "corpus-SILENT after strict re-gate (structure-pass estimate of 4 writeups was entirely substring noise: Windows build 19042, a GitHub username 'cassandra.shelton', disassembly hex '890424' — no writeup attacks Cassandra; see internal coverage ledger) — upstream-verified only (cassandra.yaml reference 3.11, Apache security page, JFrog CVE-2021-44521 advisory) + rewritten from HackTricks (src/network-services-pentesting/cassandra.md), CC BY-NC 4.0, accessed 2026-11-13"
---

# Cassandra

> A ring-clustered NoSQL column store whose shipped configuration is `AllowAllAuthenticator` — the project's own yaml comments describe the default as "performs no checks - set it to disable authentication" — so an exposed 9042 is a CQL shell into every keyspace, no password prompt.

## What it is

Apache Cassandra is a masterless, horizontally scalable wide-column store originally built at Facebook (on Amazon Dynamo's distribution ideas and Bigtable's data model), released open-source in 2008 and now an Apache top-level project written in Java. Clients speak **CQL**, a SQL-looking language, to the native transport on **9042** (binary protocol v3+, what cqlsh and every driver uses); the legacy Thrift RPC sat on 9160 and is long gone from modern versions. Nodes gossip on 7000/7001 and form a ring — no single master, every node accepts reads and writes.

The operator's core insight: **the default posture is anonymous superuser**. Cassandra's reference configuration ships `authenticator: AllowAllAuthenticator`, `authorizer: AllowAllAuthorizer` — every client is every user, and `cqlsh` connects with no credentials. The docs' own caveat on `native_transport_port` reads "For security reasons, you should not expose this port to the internet" — a warning that exists precisely because the default trust model assumes a private network. Authentication is a deliberate opt-in: swap in `PasswordAuthenticator` (hashed passwords in the `system_auth` keyspace, default superuser `cassandra`/`cassandra`) and `CassandraAuthorizer`, which real deployments forget more often than you'd think.

The second insight: **schema and secrets live in queryable system tables**. `system_schema.*` and the `system*` keyspaces describe every table in the cluster, and application keyspaces routinely hold password hashes, tokens, and PII in columns named hopefully. CQL being SQL-shaped also means the classic SQL-injection grammar applies wherever an app concatenates CQL — with no prepared-statement discipline, the injection surface is the same as MySQL's, minus the stacked-query conveniences of some drivers.

## The anatomy that matters on an engagement

```
app (any driver: python/java/go) ──CQL binary protocol──► node :9042
                                  (★ AllowAllAuthenticator: no auth handshake)
                                                             ├─ system / system_schema  (cluster map, all table defs)
                                                             ├─ system_auth.roles        (hashes — only if auth enabled)
                                                             └─ app keyspaces            (creds, tokens, PII)
node :7000/7001 gossip ◄──► ring peers  (inter-node, usually unencrypted by default)
```

Structural properties that drive findings:

1. **Auth off by default, and even "on" ships a default account** — `cassandra/cassandra` is the bootstrap superuser under PasswordAuthenticator; unchanged credentials are a standard finding on otherwise-hardened clusters.
2. **9042 binds to `rpc_address` (default localhost)** — but Docker, Kubernetes, and any multi-client deployment sets `0.0.0.0`, converting a private-ring assumption into an internet-facing CQL endpoint.
3. **UDFs are a second execution surface** — user-defined functions run JavaScript (Nashorn) on the JVM; when an admin enables scripted UDFs *and* disables the UDF thread sandbox, CVE-2021-44521 turns a plain authenticated CQL session into OS command execution.

## Where you'll meet it

- **Microservice / big-data backends** — recommendation engines, IoT telemetry, session stores; 9042 exposed on an internal segment where "internal" is doing all the security work.
- **Containerized clusters** — the official Docker images and Helm charts publish 9042; a foothold in the cluster network is a walk-in.
- **Legacy Thrift-era deployments** — very old clusters may still expose 9160 (Thrift) and 7199 (JMX, no auth by default), the latter a direct route to MBean-based code execution.
- **Cloud-managed variants** — AWS Keyspaces / DataStax Astra expose the CQL protocol with real auth; the CQL skills transfer, the default-cred attacks don't.

## Reading the daemon's mind

- nmap shows `9042/tcp open cassandra` (sometimes `unknown` with a binary protocol fingerprint); version leaks in the `READY`/`SUPPORTED` handshake exchange.
- `cqlsh 198.51.100.x` with no `-u/-p` connecting cleanly = AllowAllAuthenticator confirmed; `DESCRIBE KEYSPACES` then `SELECT * FROM system_schema.tables` maps everything.
- If auth is on: try `cassandra/cassandra` (bootstrap superuser) before any brute force; hashes in `system_auth.roles` are bcrypt-era and replayable offline once readable.
- `enable_user_defined_functions` defaults to **false** — CVE-2021-44521 (fixed 3.0.26 / 3.11.12 / 4.0.2) needs the non-default trio: UDFs on, *scripted* UDFs on, UDF threads off.
- JMX on 7199 (default no auth) is often the softer target than 9042 itself once you're on the host network.

## Where to go next

- [[cassandra-bug-bounty]] — full method: 9042 recon → anonymous cqlsh walk-in → schema/secret enumeration → default-cred and hash attacks → CVE-2021-44521 UDF sandbox escape → JMX fallback.
