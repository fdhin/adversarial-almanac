---
technology: "Grafana"
kind: overview
audience: offensive-security
family: web-platforms
status: "thin (5 evidence files pass the re-gated command-context gate — the structure pass undercounted at 4)"
layer: dashboard/visualization web app on 3000/tcp (Go backend, SQLite state at /var/lib/grafana/grafana.db)
language: Go / TypeScript
homepage: "https://grafana.com/oss/grafana/"
github-repo: "grafana/grafana"
provenance: "field-grounded THIN (re-gated 2026-11-12: 5 evidence files; structure-pass 4 undercounted — the disk-readable-db chain missed; see internal coverage ledger; chains: CVE-2021-43798 traversal → /etc/grafana/grafana.ini admin password → provisioning/datasources/mysql.yaml → MySQL creds; traversal → grafana.db download → PBKDF2-HMAC-SHA256 offline crack; grafana.db readable on disk → sqlite3 → password reuse; datasource query SQLi → sqlmap → Postgres DBA → COPY FROM PROGRAM RCE; CVE-2024-9264 SQL-Expressions/DuckDB injection → root-in-container → GF_SECURITY_ADMIN_* env creds) + upstream-verified: NVD/Grafana CVE-2021-43798 (8.0.0-beta1–8.3.0, fixed 8.3.1/8.2.7/8.1.8/8.0.7), Grafana advisory CVE-2024-9264 (11.0.0+, viewer-or-higher, duckdb binary in PATH, CVSS 9.4), Grafana docs (built-in password auth default, auth.anonymous off by default, datasource provisioning YAMLs), grafana v8.3.0 source pkg/util/encoding.go (PBKDF2 10000 iterations), 2026-11-12."
---

# Grafana

> A login page on 3000 whose whole purpose is holding credentials to every database in the company — the dashboards are the org chart of your next pivot.

## What it is

Grafana is the de-facto observability front end: a Go web app on **3000/tcp** that renders dashboards by querying **data sources** — MySQL/Postgres, Prometheus, Elasticsearch, cloud APIs — through its own server-side proxy. That proxy is the design's security story: Grafana stores each data source's **backend credentials** (in `grafana.db` and in `provisioning/datasources/*.yaml`, historically in cleartext) and attaches them when a panel runs, so anyone who can read Grafana's state can usually reach every backend it graphs. A local SQLite database (`/var/lib/grafana/grafana.db`) holds users, dashboards, and data source configs; containerized deployments inject the admin account through `GF_SECURITY_ADMIN_USER`/`GF_SECURITY_ADMIN_PASSWORD` environment variables, which later surface in `/proc` and `env` (observed).

The default posture is a login wall with a famous default pair (**admin/admin**, first-login forced change) and anonymous access **disabled** — but the same config file flips `[auth.anonymous] enabled = true`, turning the instance into an open dashboard reader. The bug that made exposed Grafanas famous is structural: plugin assets are served unauthenticated under `/public/plugins/<plugin>/`, and in 8.0.0-beta1–8.3.0 that path was traversal-able (**CVE-2021-43798**, unauthenticated arbitrary file read) — so the "just the login page" box that nmap hands you was, for that whole version era, a file-read primitive into `grafana.ini` and the provisioning YAMLs (observed twice).

The canonical chains here are: traversal → config → backend creds → database; traversal → `grafana.db` → offline password crack; authenticated datasource SQLi → DBA → RCE; and the 2024 SQL-Expressions/DuckDB injection (**CVE-2024-9264**, any Viewer, command execution when the `duckdb` binary is in PATH).

## The anatomy that matters on an engagement

    port scan → 3000/tcp → Grafana login page  ← fingerprint + version fork
      → unauth file read? (8.0.0-beta1–8.3.0, CVE-2021-43798)
            → /etc/grafana/grafana.ini          ← admin password
            → /etc/grafana/provisioning/datasources/*.yaml ← backend creds
            → /var/lib/grafana/grafana.db       ← offline crack + datasource secrets
      → login (default/weak/leaked creds)
      → datasource panel → server-side query as the backend user
            → SQLi in the panel query (sqlmap) → DBA → RCE   ← observed
            → SQL Expressions (11.0.x + duckdb) → command injection ← CVE-2024-9264, observed
      → shell in container → GF_SECURITY_ADMIN_* env → credential reuse on host

Three structural properties drive most findings:

- **The app is a credential hub.** Every data source it graphs is a credential it holds; `grafana.ini`, the provisioning YAMLs, `grafana.db`, and the container env are the four places those credentials live in order of yield (one observed chain walked exactly this ladder).
- **Plugin paths are public paths.** `/public/plugins/*` serves static assets without a session — the moment path handling breaks there, the login wall stops mattering (CVE-2021-43798, unauthenticated by design of the asset route).
- **Panel permissions are query permissions.** A Viewer with dashboard access can drive real queries against backends through the datasource proxy — enough for SQLi (observed: the injected request was a saved panel's query) and enough for CVE-2024-9264, which gates on Viewer-or-higher.

## Where you'll meet it

- **Internal monitoring endpoints** — a `grafana` subdomain on vhost fuzz (observed: one Grafana vhost found via ffuf status-diff), 3000 on internal scans; often the only unpatched box in the monitoring VLAN.
- **Kiosk/exposed dashboards** — Grafana deployed view-only for a wall display (an observed kiosk subdomain); anonymous or viewer access still reaches the datasource proxy.
- **Containerized observability stacks** — root-in-container shells (observed), `GF_*` env credentials, Docker socket neighbors.
- **Adjacent to the databases it graphs** — a Grafana foothold is usually one YAML read away from MySQL/Postgres/Elasticsearch credentials; treat the datasources page as the post-exploitation map.

## Reading the app's mind

1. **The login page leaks the version** — footer / help-icon hover (observed: v11.0.0 read off the help icon on one deployment, off the page source on another) — the version decides the CVE fork before any auth attempt.
2. **`curl --path-as-is http://198.51.100.10:3000/public/plugins/alertlist/../../../../../../../../etc/passwd`** answers without credentials on 8.0.0-beta1–8.3.0 (CVE-2021-43798; observed twice) — `--path-as-is` is mandatory, any shipped plugin name works (`alertlist`, `zipkin`, ...).
3. **`grafana.ini` reads are credential reads** — `admin_password` inline or in the provisioning YAMLs next to it (observed); the file also names the `grafana.db` path and every configured integration.
4. **`grafana.db` is an offline crack** — user table stores `password`, `salt`, `rands`; the hash is PBKDF2-HMAC-SHA256 (v8.3.0 source: 10000 iterations) — convert and hand to hashcat/john (both observed; one got the file with no CVE at all, just a readable path on disk).
5. **DuckDB in PATH turns Viewer into root** — CVE-2024-9264 needs no plugin install, only Grafana 11.0.0+ with the `duckdb` binary present (observed: uid=0 in the container, first try).

## Where to go next

[[grafana-bug-bounty]] — the phased agent methodology: fingerprint and version fork, the CVE-2021-43798 file-read ladder, grafana.db offline cracking, authenticated datasource abuse (SQLi, SQL Expressions), and container-era post-exploitation.
