---
technology: "GoPhish"
kind: bug-bounty-methodology
audience: ai-agent
version: 1.0
source: "corpus-grounded (command-context gate, internal coverage ledger) + upstream-verified: GoPhish official docs — Getting Started (default admin/gophish through v0.10.1; temporary log-printed admin password and forced reset after; admin server https://127.0.0.1:3333 default bind; phishing server 0.0.0.0:80; GOPHISH_INITIAL_ADMIN_PASSWORD) and Webhooks (X-Gophish-Signature: sha256= HMAC-SHA256 over the raw JSON body with the endpoint secret; event vocabulary) + webhook/webhook.go source (SignatureHeader/Sha256Prefix, hmac over json.Marshal payload), 2026-11-12"
provenance: "corpus-grounded: 4 contributing writeups (internal coverage ledger, gate documented in header); upstream CVEs verified against primary sources during enrichment; basis of this page summarized from the unit ledger"
---

Companion page: [[gophish|GoPhish overview]] — what the platform is and where you will meet it. Tool notes live in the tools vault; Appendix A names tools in prose.

# GoPhish bug bounty methodology

**Scope:** GoPhish instances as targets (exposed admin console, API key abuse, webhook/integration abuse) and as operator-side infrastructure whose defaults you must not fumble. **Out of scope:** phishing technique itself and mail-delivery infrastructure (belongs to the mail units), generic SQL injection in third-party products that merely received webhook JSON — the GoPhish finding is the signature/secret path that got you there.

**Authorization:** an instance you can log into holds real people's credentials and target lists. Read to establish impact, never export harvested data, and treat red-team infrastructure findings as high-urgency disclosures (their compromise burns an active operation).

## Decision tree (one screen)

1. High HTTPS port with self-signed cert + GoPhish login → Phase 2 (credential eras).
2. Admin session or API key → Phase 3 (structured loot; templates first — they map infrastructure).
3. Wiki/docs/automation surface mentions webhooks → Phase 4 (find the exported workflow; extract secret).
4. Secret in hand → Phase 4 (forge signature; probe downstream sinks with the event vocabulary).
5. Nothing exposed → Phase 5 (operator-side posture of your own instance).

## Phase 1 — Fingerprint the instance

- Wide TCP scan; GoPhish admin consoles sit on nonstandard HTTPS ports — cleartext HTTP to the port returns an opaque response while the TLS handshake serves a GoPhish certificate; port scanners with service detection name it outright.
- Fetch `https://198.51.100.20:PORT/` (accept the self-signed cert): the login page is distinctive, and static asset paths (`/api/`, `/static/js/...app...`, `/login`) confirm platform and rough version.
- Check both listeners: the phishing server (port 80 by default) exposes landing pages with tracking parameters (`?rid=...`) — evidence of live campaigns; the admin listener on 3333 is loopback unless deliberately rebound.

## Phase 2 — Credential eras and access

```text
admin : gophish          # documented default through v0.10.1 — always try first
# newer releases: one-time password printed in server logs at first start,
# forced reset on first login; operators "reset" to weak org-themed passwords
```

- Version cues: forced password-change flow = post-0.10.1; absent flow + default pair works = old build (report the version as a finding in itself).
- No login? The API accepts the key alone: keys appear in operator notes, CI scripts, and browser history — `Authorization: <key>` against `/api/campaigns/` is the probe.
- With any shell on the host: `config.json` (listener bindings, contact address), the SQLite store (`gophish.db` — templates, users, keys), and server logs (temp admin password lines) are the loot files.

## Phase 3 — Structured loot (the impact paragraph)

Priority order, each read straight from the UI or API:

1. **Email templates** — sender addresses, links, landing URLs: build the vhost/hostname list, add to hosts file, re-enumerate (harvesting clones of real sites, additional admin panels).
2. **Sending profiles** — SMTP host, port, username, and stored credentials: valid mail relay for the org (pivot to the SMTP unit's methodology).
3. **Users & groups** — target lists: scope of the operation, valid account-name formats.
4. **Landing pages + campaign results** — harvested credential pairs: impact proven, but do not exfiltrate; screenshot one redacted row.

## Phase 4 — Webhook signature forgery into downstream automation

Webhook recipients verify `X-Gophish-Signature: sha256=<hex>` = HMAC-SHA256 over the exact raw JSON body under the endpoint secret. The secret is stored on both ends — and receiver-side it lives inside the automation workflow's definition, which is exactly the artifact teams export, attach to tickets, or host on internal wikis ("live execution history" downloads included).

```text
# 1. obtain the workflow export (wiki attachment, /webhook/<uuid> docs page, API)
#    secret appears in the HMAC node:  "secret": "..."
# 2. craft the event; compute the signature over the RAW body bytes:
printf '%s' '{"campaign_id":1,"email":"probe@example.com","message":"Clicked Link"}' \
  | openssl dgst -sha256 -hmac "<SECRET>"        # -> compare to header form sha256=<hex>
# 3. POST to the webhook endpoint with x-gophish-signature: sha256=<hex>
```

- Downstream sinks to probe once signatures pass: workflow nodes that `executeQuery` against a database (template-interpolated fields like `email` are classic error-based SQL injection — `updatexml`/`extractvalue` error oracles return data in the response), and nodes that write files or invoke commands.
- Error-based extraction pattern (error text echoed back by the workflow's debug node):

```json
{"campaign_id": 1, "message": "Clicked Link",
 "email": "\" OR updatexml(1, concat(0x7e, (SELECT schema_name FROM information_schema.schemata LIMIT 1), 0x7e), 1)-- -"}
```

- The general rule to report: **a signed integration is an auth boundary whose key material is duplicated into every copy of the receiver's config** — the fix belongs on the integration, not the webhook.

## Phase 5 — Operator-side posture (your own instance)

- Bind the admin listener to loopback or an operator VPN address; the default `config.json` ships loopback — do not "fix" reachability by binding all interfaces for engagement week.
- Rotate the initial admin credential out of the log-printed era; set a contact address so your campaign mail is attributable; keep the API key out of scripts that end up in repos.
- Assume your instance is a target for the same chains: templates on a compromised instance hand the opposition your vhost list.

## Command quick reference

```text
https://198.51.100.20:3333/            # default admin listener (loopback bind unless rebound)
GET  /api/campaigns/  + Authorization: <key>   # API = full admin
X-Gophish-Signature: sha256=<HMAC-SHA256(raw body, secret)>   # webhook forgery
openssl dgst -sha256 -hmac "<SECRET>"          # signature computation
```

## Appendix A — Tools in prose

Port discovery and service fingerprinting with nmap; TLS inspection and request crafting with curl or Burp Suite; HMAC recomputation with openssl or CyberChef; workflow-JSON parsing with jq; database error-oracle extraction by hand or with sqlmap once the signature gate is passed. The phishing stack itself — template authoring, payload delivery and tracking — is the platform's own documentation territory.
