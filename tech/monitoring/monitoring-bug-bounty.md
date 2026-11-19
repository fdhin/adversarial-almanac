---
technology: "Monitoring stacks (Prometheus/Grafana/Zabbix/Nagios-class)"
kind: bug-bounty-methodology
audience: ai-agent
family: network-services
version: "1.0"
last-refresh: "2026-11-19"
status: upstream-only
provenance: "upstream-verified methodology, 2026-11-19: Prometheus querying/administrative-api and security docs; JFrog 'Don't let Prometheus steal your fire' (2021) + Aqua '300,000 Prometheus servers and exporters exposed' (2024); CVE-2026-42151 (Prometheus Azure AD remote_write client_secret disclosure); Zabbix ZBX-25623 / CVE-2024-42327 (CVSS 9.9 user.get SQLi); Grafana CVE-2021-43798 / CVE-2023-5122 / CVE-2025-3454; Nagios CVE-2024-54961 / CVE-2024-13995 and the classic status.cgi disclosure (Acunetix / CVE-2013-2214). Companion overview [[monitoring]]; product-family depth in [[monitoring-zabbix]], [[monitoring-cacti]], [[grafana]]."
source: "WebSearch/WebFetch research 2026-11-19 against the primary advisories, vendor docs, and the two exposure-census writeups named above; every command verified against current product documentation. Model claude-opus-4-8."
---

Companion pages: [[monitoring|Monitoring platforms (area router)]] — the architecture and the credential-reuse-into-RCE pattern that frames everything below. Product-family depth lives in [[monitoring-zabbix|Zabbix methodology]], [[monitoring-cacti|Cacti methodology]], and [[grafana|Grafana methodology]]. Tool notes live in the separate tools vault; tools are named in prose here.

---

# Observability-Stack Bug Bounty Methodology

**A bug hunter's field guide to the monitoring tier — v1.0 (2026-11-19)**

**In scope:** the *read-all* observability stack — **Prometheus** (and its exporters: node_exporter, cAdvisor, blackbox_exporter) on 9090/910x, **Grafana** on 3000, **Zabbix** frontend/API, and the **Nagios family** (Core `status.cgi`, Nagios XI). The unifying property: these systems are *built* to see every host, every label, every credential-bearing scrape target in the estate. Their exposure is not "a dashboard is public" — it is **infrastructure disclosure**.

**Out of scope here:** the monitored backends' own surfaces (the MySQL/Postgres/Elasticsearch a Grafana datasource points at — those have their own pages), agent-protocol RCE chains already in [[monitoring]], and Grafana's deep traversal/db-crack chain already in [[grafana]]. This page is the cross-family *exposure-class* map; the product pages are the depth.

**Authorization discipline:** everything below is single-request, low-volume, evidence-first. A Prometheus `/api/v1/query` is a read; a `POST /api/v1/admin/tsdb/snapshot` writes to disk and the admin group *also* holds `delete_series` — never fire the destructive siblings, and prove the admin API is live with the snapshot read alone. Queries routed through a Grafana datasource proxy execute on production backends: bounded enumeration, no destructive SQL.

## The one sentence that sets severity

**A monitoring server stores working credentials for everything it watches and, by design, reaches into everything it watches.** So a read primitive against the monitoring tier is rarely "info disclosure, Low" — it is a map of internal hostnames, software versions, environment variables, scrape-target credentials, and the topology of the estate, handed over in one unauthenticated request. Frame the impact as *infrastructure disclosure of the monitored fleet*, and the baseline severity is **High–Critical**, not Informational. The triager's instinct ("it's just metrics") is the thing your report has to pre-empt.

### Where the money is (triage priority)

