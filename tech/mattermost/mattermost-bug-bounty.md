---
technology: "Mattermost"
kind: bug-bounty-methodology
audience: ai-agent
family: web-platforms
version: "1.0"
status: thin
provenance: "field-grounded (real deployments: ticket-system temp email → team invite → channel password leaks + config.json SqlSettings DSN → Users-table hash crack → password reuse to root; DNS-redirection interception of the password-reset mail with the token read off a tunnel capture → account reset → custom admin-login plugin steered to an SSH honeypot; adjacent-app hash crack reused into the chat, SSH credentials found in direct messages) + upstream-verified (mattermost api/v4 introduction.yaml for token/MMAUTHTOKEN/bearer/personal-access-token semantics and websocket auth; api/v4/users/password/reset + /reset/send endpoint records incl. no-permissions and non-SSO notes; docs.mattermost.com incoming/outgoing webhook pages incl. /hooks/<id> secret-bearer semantics and EnableIncomingWebhooks/EnableOutgoingWebhooks config keys). Thin unit — token and webhook semantics are upstream-verified, chains are field-shown. Companion overview: [[mattermost|Mattermost]]. Pass 2 new-unit wave B, 2026-11-12."
---

# Mattermost bug bounty methodology

Target model: self-hosted Mattermost (single Go binary, React webapp) on 8065 or
behind a reverse proxy, MySQL or PostgreSQL on the same host or adjacent, config
at `/opt/mattermost/config/config.json`. Authorization for testing assumed.

Scope discipline: Mattermost-product surface. The chat subdomain's siblings
(ticketing, support portals) matter only as registration fodder; generic web bugs
belong to their own units. Mattermost bundled inside a GitLab Omnibus box shares
the front door — see [[gitlab|GitLab]].

## Phase 0 — Fingerprint and surface map

```bash
# vhost enum first: the chat lives on its own hostname
ffuf -w subdomains.txt -u http://198.51.100.10 -H "Host: FUZZ.example.com"
curl -s http://chat.example.com/api/v4/users/me   # 401 JSON = API live
curl -s http://chat.example.com/api/v4/users/login \
  -H 'Content-Type: application/json' -d '{}'      # shape of the auth error
```

- Product markers: "Mattermost" branding on the login page, ` Collaborate with
  your team in real-time` marketing line, `/api/v4/` paths.
- Check registration posture immediately: open signup, invite-only, email-domain
  restriction. The error messages differ for existing vs unknown users — that is
  the username oracle (field-proven).
- Note any SSO/LDAP button: reset-flow takeover (Phase 3) does not apply to SSO
  accounts (upstream: non-SSO users only).

## Phase 1 — Getting the first account (access chains)

Ordered by cost, cheapest first:

1. **Password reuse from any adjacent foothold** (field chain): crack or steal
   credentials from another app on the target (an export function, a hash dump)
   and replay them against the chat login. Internal tools share passwords with
   the chat more often than with anything else.
2. **Temporary corporate address** (field chain): if a support/ticketing portal
   issues a company email to unauthenticated visitors (ticket threads by mail),
   use that address to satisfy domain-restricted signup or a team invite. The
   invite-confirmation link lands in the same ticket thread you already read.
3. **Password-reset interception** (Phase 3) when you control any part of the
   mail path.
4. **Webhook/token leak from integrations** (Phase 5) — no login needed at all.

## Phase 2 — Session and token handling

Verified semantics (upstream API introduction):

```bash
# login → session token in the Token response header
curl -si -X POST http://chat.example.com/api/v4/users/login \
  -H 'Content-Type: application/json' \
  -d '{"login_id":"user@example.com","password":"Password123!"}'
# Token: hyr5dmb1mbb49c44qmx4whniso

# two interchangeable uses of the same token:
curl -s -H 'Authorization: Bearer hyr5dmb1mbb49c44qmx4whniso' \
  http://chat.example.com/api/v4/users/me
curl -s -H 'Cookie: MMAUTHTOKEN=hyr5dmb1mbb49c44qmx4whniso' \
  http://chat.example.com/api/v4/users/me
```

- **Personal access tokens** (Account Settings → Security, or System Console)
  do not expire until revoked — the best persistence loot on a compromised
  account. Same `Authorization: Bearer` consumption as session tokens.
- The WebSocket endpoint `/api/v4/websocket` authenticates with the same cookie
  or header — real-time event streams without polling.
- Pagination: `per_page` defaults to 60, max 200 — set it explicitly when
  enumerating users, teams, or channels.
- Rate limiting is visible in `X-Ratelimit-Limit`/`X-Ratelimit-Remaining`
  response headers; back off before lockouts on password guessing.
