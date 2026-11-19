---
name: "pwndrop"
categories:
  - blackarch-webapp
version: "18.385ba70"
homepage: "https://github.com/kgretzky/pwndrop"
packaged-by: [blackarch]
blackarch-builddate: "2021-04-27"
build-system: go
license: GPL3
meta-checked: "2026-11-09"
github-repo: "kgretzky/pwndrop"
github-stars: 2280
github-pushed: "2023-02-25"
github-archived: false
github-issues: 45
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.7
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# pwndrop

> Self-deployable file-hosting service for red teamers: upload payloads once, share them over HTTP/HTTPS/WebDAV with façade tricks.

## What it's for

Replacing `python -m SimpleHTTPServer` on a red-team VPS with something purpose-built: drag-and-drop upload of payload files, per-file enable/disable, custom download URLs, façade files served to unwanted visitors, fake file extensions via redirects, arbitrary MIME types, and a hidden admin panel gated behind a secret URL path with username/password login. It terminates TLS itself (Let's Encrypt with auto-renewal by default) and can even act as a DNS nameserver answering A records for any subdomain you point at it. Single Go binary with a Vue.js admin UI. Not a C2 or an exploitation tool — it is the delivery/sharing layer once you already have payloads to distribute.

## Typical use cases

### Install and start as a daemon

Get a persistent instance running on a VPS.

```
pwndrop install
pwndrop start
```

Outcome: registers a daemon with the system manager (systemd/SysV/upstart), starts it, and writes `pwndrop.ini` next to the executable on first launch; `pwndrop status` reports daemon health and `pwndrop stop`/`remove` control it. Then visit `https://your-domain/pwndrop` (the default secret path) to authorize your browser and log into the admin panel at the site root.

### Run a local instance without TLS and DNS

Use it on a lab box or over plain IP, no domain needed.

```
pwndrop start -no-autocert -no-dns
```

Outcome: serves plain HTTP without attempting Let's Encrypt issuance (`-no-autocert`) and skips the DNS server on UDP/53 (`-no-dns`) — the right shape for internal-network payload hosting; add `-debug` for verbose output and `-config <path>` for an alternate config file.

### Pre-configure deployment via config file

Automate setup for repeated infrastructure builds.

Write the admin account, secret path and redirect target into `pwndrop.ini` before first launch:

```
[setup]
username = "admin"
password = "Password1"
secret_path = "/change-me"
redirect_url = "https://example.com"
```

Then start the instance against that config.

```
pwndrop -config pwndrop.ini start
```

Outcome: on first run the `[setup]` section provisions the admin account and secret path and is then deleted from the config — the admin panel requires no interactive first-time wizard, which keeps scripted deploys clean (place the file at `/usr/local/pwndrop/pwndrop.ini` and it is picked up automatically).

*Provenance: no corpus evidence — derived from upstream documentation (README CLI usage + config sample, verified 2026-11).*
