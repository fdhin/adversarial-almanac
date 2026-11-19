---
technology: "Gitea / Gogs"
kind: bug-bounty-methodology
audience: ai-agent
family: web-platforms
version: "1.0"
provenance: "field-grounded (real deployments: compose-volume gitea.db theft + pbkdf2 crack; .git/config creds + docker-inspect GITEA__database__PASSWD; footer version gating; Gogs asset-hash versioning + CVE-2025-8110 symlink write to root authorized_keys; Gogs 0.5.5 API SQLi with local replica + sqlmap; issue-leaked token; token API /api/v1/user; Gogs session-file cookie trick; Windows gitea.db SMB exfil; release creds; repo cred leak; webhook→container RCE) + upstream-verified (NVD CVE-2020-14144, CVE-2025-8110 + CISA KEV, CVE-2024-6886; gogs/gogs repo). Companion overview: [[gitea|Gitea / Gogs]]."
---

# Gitea / Gogs bug bounty methodology

Target model: a self-hosted Git service (Gitea or Gogs) on :3000 (web/git-HTTP), 22
(git SSH as user `git`), possibly Dockerized with a db sidecar. Authorization assumed.

Scope discipline: Gitea/Gogs-specific surface — versioning, enum, API, data-at-rest,
hooks/webhooks, product CVEs. Exposed `.git` directories on *other* web servers belong
to the git-exposed unit; generic git client tricks to the git tooling notes.

## Triage table

| Surface | Gate | Pre-auth? | Payoff |
|---|---|---|---|
| Gogs ≤0.13.x symlink file write (CVE-2025-8110) | authenticated + affected version | account needed | Arbitrary file write → root authorized_keys — Critical (CISA KEV) |
| Git hooks RCE (CVE-2020-14144) | Gitea 1.1.0–1.12.5 + repo-admin/org role | privileged | Command exec as git user — Critical |
| Stored XSS (CVE-2024-6886) | Gitea 1.22.0 exactly | injection point | Victim-session repo read/actions — High (CVSS-B 10.0 per vendor) |
| gitea.db theft + hash cracking | any file read / volume access | no | User passwords, reuse across SSH/hosts — Critical |
| API token abuse | token in APK/configs/issues | token | Repo read, user enum — High |
| Webhook SSRF/delivery | authenticated repo settings | account | Internal POST/fetch, chained exec — Medium-High |
| Old Gogs API SQLi | Gogs ~0.5.x `/api/v1/users/search` | yes | Hash exfil — Critical on that era |

## Phase 0 — Fingerprint and version

### 0.1 The easy case (Gitea)

```text
footer, bottom-left:  "Gitea Version: 1.22.0"     (also observed live: 1.15.8)
landing page title:   "Gitea: Git with a cup of tea"
nmap: 3000/tcp open http  (gogs/gitea both default 3000)
```

`GET /api/v1/version` returns the version too (may require a token on hardened
instances — the footer usually does not).

### 0.2 The Gogs case — derive the version from asset hashes

Gogs does not print its version (observed on live deployments). The page sources carry
`gogs.js?v=<40-hex>` / `gogs.min.css?v=<40-hex>` cache-busters that are **commit
SHAs**: paste the hash into `github.com/gogs/gogs/commit/<hash>` and read the tag
(observed: `v=5084b4a9...` → 0.13.3). On-box, the binary answers directly:

```bash
ben@target:/opt/gogs/gogs$ ./gogs --version    # Gogs version 0.13.3 (observed on-box)
```

Version decides everything below (0.13.x → CVE-2025-8110 era; 0.11.x → the
session-cookie-trick era; 0.5.x → the API SQLi).

## Phase 1 — Enumeration

1. **Registration**: open? Register (one observed engagement registered a dummy account to
   Explore). Closed instances still expose Explore/public repos.
2. **Explore → users and repos**: enumerate usernames (one deployment exposed `cody`,
   `administrator`), read every public repo, issue, release, README.
3. **Issues are logs**: one real deployment leaked an API token + endpoint in an open
   issue; treat issue threads as credential leaks by convention.
4. **Adjacent leaks pointing at Gitea**: app servers' `/var/www/app/.git/config`
   carries Gitea subdomains and basic-auth creds (one observed chain:
   `cody:jh1usoih2bkjaspwe92` → gitea subdomain login); `docker inspect` output of a
   checkup script exposed `GITEA__database__PASSWD` (seen in the wild). Grep any
   foothold for `gitea`/`gogs` in configs, env, and cron.
5. **Tokens anywhere**: mobile APKs embed `Authorization: token *** strings for
   Gitea's API (one APK shipped three live service tokens).

## Phase 2 — API recon

```bash
curl -s http://198.51.100.10:3000/api/swagger        # swagger UI; empty page? check page
                                                     # source for the real vhost (seen once)
