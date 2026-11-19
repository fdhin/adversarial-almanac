---
name: "muraena"
categories:
  - blackarch-social
version: "230.9a535ba"
homepage: "https://github.com/muraenateam/muraena"
packaged-by: [blackarch]
blackarch-builddate: "2024-11-16"
build-system: go
license: BSD
meta-checked: "2026-11-09"
github-repo: "muraenateam/muraena"
github-stars: 1094
github-pushed: "2026-08-03"
github-archived: false
github-issues: 9
github-match: "exact"
gh-checked: "2026-11-07"
worth: 4.5
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# muraena

> Almost-transparent reverse proxy that automates phishing and post-phishing activity.

## What it's for

Muraena takes the reverse-proxy approach to credential harvesting: instead of hosting a static clone of a login page, it sits between the victim and the real site, rewriting every request and response on the fly so the victim browses the genuine destination through your phishing domain. Sessions therefore keep working after submission, and captured credentials arrive alongside valid session tokens — which the bundled necrobrowser module can then use for post-phishing actions; a Telegram module gives real-time notifications and a crawler helps with dynamic paths. Configuration lives in a TOML file (start from the `config/config.toml` template: phishing and destination domains, listen ports, optional `portmapping = "443:31337"`, TLS settings), documented at muraena.phishing.click. It is harvesting infrastructure only — pair it with a mailer such as gophish for delivery.

## Typical use cases

### Start the proxy from a config file

```
muraena -config config.toml
```

Outcome: loads the TOML config and starts listening for victims on the phishing domain; traffic is transparently rewritten to the destination site, submitted credentials are captured and logged, and configured modules (necrobrowser, Telegram) engage after the catch.

### Debug response rewriting

```
muraena -config config.toml -verbose
```

Outcome: prints verbose proxy messages for each transaction — use it when a page element still points at the real domain; `-debug` goes deeper, `-version` prints the build, `-no-colors` strips ANSI output.

*No corpus evidence — derived from upstream documentation.*