| Priority | Class | Where it lives | Typical severity |
|---|---|---|---|
| 1 | Unauthenticated Prometheus (`:9090`) | no auth by default — `/api/v1/*` is wide open | High–Critical |
| 2 | Scrape-target credentials in config | `/api/v1/status/config` → `scrape_configs`, remote_write secrets | Critical |
| 3 | Metrics-as-secrets | labels carrying env vars, internal FQDNs, tokens, versions | High |
| 4 | Admin-API snapshot/backup abuse | `--web.enable-admin-api` → full TSDB snapshot | High–Critical |
| 5 | Zabbix API infoleak / SQLi | guest + `user.get` SQLi (CVE-2024-42327) → session theft | Critical |
| 6 | Grafana default/anonymous access | `admin:admin`, `[auth.anonymous]` Viewer, dashboard JSON | High |
| 7 | Grafana datasource-proxy SSRF/pivot | proxy reaches internal backends & cloud metadata | High |
| 8 | node_exporter / exporter `/metrics` | host internals, `/debug/pprof` on `:9100` | Medium–High |
| 9 | Nagios `status.cgi` / XI infoleak | host inventory, users, API keys | Medium–High |
| 10 | Version disclosure → CVE targeting | every console prints its build pre-auth | Low (amplifier) |

Phase 0 fingerprints the family. Phases 1–4 are the four product families. Phase 5 is the cross-family finding (metrics-as-secrets) that pays on *all* of them. Phase 6 is snapshot/backup abuse. Then reporting, severity, CTF.

---

## Phase 0 — Fingerprint the stack before you touch it

The four families look alike in a port scan and their playbooks are disjoint. Pin the product first.

```bash
# The ports that say "monitoring tier"
nmap -p 3000,8443,9090,9091,9093,9100,9115,10050,10051 -sV 198.51.100.0/24

# Prometheus — unauthenticated liveness + self-identification
curl -s http://198.51.100.10:9090/-/healthy            # -> "Prometheus Server is Healthy."
curl -s http://198.51.100.10:9090/api/v1/status/buildinfo | jq .   # version, revision, go version

# node/other exporter — the bare /metrics tell
curl -s http://198.51.100.20:9100/metrics | head        # node_exporter: node_* metric families
curl -s http://198.51.100.10:9090/metrics | head        # Prometheus self-metrics: prometheus_*

# Grafana — branded liveness (3000)
curl -s http://198.51.100.30:3000/api/health            # {"database":"ok","version":"...","commit":"..."}

# Zabbix / Nagios frontends
curl -s http://198.51.100.40/zabbix/api_jsonrpc.php -H 'Content-Type: application/json-rpc' \
  -d '{"jsonrpc":"2.0","method":"apiinfo.version","id":1,"params":{}}'   # -> {"result":"7.0.4"} unauthenticated
curl -si http://198.51.100.50/nagios/cgi-bin/status.cgi | head          # basic-auth realm or open status
```

| You observe | Family | Go to |
|---|---|---|
| `9090/tcp`, `/-/healthy`, `prometheus_*` self-metrics, `/api/v1/status/buildinfo` | Prometheus | Phase 1 |
| `9100`/`9115`/`8080` serving bare `node_*`/`probe_*`/`container_*` metrics | Exporters | Phase 1.4 + Phase 5 |
| `3000/tcp`, `<title>Grafana</title>`, `/api/health` JSON | Grafana | Phase 2 → [[grafana]] |
| `/api_jsonrpc.php`, `apiinfo.version` answers, "© … Zabbix SIA" footer | Zabbix | Phase 3 → [[monitoring-zabbix]] |
| `/nagios/`, `status.cgi`, `/nagiosxi/` paths | Nagios / XI | Phase 4 |

`apiinfo.version` is the one Zabbix method callable **without a session** — it is a free, pre-auth version string that gates every Zabbix CVE below. Likewise `buildinfo` and `/api/health` hand you the exact Prometheus/Grafana build before you authenticate anything. Record each verbatim; they are both your fingerprint and your CVE-matching index.

---

## Phase 1 — Prometheus: the unauthenticated query surface

