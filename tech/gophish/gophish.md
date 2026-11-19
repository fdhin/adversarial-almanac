---
technology: "GoPhish"
kind: overview
audience: offensive-security
family: web-platforms
status: "thin (3 evidence files pass the command-context gate, one of them operator-side deployment — upstream carries the methodology)"
layer: self-hosted phishing platform (admin web UI + REST API on a TLS port, phishing/landing web server, SMTP sending, webhook event stream)
language: go
license: MIT
homepage: "https://getgophish.com"
github-repo: "gophish/gophish"
provenance: "corpus-grounded (re-gated 2026-11-12, command-context gate, see internal coverage ledger): one chain of an exposed admin console on a nonstandard HTTPS port taken over with documented default credentials and looted for templates/credential-harvesting infrastructure; one chain of webhook events flowing into an automation platform whose exported workflow configuration leaked the HMAC signing secret, converting a signature check into an attacker-computable value and a downstream database into an error-based SQL injection sink; one operator-side deployment (platform stood up to deliver a payload) + upstream-verified: GoPhish docs (Getting Started: releases through v0.10.1 default admin/gophish; later releases print a temporary admin password in the logs and force a reset; admin server binds https://127.0.0.1:3333 by default, phishing server 0.0.0.0:80; GOPHISH_INITIAL_ADMIN_PASSWORD override; Webhooks: X-Gophish-Signature sha256= HMAC-SHA256 over the raw JSON body with the endpoint secret; event vocabulary Email Sent/Opened/Clicked Link/Submitted Data) + webhook/webhook.go source (SignatureHeader, Sha256Prefix, hmac-sha256 over json.Marshal payload), 2026-11-12."
---

# GoPhish

> The open-source phishing platform is a one-stop credential store: log into its admin console and you get the harvest, the target lists, the SMTP credentials and the sending history — and its webhook integrations hand their signing secrets to anyone who can read the automation config.

## What it is

GoPhish is the standard self-hosted phishing framework: a Go binary that runs an **admin server** (web UI plus a full REST API under `/api/`, default listener `https://127.0.0.1:3333`) and a **phishing server** that serves landing pages and tracks opens/clicks/submissions (default `0.0.0.0:80`). Campaigns are assembled from templates, user/group lists and sending profiles (stored SMTP credentials), and every event streams out as JSON — to the UI, to the API, and optionally as **signed webhooks** pushed to automation platforms for scoring and triage.

You meet it in two postures. **Target-side**, it is someone's security-team or red-team infrastructure: an admin console on a nonstandard HTTPS port with a self-signed certificate (port scanners fingerprint it even when a plain HTTP request returns an opaque response), reachable because an operator rebound the listener to all interfaces in `config.json` and exposed it "temporarily". **Operator-side**, you deploy it yourself inside social-engineering scope — and its defaults are then your operational exposure: the documentation's default administrator pair for older releases, temporary log-printed credentials for newer ones, and an API key that equals full admin rights.

The design's characteristic mistakes follow from being infrastructure *for* attackers, hardened like an internal tool. Default credentials are documented in the manual (`admin` / `gophish` through v0.10.1; a one-time password printed in server logs after, with a forced reset) — both eras fail in the field, the first to a default pair, the second to operators who "reset" to something memorable. The admin session equals **total disclosure**: templates reveal sender addresses and vhosts of the phishing infrastructure, groups hold the target list, sending profiles hold SMTP credentials, landing pages hold the harvested credentials. And the webhook integrations — the feature that pipes event JSON into workflow automation — sign their requests with an HMAC-SHA256 secret that travels inside the integration's own configuration, so any leak of the exported workflow is a signature-forgery primitive against everything downstream of the check.

## The anatomy that matters on an engagement

    admin server (HTTPS, nonstandard port; config.json rebinding)
      -> login: default pair (<=0.10.1) / log-printed temp password / weak reset
      -> REST /api/... with the API key (settings page) — full admin rights
           templates (HTML, sender, links) -> vhost + infrastructure intel
           users/groups (target lists), sending profiles (SMTP creds)
           landing pages (harvested credentials), campaign results
      -> campaigns -> phishing server: landing + tracking (rid)
      -> webhooks: POST event JSON to automation (n8n-style workflow engines)
           X-Gophish-Signature: sha256=<HMAC-SHA256(raw body, secret)>
           secret stored in BOTH GoPhish and the integration's exported config

Three structural properties drive most findings:

- **One login is the whole operation.** There is no privilege gradient inside GoPhish: admin session, API key and UI all reach templates, targets, SMTP profiles and harvested results — the impact paragraph of any access finding writes itself.
- **The signature check is only as secret as the least-protected copy of the secret.** Webhook recipients verify `sha256=<hex>` over the raw body; the secret lives in GoPhish's webhook config *and* in the receiver's workflow definition, and exported/imported workflow JSON is routinely downloadable from wikis and ticket systems.
- **Everything downstream parses event fields.** Automation that drops webhook JSON into a database or template engine turns a forged request into injection — the signature was the only gate, and you can now compute it.

## Where you'll meet it

- **Red-team and blue-team infrastructure boxes** — a high HTTPS port answering with a GoPhish login behind a self-signed certificate, usually discovered during wide port scans; the HTTP cleartext response is often cryptic, the TLS one is not.
- **Corporate phishing-simulation deployments** — the security team's own instance inside the perimeter; admin access there discloses the simulation's targets, templates and the real SMTP credentials it mails through.
- **Automation-adjacent surfaces** — wiki pages documenting webhook integrations, downloadable workflow executions, and the scoring databases fed by them; these leak the signing secret without touching the GoPhish host at all.
- **Your own engagements** — standing GoPhish up to deliver payload hosting and tracking; its listener bindings, credential reset flow and API key handling are then your opsec surface, and the same defaults you exploit on targets apply to you.
- **Bug bounty nuance:** default credentials on an exposed console are the access finding, but the *report* is the loot — enumerate what the instance holds (targets, SMTP profiles, harvested credentials) and document exactly what was readable, without exporting the harvested data itself.

## Reading the framework's mind

1. **Version behavior dates the instance.** Through v0.10.1 the documented default is `admin`/`gophish`; later releases print a one-time admin password to the server log at first start and force a password change — so a static working pair means an old build or a weak operator reset, and either way the next try is cheap.
2. **The two listeners have opposite defaults.** The phishing server binds all interfaces on port 80; the admin server binds loopback on 3333 — an admin port reachable from outside means a deliberate `config.json` change, which also tells you the deployment has been customized (check the TLS certificate and any proxy in front).
3. **Webhook signature format is fixed and simple.** `X-Gophish-Signature: sha256=<hex>` where the hex is HMAC-SHA256 of the exact raw JSON body under the endpoint secret — recompute it with any HMAC tool and the receiver cannot tell your request from the platform's.
4. **Event JSON vocabulary is predictable.** `Email Sent`, `Email Opened`, `Clicked Link`, `Submitted Data` messages with `campaign_id`, `email` and a `details` object — these field names are what downstream automations interpolate, so they are also your injection column list.
5. **Templates are an infrastructure map.** Sender addresses, links and landing-page URLs inside templates enumerate vhosts and hostnames to add to your hosts file — the phishing infrastructure itself becomes additional attack surface (credential-harvesting clones of real sites, admin panels).

## Where to go next

[[gophish-bug-bounty]] — the phased agent methodology: fingerprinting the console, credential eras and fallbacks, structured loot (templates → vhosts, sending profiles → SMTP, groups → targets), and the webhook signature-forgery path into downstream automation.
