---
name: "ngrok"
categories:
  - blackarch-tunnel
version: "3.33.1"
homepage: "https://ngrok.com/"
packaged-by: [blackarch, aur, nixpkgs]
blackarch-builddate: "2025-11-29"
build-system: scripts
license: LicenseRef-custom
meta-checked: "2026-11-09"
github-repo: "inconshreveable/ngrok"
github-stars: 24382
github-pushed: "2024-04-26"
github-archived: true
github-issues: 0
github-match: "search"
gh-checked: "2026-11-07"
worth: 0.9
worth-basis: "historical (archived/gone upstream)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# ngrok

> Official agent for the ngrok service — puts a local port behind NAT and firewalls onto a public HTTPS or TCP address with one outbound connection.

## What it's for

ngrok exposes services running on your machine (or your LAN) to the public internet through the ngrok cloud, which is useful for webhook development and testing, quick demos, and reaching a home lab from a firewalled network: the agent dials out, so no inbound ports are needed anywhere. The packaged binary is the current ngrok v3 agent, proprietary software distributed by ngrok.com (the open-source ngrok 1.x/2.x client by the same author lives in the archived GitHub repo and is no longer what gets installed). Every running session is inspectable in a local web interface on `http://localhost:4040` and in the ngrok dashboard; free accounts get random endpoint URLs, and reserved URLs or domains are account features. An authtoken from the dashboard is required before the first tunnel.

## Typical use cases

### Authenticate the agent once

```
ngrok config add-authtoken <authtoken>
```

Outcome: the token from the ngrok dashboard is stored in the agent's config file (ngrok.yml) and all later sessions run under your account; until this is done, tunnels refuse to start.

### Put a local web service on a public URL

```
ngrok http 8000
```

Outcome: the agent connects out to ngrok and prints a public HTTPS URL (plus the inspection URL) in the terminal; requests to that URL are forwarded to localhost:8000, and both requests and responses can be inspected and replayed at http://localhost:4040.

### Expose a local HTTPS service

```
ngrok http https://localhost:8443
```

Outcome: TLS is terminated at the public ngrok URL and traffic is re-encrypted toward the local service — a local server with a self-signed certificate becomes reachable as a clean public HTTPS endpoint without certificate warnings for visitors.

### Expose SSH or another raw TCP service

```
ngrok tcp 22
```

Outcome: prints a TCP endpoint address and port (`tcp://<host>:<port>`); connect with `ssh -p <port> user@<host>` to land on the local SSH server. Works the same way for RDP, databases and other non-HTTP protocols.

### Serve under a stable, chosen URL

```
ngrok http 8080 --url https://baz.ngrok.dev
```

Outcome: the endpoint uses the account-reserved domain instead of a random one, so the public address stays the same across restarts — what you want for webhook consumers registered with a third party.

*No corpus evidence — derived from upstream documentation. (Corpus mentions are name-only: two bare tool-list entries.)*
