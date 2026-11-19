---
technology: "Mattermost"
kind: overview
audience: offensive-security
family: web-platforms
layer: self-hosted team-chat platform (Go server, React webapp, REST + WebSocket API)
language: go (server), typescript/react (webapp)
first-released: 2015
license: "MIT (core) / proprietary Enterprise"
homepage: "https://mattermost.com"
github-repo: "mattermost/mattermost"
status: thin
provenance: "field-grounded (3 engagements pass the command-context gate over text+ocr; 2 marker hits excluded on manual review — see internal coverage ledger; chains: temp-company-email registration into an internal team with credentials leaked in channels, then config.json SqlSettings DSN → database hash crack → password reuse to root; DNS/mail-redirection interception of the password-reset message → token capture → custom admin plugin steered into an SSH honeypot; adjacent-app credential reuse into the chat, SSH credentials waiting in direct messages) + upstream-verified (mattermost api/v4 introduction.yaml: session token, MMAUTHTOKEN cookie, bearer auth, personal access tokens, websocket auth; api/v4/users/password/reset and /reset/send endpoint records; docs.mattermost.com incoming/outgoing webhook pages and integrations configuration settings incl. EnableIncomingWebhooks/EnableOutgoingWebhooks config.json keys). Thin unit treated first-class per standing rule — upstream supplies API/token semantics, field evidence supplies the chains. Pass 2 new-unit wave B, 2026-11-12."
---

# Mattermost

> A self-hosted Slack clone that teams treat as an internal bulletin board — password variants, database DSNs, and password-reset tokens all flow past channels and config files you can read once you are inside.

## What it is

Mattermost (2015, Mattermost, Inc.; MIT-licensed core with a proprietary Enterprise tier) is an open-source team-chat platform marketed as a self-hosted alternative to Slack and Teams: a single Go binary serving a React webapp and a REST/WebSocket API, backed by MySQL or PostgreSQL. On a default install it listens on port 8065, keeps every setting in one JSON file on disk, and ships the same features that make Slack dangerous on the inside — channels, direct messages, incoming webhooks, slash commands, and a plugin system that runs server-side code.

For an operator the product's defining trait is that it is a **credential concentrator**. Organizations paste working passwords, password patterns, database strings, and onboarding lore into channels assuming "internal" means "safe" — and the platform faithfully stores, indexes, and searches all of it. Two field paths recur: get an account by any means (registration trick, cracked reuse, intercepted reset), then mine the archive; or land on the host and read the config file that holds the database DSN.

## The anatomy that matters on an engagement

    browser → :8065 webapp + /api/v4 (REST) + /api/v4/websocket
                    ↘ SMTP: password-reset mail carrying a one-use token link
                    ↘ MySQL/PostgreSQL (users, teams, sessions) via SqlSettings DSN
                    ↘ /hooks/<id>: incoming webhooks post into channels
                    ↘ outgoing webhooks: server POSTs to URLs on trigger words
    disk: /opt/mattermost/config/config.json  (SqlSettings, ServiceSettings)
    integrations: plugins (server-side), slash commands, personal access tokens

Three structural properties drive most findings:

- **The chat archive is the cred source.** Team channels and direct messages carry password patterns and live credentials; the built-in search does the triage for you.
- **config.json is the master key on disk.** Its `SqlSettings` block holds `DriverName` and a `DataSource` DSN — `user:pass@tcp(127.0.0.1:3306)/mattermost` — that walks you straight into the database.
- **The auth edge is token-shaped everywhere.** A login returns a bearer token; the same value rides an `MMAUTHTOKEN` cookie; the reset flow mints a one-use token and emails it. Any position that can read mail or disk converts directly into an account.

## Where you'll meet it

- **A chat vhost on a web-heavy target** (field-seen): subdomain enumeration surfaces a dedicated chat hostname; the login panel is Mattermost and open registration or invite flows decide your next move.
- **Bundled with a GitLab monolith** (field-seen as a fingerprint): Omnibus installs carry a Mattermost user and service tree alongside the code host — see [[gitlab|GitLab]] for the front door it shares.
- **Small-organization infrastructure**: the chat server often shares a host with the database it names in config.json, so one file-read becomes a SQL session and a hash-cracking engagement.
- **Behind SSO or hardened registration** (field-seen): when direct signup is closed, the password-reset mail flow becomes the interesting surface — whoever controls where that mail resolves controls the account.

## Reading the platform's mind

Five facts predict behavior — each expands in [[mattermost-bug-bounty|the methodology]]:

1. **`POST /api/v4/users/login` returns the session token in a `Token` response header** (upstream API docs); the same token works as `Authorization: Bearer …` or as the `MMAUTHTOKEN` cookie, for REST and WebSocket alike.
2. **Password reset needs no permissions** — `/api/v4/users/password/reset/send` emails a one-use, timed recovery link, and `/api/v4/users/password/reset` consumes it; it works on non-SSO users only. The token lives in the mail, so mail-position is account-takeover position.
3. **`config.json` → `SqlSettings` → the database**: the DSN format (`DriverName: mysql`, `DataSource: user:pass@tcp(host:3306)/mattermost…`) is a complete credential in one grep.
4. **An incoming webhook URL is a bearer secret** (upstream docs say to treat it as one): anyone holding `/hooks/<random-id>` can post into every channel its creator could see, with Slack-compatible payloads; outgoing webhooks conversely make the server POST to URLs of the integrator's choosing.
5. **Registration and login errors are an account oracle** (field-seen): distinct responses for existing versus unknown users turn the signup form into a username enumerator before you ever authenticate.

## Where to go next

[[mattermost-bug-bounty|Mattermost bug bounty methodology]] — fingerprinting and access chains (temp-email registration, reset-mail interception), session and token handling against `/api/v4`, channel and archive cred-mining, webhook abuse, and config-to-database loot.
