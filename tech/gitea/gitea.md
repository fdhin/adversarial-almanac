---
technology: "Gitea / Gogs"
kind: overview
audience: offensive-security
family: web-platforms
layer: self-hosted Git service (web UI + git SSH/HTTP + API)
language: go
first-released: "Gogs 2014 / Gitea fork 2016"
license: MIT
homepage: "https://about.gitea.com"
github-repo: "go-gitea/gitea"
provenance: "field-grounded (29 engagements gated by product-name markers — see internal coverage ledger; strongest patterns: compose-volume gitea.db theft, .git/config + docker-inspect creds, footer/asset-hash version gating, Windows SMB exfil, APK-embedded tokens, legacy API SQLi, issue-leaked tokens, session-file cookie trick, release creds) + upstream-verified (NVD CVE-2020-14144, CVE-2025-8110 incl. CISA KEV listing, CVE-2024-6886; gogs/gogs repo metadata). Enrichment pass 1, 2026-11-12."
---

# Gitea / Gogs

> A version number in the footer, a SQLite database full of PBKDF2 hashes at a path you can guess, and a git-hooks feature that is documented RCE for whoever holds an admin account.

## What it is

Gogs ("Go Git Service", 2014) and its 2016 community fork **Gitea** are lightweight self-hosted Git services in a single Go binary — web UI, HTTP+SSH git transport, a REST API under `/api/v1`, and by default a SQLite database. The default web port is **3000**, in Docker the whole state lives under one volume (`/data`, so `/data/gitea/gitea.db` and `/data/gitea/conf/app.ini`), and on Windows installs it lands under `C:\Program Files\Gitea\data\`. Gitea is the maintained sibling (Gogs development has been quiet through the 0.13.x era) — but both still run in the wild, and the divergence itself is an attack surface fact (one live deployment required deriving Gogs' version from an asset-cache hash because Gogs doesn't print it).

The operator takeaway: a Gitea/Gogs instance is a **credential and code-execution hub** — every engagement chain observed to date runs through its repositories (leaked tokens in issues, deployment secrets in compose files), its database (crackable password hashes), or its privileged features (git hooks, webhooks, actions runners).

## The anatomy that matters on an engagement

    user → :3000 web UI  → Explore (users/repos) → repos (issues, releases, raw files)
         → /api/v1 (token auth, swagger) → same data, scriptable
         → git over HTTP(:3000)/SSH(:22 as user 'git')
    disk state: /data/gitea/{gitea.db, conf/app.ini, repositories/, sessions/}
    exec features: git hooks (privileged) · webhooks (delivery) · actions runners (CI)

Three structural properties drive most findings:

- **The footer tells you the version** (Gitea: `Gitea Version: 1.22.0` bottom-left — seen on live instances) — one glance gates the whole CVE playbook. Gogs hides it; derive from the asset query-string commit hash instead.
- **All secrets concentrate in one data directory.** The SQLite `user` table carries `pbkdf2$50000$...` password hashes; `app.ini` carries `SECRET_KEY`/`INTERNAL_TOKEN`. Any file-read primitive (traversal on the main site, a mounted volume, a container-inspect helper) aims at these paths (all three observed in real engagements).
- **Repository content is treated as trusted.** Issues leak API tokens (observed), compose files leak database passwords (observed via docker-inspect env), releases hold credentials (observed), and READMEs hold admin notes — because the instance is "internal".

## Where you'll meet it

- **Docker-compose internal code hosts** (observed repeatedly): gitea container + db container on an internal bridge; the web UI is reachable, the env vars carry `GITEA__database__PASSWD`.
- **Org tooling vhosts** (a recurring pattern): one Gitea per project, half-abandoned registrations, public repos with secrets in issues and profiles.
- **Windows members** (observed): `Program Files\Gitea`, SMB-exfiltrable `gitea.db`; Gogs binaries callable for `--version` on-box.
- **Internal-only instances reached by pivoting** (observed): localhost:3000 behind SSRF, SSH tunnels or double port-forwards — API and version still disclose plenty pre-auth.

## Reading the platform's mind

Five facts predict behavior — each expands in [[gitea-bug-bounty|the methodology]]:

1. **`/api/v1/version` and the footer** version the instance unauthenticated (token for some API routes; footer is free).
2. **Open registration is common** — register, Explore, and read every public repo/issue before touching an exploit.
3. **`gitea.db` is SQLite** — pull it, `sqlite3` it, crack the `user` table (PBKDF2; hashcat after format conversion).
4. **Git hooks are RCE for privileged accounts** on vulnerable versions (CVE-2020-14144, 1.1.0–1.12.5) — check who you can become before discounting it.
5. **Webhooks make the server fetch and POST** — an authenticated SSRF/delivery primitive aimed at internal services (and in one observed chain, the code-exec foothold itself).

## Where to go next

[[gitea-bug-bounty|Gitea/Gogs bug bounty methodology]] — version fingerprinting (including the Gogs asset-hash trick), enumeration and API recon, database/app.ini extraction with hash cracking, the verified CVE chains, webhook abuse, and the Gogs divergence table.
