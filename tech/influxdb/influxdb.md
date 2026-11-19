---
technology: InfluxDB
kind: overview
audience: offensive-security
status: upstream-only
family: databases
layer: data store
language: Go
homepage: https://www.influxdata.com
github-repo: https://github.com/influxdata/influxdb
provenance: "upstream-only; zero corpus evidence by design (owner-approved new-unit wave 2026-11-13) — CVE-2019-20933 range < 1.7.6 (patched 1.7.6, critical, CWE-287) verified against GHSA-2rmp-fw5r-j5qv and upstream issue 12927; CVE-2024-30896 (OSS 2.x through 2.7.11, operator-token disclosure from default-org authorization listing, CVSS 9.1) verified against the Wiz advisory entry; API endpoints per docs.influxdata.com + rewritten from HackTricks (src/network-services-pentesting/8086-pentesting-influxdb.md), CC BY-NC 4.0, accessed 2026-11-13"
---

# InfluxDB

> The time-series database that is "just an HTTP API on 8086" — where a 1.x build with the JWT shared secret left empty authenticates a forged admin token, and where a 2.x all-access token in the default org can list the instance-wide operator token.

## What it is

InfluxDB (InfluxData; Go in the 1.x–2.x era that dominates exposed installs) is a time-series database: timestamp–value–tag points grouped into **measurements** (the table analogue) within databases/buckets, queried in **InfluxQL** (1.x, SQL-like) or **Flux** (2.x, a piped scripting language). Everything — writes, queries, administration — travels as HTTP on **8086**, which is why every InfluxDB assessment is a `curl` exercise: `/query` and `/write` in the 1.x API, `/api/v2/*` in 2.x, and the free `/ping` (1.x, returns `204` plus an `X-Influxdb-Version` header) and `/health` (2.x, JSON version) version oracles on both.

Its security history is an era map. **1.x deployments** may run with authentication disabled entirely (world-readable telemetry, and world-*writable* — including user creation), or with basic/JWT auth where the classic bug lives: **CVE-2019-20933** — when the HTTP service's JWT shared secret is empty (the misconfiguration, not the patched default of any supported line), any client can forge a validly-signed admin JWT; all versions **before 1.7.6** are affected, patched in 1.7.6. **1.8+** additionally serves v2-compatible endpoints (`/api/v2/query`, `/api/v2/write`, `/health`), so a "v1" server may speak v2 shapes. **2.x** replaced all of that with token auth (`Authorization: Token <value>`) and orgs/buckets — with its own crown-jewel bug, **CVE-2024-30896**: in OSS 2.x through 2.7.11, an authenticated user whose token grants access to the authorization resource in the **default organization** can list authorizations and retrieve the instance-wide **operator token**, converting one scoped token into full instance administration.

The operator takeaway: exposure is often incidental (the monitoring stack's database on a reachable port), but the data is dense — host/container telemetry (`telegraf` databases name every machine), business metrics, and occasionally secrets baked into metric tags — and the auth layers have repeatedly failed in ways that turn read into full admin.

## The anatomy that matters on an engagement

    client → 8086 HTTP
      /ping   → 204 + X-Influxdb-Version            (free fingerprint, 1.x)
      /health → JSON version/status                 (free fingerprint, 2.x + 1.8 compat)
      1.x: /query?db=&q=<InfluxQL>   /write  (line protocol)
           auth: none | basic | JWT  (empty shared secret ⇒ CVE-2019-20933 admin forge)
      2.x: /api/v2/{organizations,buckets,authorizations,query}
           auth: Authorization: Token <value>       (default-org auth listing ⇒ CVE-2024-30896)
      /metrics → Prometheus-style metrics           (often unauthenticated)

Properties that drive findings: (1) version oracles cost one request and pick the era; (2) "authentication might not be enabled" is a first-class hypothesis on 1.x — one `SHOW DATABASES` settles it; (3) both CVEs are authentication-scope confusions, not memory bugs — exploitation is quiet HTTP; (4) the query languages are string-concatenated by naive applications, and the `/query` endpoint accepts semicolon-separated multi-statement requests — app-level injection lands as whole extra statements under the app's rights.

## Where you'll meet it

- **Monitoring/observability stacks** — InfluxDB behind telegraf/Grafana/Kubernetes operators: infrastructure telemetry that maps the estate, sometimes with sensitive values in tags. See [[grafana]], [[kubernetes]].
- **IoT and industrial data platforms** — long-lived 1.x appliances with auth disabled, internet-facing because someone needed the dashboard.
- **Application backends** storing business time series — the injection-adjacent surface: any feature that splices user input into an InfluxQL `q=` or a Flux script becomes a query-body rewrite under the application's credentials.

## Reading the framework's mind

1. `curl -i .../ping` names the exact build before you commit to an era; `/health` covers 2.x and 1.8+.
2. In 1.x, the error `unable to parse authentication credentials` is the auth-required signal; its absence means open season on `SHOW`/`SELECT`/`CREATE USER` (see [[influxdb-bug-bounty]] Phase 1).
3. CVE-2019-20933 is a misconfiguration gate, not a version-only gate: build < 1.7.6 AND empty JWT shared secret; verify both before claiming, and expect distro backports.
4. In 2.x every request is one header away — tokens leak via logs, configs, CI variables, backups; an operator token (or the CVE-2024-30896 listing path to one) is the whole instance across all orgs.
5. Measurements must be double-quoted in `SELECT * FROM "cpu"` when exploiting via the bypass tooling — and that same quoting habit is what application code forgets, which is the injection seam.

## Where to go next

The companion methodology: [[influxdb-bug-bounty]] — fingerprinting, the 1.x unauth sweep, the JWT forge, token-era enumeration, the operator-token path, and injection-adjacent query abuse.