**The fact that defines the whole family: Prometheus ships with no authentication and no TLS.** The upstream security model is explicit — "it is presumed that untrusted users have access to the Prometheus HTTP endpoint" is *not* the design; the design assumes you put it behind a trusted boundary and most operators don't. Aqua's 2024 census found ~40,000 internet-facing Prometheus servers and ~296,000 exposed exporters. If you can reach `:9090`, you can read everything it holds.

### 1.1 The query API as reconnaissance

`/api/v1/query` and `/api/v1/query_range` are the data plane; `/api/v1/*` metadata endpoints are the intel plane. None need a credential on a default install.

```bash
P=http://198.51.100.10:9090

# What is being monitored, and from where — scrape targets with their resolved addresses
curl -s "$P/api/v1/targets" | jq '.data.activeTargets[] | {job:.labels.job, inst:.labels.instance, url:.scrapeUrl, health}'

# Every label name, then every value of the juicy ones — a map of the estate
curl -s "$P/api/v1/label/__name__/values" | jq '.data | length'      # how many distinct metrics
curl -s "$P/api/v1/label/instance/values" | jq .                      # internal hostnames/IPs:ports
curl -s "$P/api/v1/label/job/values"      | jq .                      # service inventory

# Series matching — pull everything carrying a given label dimension
curl -s "$P/api/v1/series" --data-urlencode 'match[]={__name__=~".+"}' | jq '.data | length'

# Point query — read an actual value (uptime, version gauges, build_info)
curl -s "$P/api/v1/query" --data-urlencode 'query=node_uname_info' | jq '.data.result[].metric'
```

The `instance` label is the inventory: internal FQDNs and `ip:port` pairs for every scraped host, exporter, and sidecar. `*_build_info` and `*_version` gauges carry exact software versions as **label values** — Prometheus turns the whole fleet's version matrix into a queryable table. That is a targeting list for every *other* finding on the engagement.

### 1.2 The config endpoint — scrape-target credentials

The single highest-value Prometheus read:

```bash
curl -s "$P/api/v1/status/config" | jq -r '.data.yaml' | tee prom-config.yaml   # == the /-/config UI view
grep -nE 'password|token|bearer|credentials|authorization|_url|basic_auth' prom-config.yaml
```

Prometheus **redacts** `password`/`credentials` fields to `<secret>` in this output — but two leaks survive:

1. **Usernames are not masked** (`basic_auth.username` is shown in full), and the structure tells you exactly which backend each credential unlocks.
2. **Credentials embedded in a URL string are not redacted at all** — a `scrape_config` or `remote_write`/`remote_read` target written as `https://user:pass@metrics.internal/…` leaks the full secret, as does any `params:` carrying a token.

This is not theoretical: **CVE-2026-42151** (current advisory) is exactly this class — the Azure AD `remote_write` OAuth `client_secret` was typed as a plain string, so Prometheus's redaction skipped it and the plaintext secret was served from `/-/config` (hence `/api/v1/status/config`) to **any unauthenticated caller**. Fixed in Prometheus 3.5.3 (LTS) and 3.11.3; on an unpatched target with Azure AD remote-write, one unauthenticated GET is the whole PoC. Gate it on the `buildinfo` version from Phase 0.

### 1.3 Federation — bulk metric export

```bash
# /federate dumps every series matching the selector in one shot — the batch version of 1.1
curl -s "$P/federate" --data-urlencode 'match[]={__name__=~".+"}' -o federate.txt
wc -l federate.txt    # often tens of thousands of lines: the entire fleet's metric state
```

`/federate` is unauthenticated on the same default install and is the fastest way to snapshot the whole semantic model of the environment for offline mining (Phase 5).

### 1.4 Exporters — the host-internal leak on `:9100`

Exporters are even more exposed than the server: a `node_exporter` on `:9100` serves `/metrics` to anyone, and the same census work found hundreds of thousands reachable. The leak is host-internal detail, not aggregate graphs:

