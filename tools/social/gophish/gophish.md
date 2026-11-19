---
name: "gophish"
categories:
  - blackarch-social
version: "815.95618469"
homepage: "https://github.com/gophish/gophish"
packaged-by: [blackarch, aur, nixpkgs]
blackarch-builddate: "2024-10-06"
build-system: go
license: MIT
meta-checked: "2026-11-09"
github-repo: "gophish/gophish"
github-stars: 14394
github-pushed: "2024-09-23"
github-archived: false
github-issues: 759
github-match: "exact"
gh-checked: "2026-11-07"
worth: 4.5
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# gophish

> Open-source phishing framework with a web console and REST API for full engagements.

## What it's for

Gophish is a self-contained phishing toolkit: one Go binary starts a web admin console (default https://127.0.0.1:3333) plus the listeners used to send mail and track victims. Operators register SMTP sending profiles, upload email templates and landing pages (imported or cloned), import recipient groups, then launch campaigns that record per-recipient opens, link clicks, and credentials submitted on the landing page. Reach for it when an engagement needs repeatable campaigns and reporting rather than a one-off mail blast; it is not an OSINT or harvesting tool. The API key from the Settings page exposes every object over a REST API served on the same port as the console.

## Typical use cases

### Start the server and open the admin console

```
./gophish
```

Outcome: reads config.json, starts the admin interface on https://127.0.0.1:3333, and prints a one-time random admin password in the log — log in as user `admin` with that password and change it. Instances older than v0.10.1 instead carry the documented default credentials `admin` / `gophish`, the first thing to try when auditing a deployment you did not build.

### Launch a campaign through the REST API

```
curl -k -X POST "https://127.0.0.1:3333/api/campaigns/?api_key=$GOPHISH_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{"name":"q4-awareness","template":{"name":"DocShared"},"page":{"name":"PortalClone"},"smtp":{"name":"relay-1"},"groups":[{"name":"staff"}]}'
```

Outcome: returns the created campaign as JSON with its id and launch status, and gophish begins sending to the referenced group; the template, landing page, sending profile, and group must already exist (created in the console or via the same API).

### Pull campaign results

```
curl -k "https://127.0.0.1:3333/api/campaigns/1/results?api_key=$GOPHISH_API_KEY"
```

Outcome: JSON summary of campaign 1 — per-recipient delivery status, open and click events with timestamps, and any credentials captured by the landing page.

### Verify webhook event signatures

Confirm that an event delivered to your automation endpoint really came from your gophish instance.

```
python3 -c 'import hmac,hashlib,sys
body, secret, got = sys.stdin.buffer.read(), b"webhook-secret", sys.argv[1]
sig = "sha256=" + hmac.new(secret, body, hashlib.sha256).hexdigest()
print("valid" if hmac.compare_digest(sig, got) else "forged")' \
  "sha256=<X-Gophish-Signature header value>" < event.json
```

Outcome: prints `valid` or `forged` — gophish signs every webhook POST with an `X-Gophish-Signature: sha256=<hex>` header holding the HMAC-SHA256 of the raw request body keyed with the secret configured on the webhook; drop any event whose signature does not match.

*Corpus-grounded — console setup and default credentials, campaign delivery, and webhook signature handling mined from corpus evidence; API surface and webhook format verified against the upstream source.*