curl -s -H 'Authorization: token TOK' http://gitea.example.com:3000/api/v1/user
curl -s http://198.51.100.10:3000/api/v1/users/search?q=x      # legacy Gogs: SQLi point
curl -s http://198.51.100.10:3000/api/v1/version
```

- Authenticated: list repos, users, orgs; read raw files of private repos your token
  can see.
- Legacy Gogs SQLi (seen live on Gogs 0.5.5.1010 Beta): injection in the `q`
  parameter of `/api/v1/users/search` — spaces filtered (`/**/` tamper for sqlmap),
  best exploited by **replicating the same Gogs version locally** (`./gogs web`),
  attacking your copy, then replaying the exact payload through the SSRF/tunnel to
  the real one.

## Phase 3 — Data at rest (gitea.db and app.ini)

Locations to aim every read primitive at:

| Context | Path | Observed |
|---|---|---|
| Docker (volume /data) | `/data/gitea/gitea.db`, `/data/gitea/conf/app.ini` | compose file reveals the host mount (real deployment) |
| Windows | `C:\Program Files\Gitea\data\gitea.db` | SMB exfil (observed) |
| Gogs source layout | `/opt/gogs/data/gogs.db` | netcat exfil (observed) |

Workflow (observed chain):
1. Confirm the path (SQLite magic bytes on download; wrong depth? run your own
   `gitea/gitea` container with the same compose and see where files land).
2. `sqlite3 gitea.db` → `.tables` → `user` table: `name`, `passwd`
   (`pbkdf2$50000$50$...`), `salt`, `email`.
3. Hash-format conversion: Gogs-era hashes need base64 form for hashcat mode
   (PBKDF2-HMAC-SHA256; field-observed) or a GogsToHashcat-style converter producing
   `sha256:10000:b64salt:b64hash` (field-observed).
4. Crack with rockyou; passwords reuse onto SSH on the host (observed both as
   developer→SSH and as a service-account login).

`app.ini` (same directory) holds `SECRET_KEY`, `INTERNAL_TOKEN`, LFS JWT secrets and
mailer creds — often better than the hashes.

## Phase 4 — Authenticated chains

- **Session-cookie tricks (old Gogs)**: one engagement against 0.11.66 wrote a cookie file
  into a repo via a crafted repo/`data` file, then set the session cookie name to
  `../tmp-repo/2/data` making the server load attacker-controlled session state.
  Era-gated; remember when the target is ancient.
- **Gogs symlink write (CVE-2025-8110, verified)**: improper symlink handling in the
  PutContents API — update a symlinked file via the API (`PUT /api/v1/repos/.../
  contents/...` with the blob SHA and base64 key) and Gogs follows the link. Observed
  payoff: write an SSH public key to `/root/.ssh/authorized_keys` as a normal
  authenticated user. In the CISA KEV catalog (added 2026-01-12) — expect it patched
  on anything current, live on quiet 0.13.x internal hosts.
- **Repository as malware host**: private Gogs/Gitea doubles as a package/file server
  — one real deployment hosted a malicious Python package there for a root
  `pip3 download` to execute (see the node-express/npm unit for the registry-side
  variant of this pattern).

## Phase 5 — Privileged execution features

### 5.1 Git hooks (CVE-2020-14144, verified)

Gitea 1.1.0–1.12.5: repo settings → Git Hooks → any hook script executes on push, as
the git user. Gate: your account must reach repo-admin or org privileges. NVD carries
the vendor dispute ("this is functionality") — for you it means: any path to a
privileged account (cracked admin hash from Phase 3, XSS session ride from the CVE
below) converts to RCE on these versions. Post-1.12.5 hooks are admin-only and
disabled by default.

### 5.2 Stored XSS (CVE-2024-6886, verified)

Gitea exactly 1.22.0: stored XSS via improper input sanitization (vendor CVSS-B 10.0,
fixed in 1.22.1). Seen in the wild: injected script in repo content read a **private repo's**
README through a maintainer's session — XSS is the door to 5.1's privileged hooks.

### 5.3 Webhooks

Repo settings → webhooks: the server fetches your URL and POSTs event JSON on push.
Uses: internal POST delivery (CSRF-style hits at internal services), SSRF probe of
loopback/internal ports, and — one observed chain — a configured webhook on an admin repo
was itself the RCE path into the container network. On Gitea with Actions enabled,
`.gitea/workflows/` + runners extend this surface (CI is code-execution by design;
gate on runners being enabled before investing).

## Phase 6 — Gogs vs Gitea divergence (quick table)

| Fact | Gogs | Gitea |
|---|---|---|
| Version in footer | no — derive via asset `?v=` commit hash | yes |
| Maintained | quiet through 0.13.x | active (actions, releases) |
| CVE-2025-8110 symlink write | affected (KEV) | not affected (Gogs-only bug) |
| CVE-2020-14144-era hooks | comparable advisory coverage (FZI FSA-2020-3 covered Gogs 0.12.2) | 1.1.0–1.12.5 |
| Suid helper on host | `gosu` (observed as the root step) | — |

Check which one you face *before* running version-gated exploits — the pages look alike.

## Command quick reference

```bash
# fingerprint
curl -s http://198.51.100.10:3000/ | egrep -i 'gitea|gogs|version'   # footer + assets
curl -s 'http://198.51.100.10:3000/api/v1/version'
# api
curl -s -H 'Authorization: token TOK' 'http://gitea.example.com:3000/api/v1/user'
curl -s 'http://198.51.100.10:3000/api/v1/users/search?q=a'          # legacy SQLi probe
# db
sqlite3 gitea.db '.tables' ; sqlite3 gitea.db 'select name,passwd,salt from user;'
# on-box
find / -name 'gitea.db' -o -name 'gogs.db' 2>/dev/null
./gogs --version
```

## Tooling (prose)

sqlite3 (and a GUI browser) for the databases; hashcat for PBKDF2 after format
conversion (mode per the sha256:10000 layout); sqlmap with a custom tamper for the
legacy API injection (spaces → `/**/`); curl for the REST API; standard git over
HTTP/SSH for repo access; SMB server or netcat for database exfil. Tools live in the
tools vault.

## References

- NVD: CVE-2020-14144 (hooks), CVE-2025-8110 (Gogs symlink, KEV), CVE-2024-6886
  (Gitea 1.22.0 XSS, fixed 1.22.1); FZI FSA-2020-3 (Gogs/Gitea hooks advisory).
- gogs/gogs repository (divergence, versioning behavior).
- Evidence ledger: engagement chains behind this page (full ledger in
  internal coverage ledger).