```bash
E=http://198.51.100.20:9100
curl -s $E/metrics | grep -E '^node_uname_info|^node_dmi_info|^node_os_info|^node_network_|^node_filesystem_'
# node_uname_info{...nodename, release, version, machine} — kernel + hostname
# node_dmi_info{...} — BIOS vendor/version, board/product name (cloud vs bare-metal, hypervisor)
# node_network_* / node_filesystem_* — interfaces, internal IPs, mount points, device names
curl -s $E/debug/pprof/ | head     # pprof is reachable on :9100 by default — goroutines, cmdline
```

`node_uname_info` and `node_dmi_info` hand over hostname, kernel, and hardware/hypervisor identity per host. `blackbox_exporter` (`:9115`) is worse as a *pivot*: it is a built-in SSRF primitive — `GET /probe?target=http://169.254.169.254/&module=http_2xx` makes the exporter fetch a destination you choose. The metasploit module `auxiliary/gather/prometheus_node_exporter_gather` automates the host-fact harvest; `nuclei`'s exposed-panel templates flag the servers. Decision point: an exposed exporter with a `blackbox`/`http` probe module is an SSRF report *today*, before you look at a single gauge.

**Prometheus decision tree:** reachable `:9090` → dump `targets`+`config`+`federate` (Phases 1.1–1.3) → is Azure AD remote-write present and version < 3.5.3/3.11.3? → CVE-2026-42151 secret read. Admin API enabled (Phase 6)? → snapshot. Exporters reachable → host-internal leak + blackbox SSRF. Everything after the config read feeds Phase 5.

---

## Phase 2 — Grafana: access gates and the proxy pivot

Grafana's deep chains (CVE-2021-43798 unauth traversal → `grafana.db` crack; CVE-2024-9264 DuckDB RCE) live in [[grafana]]. Here is the *exposure-class* layer that matters when you meet Grafana as part of the observability tier.

- **Default-login gate.** `admin:admin` with the first-login change deferred/dismissed on misconfigured installs. One working login is enough — Grafana's value is what the logged-in session can *read*.
- **Anonymous-access org access.** `[auth.anonymous] enabled = true` grants the `Viewer` org role with no login at all (kiosk/TV-wall deployments do this constantly). A Viewer already sees every dashboard in the org — which is the intel below — and on 11.x is enough for the CVE-2024-9264 path.
- **Dashboard JSON as infrastructure intel.** Every dashboard is a JSON model of panels, and each panel embeds its datasource reference and often raw backend queries (SQL, PromQL, LogQL) with **real table names, schema, internal hostnames, and sometimes credentials in URLs**. Pull it with any Viewer session:

```bash
G=http://198.51.100.30:3000
curl -s -H "Authorization: Bearer $GRAFANA_TOKEN" "$G/api/search?type=dash-db" | jq -r '.[].uid' |
while read uid; do curl -s -H "Authorization: Bearer $GRAFANA_TOKEN" "$G/api/dashboards/uid/$uid"; done > dashboards.json
jq -r '.. | .rawSql? // .expr? // empty' dashboards.json | sort -u    # every embedded backend query
curl -s -H "Authorization: Bearer $GRAFANA_TOKEN" "$G/api/datasources" | jq '.[] | {name,type,url,user}'   # backend map
```

- **Datasource-proxy SSRF class.** `/api/datasources/proxy/<id>/...` makes Grafana's *server* issue the request to the configured backend, so it is a standing SSRF/pivot primitive: the proxy reaches internal-only databases and HTTP APIs the datasource URL points at, and a bare-host/crafted datasource config has repeatedly turned it into a general SSRF (CVE-2023-5122, SSRF in the CSV datasource plugin; CVE-2025-3454, an authorization bypass in the data source proxy that let a caller reach backends it shouldn't). Treat any datasource whose `url` resolves to internal space or cloud metadata as a pivot to test under the program rules. Depth and the SQLi-through-proxy chain: [[grafana]].

