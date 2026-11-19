---
name: "evilginx2"
categories:
  - spoof
version: "3.3.0+ds1-0parrot2"
homepage: "https://github.com/kgretzky/evilginx2"
origin: "parrot"
recipe: "parrot-repo"
packaged-by: [parrot]
build-system: go
meta-checked: "2026-11-09"
github-repo: "kgretzky/evilginx2"
github-stars: 15837
github-pushed: "2026-06-10"
github-archived: false
github-issues: 267
github-match: "exact"
gh-checked: "2026-11-07"
worth: 6.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 2
gh-activity-checked: "2026-11-09"
---

# evilginx2

> Kuba Gretzky's man-in-the-middle phishing framework: a standalone Go binary that reverse-proxies a real login site, captures credentials and session cookies as the victim logs in, and thereby defeats 2FA.

*No corpus evidence — derived from upstream documentation.*

## What it's for

The reference implementation of reverse-proxy phishing. Evilginx terminates the victim's TLS on a lookalike domain, proxies every request to the genuine site, and records what passes through — including the session cookies set after a successful login. Replaying those cookies gives the operator an authenticated session, which is why possession-based 2FA (TOTP, SMS, push) does not help: the victim completes the real second factor through the proxy. It ships its own HTTP and DNS servers (the domain's NS records must point at it, and it fetches Let's Encrypt certificates automatically), a console for configuration, and an ecosystem split: `phishlets` (YAML definitions of sites to proxy), `lures` (phishing URL generators with tracking) and `sessions` (captured credentials and tokens). Despite the vault name the package tracks the current release line — the repository has hosted Evilginx 3.x since 2023; the author's actively maintained successor with evasion features is the commercial Evilginx Pro. BSD-3 licensed; the author explicitly does not support writing phishlets — use ready-made ones and point your own domain at the server.

## Typical use cases

### Start the console with your phishlets

One sentence of intent: bring up the server with a custom phishlet collection.

```
sudo evilginx2 -p ./phishlets
```

Outcome: starts the HTTP/DNS servers and drops into the interactive console; `-p` selects the phishlets directory (the stock checkout includes only a demo `example.yaml`, so most operators populate the directory from a phishlet collection first). `-t` adds redirector pages, `-c` an alternate config directory, `-developer` generates throwaway self-signed certificates for local testing.

### Point the framework at your domain

One sentence of intent: configure the base domain and server IP the first time it runs.

```
config domain phishing.example.com
config ipv4 198.51.100.10
config autocert on
```

Outcome: sets the base domain for all phishlet hostnames and the server's external IPv4; with `autocert on`, certificates for each enabled phishlet's hostname are requested from Let's Encrypt when needed. Run `config` bare to list every variable. Prerequisite: the domain's nameserver records must delegate to this host so its DNS server can answer for the phishlet subdomains.

### Stand up a phishing site for a service

One sentence of intent: proxy a real login flow under your domain.

```
phishlets hostname example login.phishing.example.com
phishlets enable example
```

Outcome: binds the `example` phishlet to `login.phishing.example.com`, requests a TLS certificate, and starts proxying the target site; `phishlets` bare lists every phishlet with its status. The stock `example.yaml` is a demo — in practice you enable a real phishlet copied into the directory chosen with `-p`.

### Create a lure and harvest sessions

One sentence of intent: generate the URL to send and watch captures arrive.

```
lures create example
lures get-url example
sessions
```

Outcome: `lures create` mints a tracked phishing URL (printed by `lures get-url`; `lures set` customizes path, redirect and filtering), and every visitor who logs in through it produces an entry in `sessions` with credentials, captured cookies and the full visit history — import the cookies into a browser to reuse the authenticated session. `phishlets hide example` blanks the page and redirects scanners while a campaign is in flight.
