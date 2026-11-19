---
technology: Elasticsearch
kind: overview
audience: offensive-security
family: databases
status: "thin (3 writeup files pass the re-gated command-context gate — upstream carries the methodology)"
layer: distributed search/analytics engine (REST over HTTP on 9200/tcp, transport on 9300)
language: Java
homepage: "https://www.elastic.co/elasticsearch"
github-repo: "elastic/elasticsearch"
provenance: "corpus-grounded THIN (3 writeup files pass the re-gated command-context gate: ES REST paths (/_cat/.*, /_cluster/*, /_nodes*, /_search), :9200 with scheme, ELASTICURI env, Adminer elastic driver param; corpus exemplars: unauth 9200 with _cat/indices and _search exfil; loopback HTTPS 9200 with .env creds and seed/user indices; Adminer elastic-driver credential capture; structure-pass hint was 13 on bare 'elasticsearch'/'9200' markers — re-gate killed timestamp/hex/Solr/ELK-name-drop collisions, e.g. one dropped match was Solr not ES) + upstream-verified: NVD CVE-2014-3120 (default dynamic scripting, MVEL via _search source, pre-1.2), NVD CVE-2015-1427 (Groovy sandbox bypass, pre-1.3.8/1.4.3), Elastic blog 'Security for Elasticsearch is now free' (TLS/RBAC/file+native realms free at Basic tier since 6.8.0/7.1.0, 2019-05-20), Elasticsearch _cat API reference (/_cat lists commands), 2026-11-12."
---

# Elasticsearch

> A REST API that speaks JSON on 9200 with the entire dataset one `_search` away — and for most of its history, no authentication layer between the port and the data.

## What it is

Elasticsearch is the Elastic Stack's distributed document store and search engine: documents land in **indices**, and everything — health, index lists, node state, data — is reachable over its HTTP REST API on **9200/tcp** (nodes talk to each other on 9300). The mental model for an operator is "database with a web interface and no query language to learn": `GET /` identifies the cluster, `GET /_cat/indices?v` lists the databases, `GET /<index>/_search` dumps them. It is deployed as the storage tier of the ELK logging stack (with Logstash piping and Kibana visualizing — [[kibana]]), as an application search backend bound to loopback, and increasingly as a standalone data lake someone left on a cloud security group.

The default posture is the whole story: authentication, TLS, and role-based access control were **paid-only features until 6.8/7.1 (May 2019)**, when Elastic moved core security to the free Basic tier (upstream-verified). That means a huge installed base of pre-6.8 clusters — and every cluster whose operators never switched security on after it became free — answers anonymous HTTP. Even where security is enabled, the observed pattern is credentials leaking in `.env` files (`ELASTICURI=https://127.0.0.1:9200`, observed) and an authentication flow (Adminer's `elastic` driver) that an attacker can point at their own server to capture the username/password pair (field-observed).

This unit is **thin** in the observed record so the methodology leans on upstream ground truth: the REST surface is the attack surface, and the famous era-specific RCEs — CVE-2014-3120 (dynamic scripting on by default before 1.2: MVEL expressions through `_search`'s `source`) and CVE-2015-1427 (Groovy sandbox escape before 1.3.8/1.4.3) — define what an old banner means.

## The anatomy that matters on an engagement

    port scan → 9200/tcp (http)
      → GET /                      → version + cluster_name JSON   ← fingerprint fork
      → security on?  401/`missing authentication credentials`
                       vs full JSON anonymously                     ← the default-posture fork
      → GET /_cat/indices?v        → the databases, with doc counts
      → GET /_cluster/health       → green/yellow/red + version
      → GET /<index>/_search       → the data (credentials, tokens, PII)
      → old version (<1.2 / <1.4.3)? → scripting RCE era (CVE-2014-3120 / CVE-2015-1427)
      → creds found (env files, app config, captured auth flow) → authenticated data plane

Three structural properties drive most findings:

- **Everything is one HTTP verb away.** No client tooling needed — curl against `_cat`, `_search`, `_cluster` is the entire reconnaissance-and-exfil loop (observed: one chain walks index list → `_search` → base64 SSH credentials in three requests).
- **The version banner is a vulnerability clock.** `<1.2` = default-on dynamic scripting (CVE-2014-3120); `1.3.x<1.3.8 / 1.4.x<1.4.3` = Groovy escape (CVE-2015-1427); `>=6.8/7.1` = security *available* (not necessarily *enabled*).
- **The interesting index is rarely named `logs`.** Applications store seeds, sessions, backup keys, and user records in ES precisely because it's the convenient store at hand (observed: one deployment's `seed` and `user-00001` indices under loopback HTTPS).

## Where you'll meet it

- **Direct exposure on internal/external scans** — 9200 answering `GET /` anonymously; the 2010s' most common unauth-data-breach appliance. Expect logging clusters full of PII and credential-shaped events.
- **Loopback-bound application backends** — `.env`/config files on an adjacent shell yield `ELASTICURI` plus creds; the port never needed to face you (one observed chain used exactly this port-forward-then-curl pattern).
- **The ELK stack** — where ES appears, Kibana (5601) and Logstash pipeline configs are nearby; a Kibana RCE or a readable Logstash filter config is the companion finding (one observed chain runs ES data → Kibana LFI → Logstash-config RCE — see [[kibana]]).
- **Auth-adjacent applications** — Adminer-style database front-ends expose an `elastic` driver whose `server` parameter you can re-point at yourself: the app will hand its ES credentials to your listener (field-observed, upstream CVE-2021-21311 context).

## Reading the server's mind

1. **`GET /` returns `version.number` and `cluster_name` anonymously** unless auth intercepts — the fingerprint and the era check in one request (one observed target: cluster name `elasticsearch` confirmed the service from the browser).
2. **HTTP 401 with `missing authentication credentials`** means security is on — pivot to credential hunting (env files, app configs, the Adminer capture).
3. **`/_cat/indices?v` sorts your next hour**: doc counts identify the juicy index; `.kibana` tells you Kibana is in play.
4. **`/_cluster/health` echoes `number` (version) again** — corroborate the banner before trusting it.
5. **Self-signed HTTPS on 9200** (`curl -k https://127.0.0.1:9200`) is the modern loopback pattern — TLS ≠ auth; creds still decide.

## Where to go next

[[elasticsearch-bug-bounty]] — the phased agent methodology: fingerprint, auth fork, unauth enumeration, `_search` exfil patterns, the scripting-RCE era table, and credential-source hunting.