Grafana severity framing: the dashboards + datasource list are *already* infrastructure disclosure (High) before any CVE; the proxy turns that into lateral reach.

---

## Phase 3 — Zabbix: guest, API infoleak, JS-item RCE

Full agent/`system.run` chains and SQLi depth are in [[monitoring-zabbix]]; the exposure classes:

- **Guest access.** Zabbix historically ships a `guest` user; where `[Guest]` retains frontend access (older installs, or a re-enabled guest group) you get read access to hosts, maps, and inventory with no credentials. Combined with the documented default `Admin:zabbix`, the login gate is often not a gate.
- **API as an infoleak surface.** The JSON-RPC API (`/api_jsonrpc.php`) is the fast path: `apiinfo.version` is pre-auth (Phase 0); after any login, `host.get`, `hostinterface.get`, `item.get`, and `usermacro.get` enumerate the entire monitored estate — and **user macros (`{$...}`) routinely store the very credentials Zabbix monitors with** (DB passwords, SNMP communities, API tokens).

```bash
Z=http://198.51.100.40/zabbix/api_jsonrpc.php
# after user.login returns $AUTH — low-priv is enough for the reads
curl -s $Z -H 'Content-Type: application/json-rpc' -d '{"jsonrpc":"2.0","method":"usermacro.get",
  "params":{"output":"extend","globalmacro":true},"auth":"'"$AUTH"'","id":1}' | jq .
```

- **`user.get` SQL injection — CVE-2024-42327 (CVSS 9.9).** The headline current-era Zabbix bug: SQL injection in `CUser::addRelatedObjects`, reachable from the `user.get` API by **any account with API access, including the default `User` role** — i.e. a non-admin. Public PoCs leak user credentials and **session tokens** from the DB through the injection, then authenticate as admin with the stolen session and reach `system.run`/script execution for a reverse shell. Affects 6.0.0–6.0.31, 6.4.0–6.4.16, 7.0.0; fixed 6.0.32 / 6.4.17 / 7.0.1 (ZBX-25623). Gate on the `apiinfo.version` string.
- **JS-item / `system.run` RCE history (one line).** Zabbix's legitimate "run a check on a host" features — agent `system.run` items, global scripts, and JavaScript preprocessing/JS items — are the execution sinks once you hold a sufficiently privileged session; see [[monitoring-zabbix]] for the chain.

---

## Phase 4 — Nagios-class: the status.cgi classic

The Nagios family splits into Core (CGI frontend) and the commercial Nagios XI; both leak host inventory before they leak anything exploitable.

