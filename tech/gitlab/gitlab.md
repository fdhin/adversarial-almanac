---
technology: "GitLab"
kind: overview
audience: offensive-security
family: web-platforms
layer: self-hosted DevOps platform (Rails monolith: repos, CI/CD, registry)
language: ruby
first-released: 2011
license: "CE: MIT / EE: proprietary"
homepage: "https://about.gitlab.com"
github-repo: "gitlabhq/gitlabhq"
status: thin
provenance: "field-grounded (6 engagements gated by command-context markers, one manual exclusion — see internal coverage ledger; chains: 11.4.7 SSRF+CRLF→Redis RCE + gitlab.rb creds; 17.3.2 SAML bypass + runner env dump; 12.8.1 file-read → secret_key_base → marshalled-cookie RCE → rails-console admin; cred autofill + git-hook deploy; fingerprint-only side target; webhook→container RCE) + upstream-verified (GitLab advisory/release notes for CVE-2023-7028 and CVE-2024-45409, advisories.gitlab.com ruby-saml record, NVD CVE-2020-10977, exploit-db pair record for CVE-2018-19571/-19585, docs.gitlab.com GraphQL API page). Thin unit treated first-class per standing rule — upstream supplies version gates and CVE mechanics, field evidence supplies the chains. Enrichment pass 1, 2026-11-12."
---

# GitLab

> A Rails monolith that prints its build number on the help page — and whose exploitation is a version-gated catalog: password-reset takeover in the 16.x line, SAML bypass below 17.3.3, and file-read-to-RCE chains on anything 11–12 vintage.

## What it is

GitLab (2011, Zaporozhets/Sijbrandij; CE is MIT-licensed) is a full DevOps platform in one Omnibus package: git hosting, a Ruby on Rails web UI, REST (`/api/v4`) and GraphQL (`/api/graphql`) APIs, an integrated CI/CD runner system, and container registry. Behind the nginx front sit Puma/Workhorse, PostgreSQL, and Redis — and admin tooling (`gitlab-ctl`, `gitlab-rails console`) that is god-mode on the box. For an operator the product's defining trait is **version density**: nearly every worthwhile bug is pinned to an exact minor-version window, and the instance often tells you the version itself.

The bug profile follows the architecture: authentication edge (SSO/SAML integrations, password-reset email flow), Rails marshalled-cookie deserialization once `secret_key_base` leaks, import/webhook features that make the server talk to hosts you choose, and a runner fleet that executes `.gitlab-ci.yml` as designed — which means your YAML is the payload.

## The anatomy that matters on an engagement

    user → nginx → GitLab Rails (web UI, /api/v4, /api/graphql)
                     ↘ import/mirror URLs, webhooks → arbitrary internal fetches
                     ↘ Redis queues → gitlab-shell/gitaly → git on disk
    CI: .gitlab-ci.yml → runner picks up job → container exec (image: you choose)
    admin: gitlab-rails console (User.find_by..., user.admin = true)

Three structural properties drive most findings:

- **The help page is a version oracle** (observed builds 11.4.7, 12.8.1, and 17.3.2 — all read from `/help`) — first stop on every GitLab engagement, and the gate for every CVE below.
- **Rails session machinery is only as safe as `secret_key_base`** — the file-read CVEs exist to steal `secrets.yml`, and a marshalled `experimentation_subject_id` cookie then converts to RCE.
- **Runners execute repository instructions by design** — with any project write (or an admin account), `.gitlab-ci.yml` is a scripted container-execution request with environment-variable access.

## Where you'll meet it

- **Org code hosts with SSO** (observed): GitLab behind an identity provider (Authentik in that deployment) — SAML is both the auth path and the attack surface (CVE-2024-45409 forges any user below 17.3.3).
- **Aging internal GitLab** (observed): old versions on odd ports (5080 seen on one), unauthenticated version leak, classic file-read/RCE chains still live.
- **CI/CD fleets** (observed): runners with secrets in env vars; webhooks wired into the container network; admin repos whose hooks deliver code exec.
- **As a side-note target** (one observed case): a public repo used as a file/commit-history oracle during V8 reversing — even read-only GitLab pays recon.

## Reading the platform's mind

Five facts predict behavior — each expands in [[gitlab-bug-bounty|the methodology]]:

1. **`http-title: Sign in · GitLab`** and the redirect to `/users/sign_in` identify the product from nmap alone (observed twice); `/help` (and robots.txt's tell-tale disallow list) version it.
2. **Explore works logged-out** — public projects, members, and activity are enumerable pre-auth; scripts inside them carry credentials (observed: a user's password in a Python client).
3. **GraphQL is versionless but introspectable** — `/api/graphql` answers unauthenticated queries for public data and full introspection in default configs (upstream docs).
4. **Password reset takes an email array on vulnerable 16.x** — `user[email][]=victim&user[email][]=attacker` is the whole CVE-2023-7028 exploit (verified ranges 16.1–16.7.1).
5. **`gitlab-rails console` ends engagements** — with a foothold on the box (or a marshalled-cookie RCE), two lines grant admin: `user = User.find_by(username: 'you'); user.admin = true` (observed end-to-end).

## Where to go next

[[gitlab-bug-bounty|GitLab bug bounty methodology]] — fingerprinting and version gating, enumeration via UI/REST/GraphQL, the four verified CVE families with version ranges and exploit shapes, rails-console post-exploitation, webhook and runner abuse.
