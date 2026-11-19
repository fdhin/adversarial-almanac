---
name: "assassingo"
categories:
  - blackarch-scanner
version: "173.2dd8650"
homepage: "https://github.com/AmyangXYZ/AssassinGo"
packaged-by: [blackarch]
blackarch-builddate: "2026-02-14"
build-system: go
license: MIT
meta-checked: "2026-11-09"
github-repo: "AmyangXYZ/AssassinGo"
github-stars: 560
github-pushed: "2024-08-23"
github-archived: true
github-issues: 2
github-match: "exact"
gh-checked: "2026-11-07"
worth: 0.6
worth-basis: "historical (archived/gone upstream)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# assassingo

> Go web-pentest framework with a browser GUI: recon, subdomain and directory scanning, XSS/SQLi checks and brute forcing behind a REST/WebSocket API.

## What it's for

AssassinGo (AmyangXYZ; the upstream repo is archived since 2024) bundles information
gathering and vulnerability checking behind a WebSocket web GUI: security-header
retrieval, CloudFlare bypass via real-IP discovery, CMS detection, honeypot scoring, port
scanning, traceroute geolocation, subdomain and directory scanning, whois, crawling,
basic SQLi and XSS checks, an HTTP intruder, an SSH brute forcer, and Google dorking via
headless Chrome. The single Go binary is only the web server (`main()` calls
`web.Run()`); the documented deployment is a docker-compose stack (Go app plus MariaDB)
whose bundled `backup.sql` seeds the default `admin:admin` login. Interesting today mainly
as a self-contained lab framework — expect no upstream fixes.

## Typical use cases

### Bring up the framework stack

Start the GUI, API and database the documented way.

```
cd AssassinGo
docker-compose up --build -d
cat backup.sql | docker exec -i assassingo_mariadb_1 /usr/bin/mariadb -uag --password=«redacted-secret» ag
```

Outcome: builds and starts the AssassinGo web application with its MariaDB backend, then
restores the seed database; the GUI and API become reachable at http://127.0.0.1:8000 —
log in with the default `admin:admin` and set a target from the dashboard.

### Drive a scan through the API

Script the framework instead of clicking the GUI.

```
curl -s -c cookie.txt -d 'username=admin&password=admin' http://127.0.0.1:8000/token
curl -s -b cookie.txt -d 'target=example.com' http://127.0.0.1:8000/api/target
curl -s -b cookie.txt http://127.0.0.1:8000/api/info/basic
```

Outcome: signs in and stores the session token, sets `example.com` as the active target,
and pulls basic recon — resolved IP, web server and security-header posture — as JSON;
the WebSocket endpoints (`/ws/info/subdomain`, `/ws/attack/sqli`, ...) stream live scan
results the same way.

*No corpus evidence — derived from upstream documentation (README deployment and API tables).*