- **`status.cgi` information disclosure (the classic).** `GET /nagios/cgi-bin/status.cgi?host=all` renders the complete monitored-host inventory — hostnames, services, states, and often check-command detail. When the CGI directory sits behind weak or absent basic auth (or an authenticated low-priv contact can see hosts they aren't a contact for, the CVE-2013-2214 shape), it is a full map of the estate. Acunetix ships a dedicated "Nagios status.cgi information disclosure" check precisely because open/weakly-gated `status.cgi` is endemic.

```bash
N=http://198.51.100.50
curl -s "$N/nagios/cgi-bin/status.cgi?host=all" -u guest:guest | grep -oE 'host_name=[^"&]+' | sort -u
curl -s "$N/nagios/cgi-bin/extinfo.cgi?type=0&host=localhost" -u guest:guest   # per-host detail, plugin output
```

- **Nagios XI — modern infoleak CVEs (one line each).** CVE-2024-54961: **unauthenticated** access to pages leaking usernames and emails of current users (XI 2024R1.2.2). CVE-2024-13995: exposure of **API keys and hashed passwords** (before 2024R1.1.2); CVE-2024-13999 leaks AD/LDAP tokens. These convert "I can see the login page" into credential material. Depth on XI's token-auth quirks and the `banner_message` SQLi (CVE-2023-40931) is in [[monitoring]].

Nagios decision point: open `status.cgi` = inventory disclosure report on its own; an XI banner + version string → match the 2024-era infoleak CVEs before anything heavier.

---

## Phase 5 — The cross-family finding: metrics are secrets

This is the finding that pays on *every* product above, and the one triagers underrate. Monitoring labels and dashboard queries are an uncurated dumping ground for exactly the data an attacker wants, because developers treat metric labels as "internal telemetry" and never sanitize them.

Mine every corpus you collected (Prometheus `/federate` + `/api/v1/series`, Grafana dashboards JSON, Zabbix `item.get`/`usermacro.get`, Nagios plugin output) for these shapes:

```bash
# Secrets and identity hiding in metric/label text
grep -ioE '(password|passwd|secret|token|api[_-]?key|bearer|aws_|sk_live_|AKIA[0-9A-Z]{16})[^ "]*' federate.txt dashboards.json
# Internal topology: FQDNs, RFC1918, cloud-internal names
grep -ioE '([a-z0-9-]+\.)+(internal|corp|local|svc\.cluster\.local)|10\.[0-9.]+|192\.168\.[0-9.]+|172\.(1[6-9]|2[0-9]|3[01])\.[0-9.]+' federate.txt
# Environment variables surfaced as labels (process_* / *_env / build_info)
grep -iE '\{.*(env|environment|commit|branch|image|registry|version)=' federate.txt | head
```

Four recurring leaks, in payout order:

1. **Credentials in labels.** A `scrape_config` that copies a target's env into labels, a custom exporter that exposes config, or a dashboard query string with a token baked in. `AKIA…`, `sk_live_…`, bearer tokens, DB DSNs — all seen in the wild in metric text.
2. **Internal hostnames / topology.** The `instance` label set *is* the internal DNS map; `kube_pod_*`, `container_label_*`, and `node_dmi_info` expose cluster/namespace/image/hypervisor layout. This alone is High-value recon.
3. **Exact software versions.** `*_build_info`, `node_uname_info`, `go_info`, agent version gauges — the whole fleet's patch posture as a queryable table, which is the targeting list for the rest of the engagement.
4. **Business-sensitive counters.** Revenue, user counts, queue depths, feature-flag states — sometimes the sensitive-data finding in its own right for a bounty program.

Report framing: do not report "metrics are exposed." Report "the monitoring tier discloses `<the specific internal hostnames / the AWS key / the fleet version matrix>`," with the exact query and the exact lines as evidence. The specificity is what moves it from Informational to High.

---

## Phase 6 — Snapshot and backup endpoints

Monitoring stores history, so it offers bulk-export endpoints that turn a read primitive into *the entire dataset* — and some write to disk.

- **Prometheus admin API — TSDB snapshot.** `POST /api/v1/admin/tsdb/snapshot` creates a full snapshot of the TSDB under `data/snapshots/<name>/` and returns the directory name. It is only reachable when Prometheus was started with `--web.enable-admin-api` (otherwise `403 {"status":"error",...}` — itself a clean negative). JFrog's census found ~15% of exposed instances had the admin API enabled and ~4% had DB management on. **Prove it with the snapshot call alone** and stop — the same admin group also exposes `delete_series` and `clean_tombstones`, which are destructive; do not touch them.

```bash
# Non-destructive proof the admin API is live (read the returned snapshot name; do NOT call delete_series)
curl -s -XPOST "$P/api/v1/admin/tsdb/snapshot" | jq .
# -> {"status":"success","data":{"name":"20261119T120000Z-<hash>"}}   == full historical dataset on disk
```

- **Grafana snapshots / public dashboards.** `/api/snapshots` and the public-dashboard toggle export panel *results* to externally shareable links without requiring the viewer to hold datasource access — an authenticated user can leak sensitive panel data out of the org boundary (functionality-level risk; no CVE needed).
- **Zabbix / Nagios config exports.** Zabbix configuration export (`configuration.export` API, or the UI XML export) and Nagios config/backup dumps carry host credentials and macros; a readable export is the credential store in one file.

Severity: the snapshot/export is Critical when it contains credentials or cross-tenant data, because it is the *complete* historical record, not a sampled view.

---

## Reporting and evidence standards (per family)

The report is the product. Monitoring findings live or die on showing that the data is real and *sensitive*, captured at minimum volume.

| Family | Minimum evidence | Do NOT |
|---|---|---|
| Prometheus | one `/api/v1/status/config` (or `/targets`) response showing a real internal host/credential; `buildinfo` for the version claim | run `delete_series`; snapshot repeatedly; `/federate` more than once |
| Exporter | the `node_uname_info`/`node_dmi_info` lines; one `blackbox` `/probe?target=` to an **in-scope** canary for the SSRF PoC | scan the internal net through blackbox |
| Grafana | the dashboard JSON line with the backend query/host; the `/api/datasources` entry proving the internal `url` | run SQL through the proxy on prod backends beyond a bounded `SELECT 1` |
| Zabbix | `apiinfo.version` + the `user.get` error/response proving the injection; one leaked macro name (redact the value) | dump the full user table; replay stolen admin sessions beyond proof |
| Nagios | the `status.cgi?host=all` host list (redacted) + the auth state that let you read it | enumerate every host page |

Universal rules: **one request per step**, expected-vs-actual beside each, redact the secret *value* while proving its *presence and location*, clean up any snapshot you created, and never exfiltrate more than the proof needs. For a credential you read, note "credential present and readable at `<endpoint>`" — do not use it against the backend unless the program explicitly authorizes the pivot.

## Severity framing

Monitoring exposure is usually **Critical** and here is the argument to hand the triager, because the reflex undervaluation ("just a dashboard / just metrics") is predictable:

- **Read-all visibility is the multiplier.** A single unauthenticated Prometheus/exporter/`status.cgi` read is not one host's worth of disclosure — it is the *fleet's* internal DNS, version matrix, and topology. That is High on recon value before a single credential appears.
- **Credential material = Critical.** Scrape-target creds (`status.cgi`-adjacent config, Zabbix macros, Grafana datasource URLs, XI API keys) are reusable *off the monitoring box* — they unlock the monitored estate. Map to the program's "exposure of secrets / credentials" tier.
- **By-design execution = Critical.** Zabbix `system.run`, Grafana's DuckDB/proxy paths, Nagios check commands — monitoring converts console/API access into code execution on monitored hosts as a feature. Cite the chain even when you stop at the read.
- **SSRF/pivot = High.** blackbox_exporter probes and Grafana datasource proxy reach internal-only services and cloud metadata.

When a program's rubric would cap a "monitoring console exposed" finding at Medium, the lever is the *specific* secret or the *specific* internal map you extracted — attach it, and argue infrastructure disclosure, not misconfiguration.

## CTF notes

- **`:9090` is a flag vending machine.** On CTF/HTB-style boxes an exposed Prometheus almost always wants `/api/v1/status/config` (creds in `scrape_configs`) or a label/series query that leaks an internal vhost you then add to `/etc/hosts`. `curl -s $P/api/v1/label/instance/values` is the fast first move.
- **Exporters pin the pivot.** A `node_exporter`/`blackbox_exporter` on `:9100`/`:9115` is usually the intended SSRF/next-host hint — `node_uname_info` gives the box's real hostname; a `blackbox` `/probe?target=` reaches an internal service the challenge wants you to find.
- **Grafana → datasource → backend** is the canonical chain: default/anonymous login → read dashboard JSON for the DB name/creds → datasource proxy or direct backend login. The deep traversal (CVE-2021-43798) and DuckDB RCE (CVE-2024-9264) are in [[grafana]] and show up on version-pinned boxes.
- **Zabbix** boxes lean on `Admin:zabbix`, guest access, or the `user.get` SQLi (CVE-2024-42327) to lift a session, then `system.run` for the shell — the [[monitoring-zabbix]] chain verbatim.
- **Nagios** CTFs use open `status.cgi` for host discovery, then an XI/Core version-gated RCE; the status page is the recon step, not the flag.

## References

- Prometheus querying API (`/api/v1/*`, `/federate`) — https://prometheus.io/docs/prometheus/latest/querying/api/
- Prometheus administrative API & snapshot (`--web.enable-admin-api`, `/api/v1/admin/tsdb/snapshot`) — https://prometheus.io/docs/prometheus/latest/querying/api/#tsdb-admin-apis
- Prometheus security model (no auth/TLS by default; trusted-boundary assumption) — https://prometheus.io/docs/operating/security/
- JFrog, "Don't let Prometheus steal your fire" (config endpoint `<secret>` redaction gaps; ~15% admin-API / ~4% DB-management exposed) — https://jfrog.com/blog/dont-let-prometheus-steal-your-fire/
- Aqua Security, "300,000 Prometheus servers and exporters exposed" (census; `/metrics`, `/debug/pprof` on exporters) — https://www.aquasec.com/blog/300000-prometheus-servers-and-exporters-exposed-to-dos-attacks/
- CVE-2026-42151 — Prometheus Azure AD remote_write `client_secret` plaintext disclosure at `/-/config`; fixed 3.5.3 (LTS)/3.11.3 — https://www.wiz.io/vulnerability-database/cve/cve-2026-42151
- Metasploit `auxiliary/gather/prometheus_node_exporter_gather` — https://vulners.com/metasploit/MSF:AUXILIARY-GATHER-PROMETHEUS_NODE_EXPORTER_GATHER-
- Zabbix CVE-2024-42327 (user.get `CUser::addRelatedObjects` SQLi, CVSS 9.9; non-admin/default User role; ZBX-25623) — https://threatprotect.qualys.com/2024/11/29/zabbix-server-critical-sql-injection-vulnerability-cve-2024-42327/
- Zabbix tracker ZBX-25623 — https://support.zabbix.com/browse/ZBX-25623
- Grafana CVE-2021-43798 (unauth plugin-path traversal) & CVE-2024-9264 (SQL Expressions/DuckDB) — covered in [[grafana]]; Grafana security advisories — https://grafana.com/security/security-advisories/
- Grafana CVE-2023-5122 (CSV datasource plugin SSRF) — https://avd.aquasec.com/nvd/2023/cve-2023-5122/
- Grafana CVE-2025-3454 (data source proxy authorization bypass) — https://grafana.com/security/security-advisories/
- Nagios status.cgi information disclosure (Acunetix check; CVE-2013-2214 host-visibility shape) — https://www.acunetix.com/vulnerabilities/network/vulnerability/nagios-status-cgi-information-disclosure-vulnerability/
- Nagios XI CVE-2024-54961 (unauthenticated user/email disclosure) — https://nvd.nist.gov/vuln/detail/cve-2024-54961
- Nagios XI CVE-2024-13995 (API key & hashed-password exposure) — https://cve.circl.lu/vuln/cve-2024-13995

*Compiled 2026-11-19 from the Prometheus querying/administrative-API and security docs, the JFrog (2021) and Aqua (2024) exposure-census writeups, CVE-2026-42151 (Prometheus), Zabbix ZBX-25623/CVE-2024-42327, Grafana CVE-2021-43798/CVE-2023-5122/CVE-2025-3454, and Nagios CVE-2024-54961/CVE-2024-13995 plus the classic status.cgi disclosure — each command verified against current product documentation. Model claude-opus-4-8.*