- With any shell on the host: sessions are rows in the database — clearing or
  minting them there is quieter than password changes.

## Phase 3 — Password-reset interception chain

The reset flow (upstream-verified shape, field-proven end to end):

1. `POST /api/v4/users/password/reset/send` with `{"login_id":"victim@example.com"}`
   — **no permissions required**. The server emails a link containing a
   one-use, time-limited token (field shape:
   `/reset_password_complete?token=<token>`, link advertised as expiring in a
   day).
2. `POST /api/v4/users/password/reset` with `{"token":"<token>","new_password":"…"}`.

Interception positions, cheapest first:

- **You control DNS for the mail hostname** (field chain: a leaked key let the
  operator re-point the mail subdomain at their box). Trigger a reset, capture
  SMTP on your tunnel interface — `wireshark`, display filter
  `tcp.port == 25` — and read the token straight out of the message body.
- **You are on the mail path**: mailbox access anywhere (see [[pop3|pop3]] /
   [[smtp|smtp]] sibling units for the mail-credential side) or a relay
  position on the target's MX.
- **Reset tokens are one-use**: race the victim only if you can read their mail
  continuously; otherwise pick dormant accounts.

After takeover, walk the account: profile (email → username map), teams,
channels, direct messages, and Account Settings for personal access tokens.

## Phase 4 — Team and channel recon as a credential source

The archive is the payload (field chains, both directions):

- Read `town-square` and default team channels first — onboarding chatter,
  "the password format is …" hints, and outright credentials show up here.
- Direct messages beat channels for secrets: one field deployment had SSH
  credentials waiting in a DM conversation; another leaked a password variant
  in-channel that later cracked the database administrator's hash.
- Use the in-app search aggressively (passwords, "login", hostnames, "key");
  the server indexes message history.
- Watch for **custom plugins** in the UI (field chain): a bespoke
  "admin logs into remote servers" plugin is a credential-interception
  opportunity — steer the flow at a host you control (an SSH honeypot answers
  with usable credentials) or read its stored targets.

## Phase 5 — Webhook abuse

Verified model (upstream docs):

```bash
# incoming webhook: secret URL, Slack-compatible JSON body
curl -s -X POST http://chat.example.com/hooks/xxx-generatedkey-xxx \
  -H 'Content-Type: application/json' \
  -d '{"channel":"town-square","text":" maintenance window 02:00"}'
```

- **Finding them**: webhook URLs appear in CI configs, monitoring setups,
  scripts, and shell history on any host you reach — grep for `/hooks/`.
- **Value**: posting into any channel the creator could see, including private
  channels — phish the team from inside its own tool. Default posting username
  is `webhook` unless username overrides are enabled (config keys
  `EnableIncomingWebhooks`, `EnableOutgoingWebhooks`, override settings).
- **Outgoing webhooks** are the inverse: trigger words in public channels make
  the *server* POST to integrator-chosen URLs — a ready-made SSRF/beacon
  primitive and a place to harvest internal URLs from config once on disk.
- With an account: Integrations menu lists what exists; create your own to
  keep a foothold that survives password resets.

## Phase 6 — Config and database loot (host foothold)

```bash
cat /opt/mattermost/config/config.json | grep -A6 SqlSettings
#   "SqlSettings": {
#     "DriverName": "mysql",
#     "DataSource": "mmuser:Passw0rd!@tcp(127.0.0.1:3306)/mattermost?...",
```

- The DSN is a complete database credential — replay it immediately
  (`mysql -u mmuser -p mattermost`).
- In the database: the `Users` table holds password hashes. Crack them against
  patterns learned in chat (field chain: an in-channel password variant cracked
  the privileged account, which then authenticated as root on the host via
  password reuse).
- Correlate config `ServiceSettings` with the running port; the local 8065
  listener is often reachable from a shell even when the proxy is hardened.

## Decision points

- Login possible → Phase 2/4/5 (tokens, archive, webhooks).
- Registration blocked, no creds → Phase 3 (reset interception) or Phase 1.2
  (temp-email) — check the mail path before brute force; rate limits bite.
- Host shell → Phase 6 first; config.json outranks everything else on the box.
- Chat present but unauthenticated surface empty → hunt webhook URLs and tokens
  on adjacent hosts; they bypass the login entirely.

## Tool mapping (in prose)

Burp Suite or ZAP for the registration/login oracle work and for steering
plugin flows; curl and jq for every `/api/v4` call above; Wireshark on the
tunnel interface for reset-mail interception; the mysql or psql client against
the DSN; hashcat for the Users-table hashes, seeded with patterns mined from
channels; ffuf or a sibling enumerator for the chat vhost. An SSH honeypot
(e.g. a simple callback listener) for custom-plugin interception.
