---
name: "smtptx"
categories:
  - blackarch-scanner
version: "1.0"
homepage: "http://www.0x90.se/"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-26"
build-system: scripts
license: GPL3
meta-checked: "2026-11-09"
github-repo: "0X90SE/smtptx"
github-stars: 0
github-pushed: "2021-10-10"
github-archived: false
github-issues: 0
github-match: "search"
gh-checked: "2026-11-07"
worth: 1.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# smtptx

> Minimal mail sender for SMTP testing from a pentester's seat — resolves MX records itself, so no relay address is needed.

## What it's for

SMTPTX (circle@0x90.se, packaged version 1.0 Beta, GPLv3) sends simple email and performs basic SMTP testing. It resolves the MX records for the recipient domain itself, connects to the resulting mail server and delivers the message — no need to know the MTA address beforehand, unlike `sendemail`-style tools. It can also target a specific SMTP server with authentication and TLS, which makes it useful for verifying credentials, relay behavior and delivery paths. It is explicitly not a bulk mailer. Flags below are from the packaged 1.0 release; the upstream GitHub repository carries a later 2.x with a slightly different flag set.

## Typical use cases

### Send a test message via MX resolution

```
smtptx -t user@example.com -f tester@example.com -s "Mail test" -m "Testing delivery path"
```

Outcome: looks up the MX records for `example.com`, connects to the resulting mail server on port 25 and delivers the message; add `-v` to print each resolution and delivery step.

### Submit through a specific server with auth and TLS

```
smtptx -S mail.example.com -p 587 -U user -P 'Password1' -e -t user@example.com
```

Outcome: skips MX resolution and submits the message through `mail.example.com:587` with the given credentials, negotiating TLS (`-e`) when the server supports it — confirms whether the credentials work and whether the server accepts the submission.

### Resolve mail exchangers without sending

```
smtptx -r -t user@example.com
```

Outcome: prints the SMTP servers resolved via MX for the domain and exits without sending anything — a quick check of where a domain's mail actually flows.

*No corpus evidence — derived from upstream documentation (packaged 1.0 release README and script usage).*
