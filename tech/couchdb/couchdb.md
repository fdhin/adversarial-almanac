---
technology: "CouchDB"
kind: overview
audience: offensive-security
family: databases
layer: data store
language: Erlang
homepage: https://couchdb.apache.org
github-repo: https://github.com/apache/couchdb
provenance: "corpus-SILENT after strict re-gate (structure-pass estimate of 3 writeups was substring noise: disassembler line numbers '5984 BACreateFile', an NTLM-hash hex fragment, a file size '15984' — no writeup attacks CouchDB; see internal coverage ledger) — upstream-verified only (CouchDB official CVE pages for 2017-12635/12636, docs.couchdb.org security intro + server API, discoverer writeup justi.cz) + rewritten from HackTricks (src/network-services-pentesting/5984-pentesting-couchdb.md), CC BY-NC 4.0, accessed 2026-11-13 (CVE-2018-8007 admin-to-OS-user escalation verified against NVD; adds 6984 TLS port, scheduler/replication disclosure endpoints, nmap/metasploit scanner coverage)"
---

# CouchDB

> An HTTP-native JSON document database where "admin" is a config entry, not a gate — a 1.x/2.x install with an empty `[admins]` section is in Admin Party mode (every request is a superuser), and the 2017 parser-mismatch CVEs turned user self-registration into admin and admin into shell.

## What it is

Apache CouchDB is a document-oriented database written in Erlang, originally by Damien Katz (IBM, later CouchOne/Cloudant lineage), Apache-hosted since 2010. Its defining trait for an operator: **the entire database is an HTTP API**. Port **5984** serves JSON over REST — documents, users, configuration, everything — with the Fauxton web console at `/_utils`. Documents are JSON blobs with MVCC revisions; "design documents" carry views written in JavaScript that run in a sandboxed query server.

Two structural facts drive nearly every finding. First, **the admin is not a user, it's a configuration state**: on CouchDB 1.x/2.x, until someone adds an entry under `[admins]` in local.ini (or via the config API), *every* client is an admin — the community called this Admin Party mode, and it persisted through thousands of tutorial deployments. Modern 3.x standalone servers require an admin before startup completes, but upgraded or containerized installs that never set one are the direct descendants of the same mistake. Second, **two parsers parsed the same JSON**: the Erlang-side jiffy parser and the JavaScript parser used by validation functions disagreed about duplicate keys, and that disagreement was CVE-2017-12635 — an unauthenticated privilege escalation present in *all* CouchDB versions released before 1.7.1/2.1.1 (November 2017).

The escalation chain that made it famous: 12635 gives any anonymous user the `_admin` role; CVE-2017-12636 then lets an admin point "query server" configuration at arbitrary OS binaries — the config API writes paths of executables that CouchDB will spawn. Together: unauthenticated RCE as the CouchDB user, used against npm's own registry infrastructure the week it dropped.

## The anatomy that matters on an engagement

```
client ──HTTP/JSON──► :5984
                      ├─ GET /                      → {"couchdb":"Welcome","version":...}   (fingerprint)
                      ├─ GET /_all_dbs              → every database, no auth if party/3.x-open
                      ├─ /_users org.couchdb.user:*  (★ 12635: duplicate "roles" keys self-admin)
                      ├─ /_utils                    → Fauxton console
                      └─ /_node/_local/_config      (admin-gated; ★ 12636: query_server paths = execv)
```

Structural properties that drive findings:

1. **Admin Party default (1.x/2.x)** — no `[admins]` entry means read-write *config* access for everyone, including the endpoints that spawn binaries.
2. **Config API is the RCE primitive** — `/_node/{node}/_config` admin-gates exactly the settings (query servers, external processes) that end in process execution; an admin anywhere is code execution somewhere.
3. **The HTTP surface is uniform and scriptable** — every object (db, doc, user, config) is a URL; enumeration is `GET` on predictable paths, so exposure audits are cheap and thorough.

## Where you'll meet it

- **Sync/backends for mobile and offline-first apps** (PouchDB on the client syncing to CouchDB behind nginx) — the replication-friendly design that made it popular also made deployments where 5984 faces more network than intended.
- **npm-era Node.js infrastructure** — the registry ran on CouchDB; legacy self-hosted mirrors and internal registries are the 12635/12636 sweet spot (all pre-2018 versions vulnerable).
- **IoT / home-automation platforms and legacy SaaS** — long-lived appliances shipping an old CouchDB with no admin configured.
- **Post-compromise config dumps** — `local.ini` and `_config` responses carry hashed admin passwords (`-pbkdf2-...`) worth offline cracking.

## Reading the daemon's mind

- `GET /` returns version in JSON (`"couchdb":"Welcome"`, `"version":"3.x.y"`) — pre-1.7.1/2.1.1 version strings mean unauth→admin→RCE is live (12635+12636).
- Admin-gated (not party-gated) actions per the docs: create/delete DB, `/_security` writes, design-doc writes, `/_active_tasks`, `/_node/{node}/_restart`, and read/write of `/_node/{node}/_config` — probe these to distinguish Party mode from a merely mis-scoped deployment.
- `_users` documents (`org.couchdb.user:<name>`) carry the roles that authorize everything; by design users cannot assign their own roles — 12635 broke exactly that invariant.
- Admin passwords hash as pbkdf2 with the raw value written to local.ini *first* and hashed on next write — config dumps sometimes still hold plaintext.
- 3.x binds localhost by default and demands an admin in setup — an exposed 5984 on 3.x is a deliberate exposure decision (reverse proxy, k8s service), not a default.

## Where to go next

- [[couchdb-bug-bounty]] — full method: fingerprint → Admin Party takeover → CVE-2017-12635 duplicate-roles escalation → CVE-2017-12636 query-server RCE → config/hash looting → hardening gaps.
