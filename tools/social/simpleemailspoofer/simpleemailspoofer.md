---
name: "simpleemailspoofer"
categories:
  - blackarch-social
version: "54.7075f0c"
homepage: "https://github.com/lunarca/SimpleEmailSpoofer"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-26"
build-system: scripts
license: MIT
aliases: ["simple-email-spoofer-git"]
meta-checked: "2026-11-09"
github-repo: "lunarca/SimpleEmailSpoofer"
github-stars: 563
github-pushed: "2022-04-20"
github-archived: true
github-issues: 9
github-match: "exact"
gh-checked: "2026-11-07"
worth: 0.6
worth-basis: "historical (archived/gone upstream)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# simpleemailspoofer

> Minimal Python CLI that sends an email with a fully attacker-chosen From address and display name through a relay you control.

## What it's for

SimpleEmailSpoofer does one thing: hand it an HTML body file, a target, a spoofed sender address/name and a subject, and it delivers the message through an SMTP relay (localhost:25 by default). Penetration testers use it to demonstrate whether a target domain's mail setup accepts mail claiming to be from its own executives — the classic business-email-compromise simulation. It supports attachments, images, reply-to headers, per-recipient files and a link-tracking mode backed by SQLite, but it deliberately does not bypass SPF/DKIM/DMARC-based rejection; the upstream project is retired because modern mail protections made naive spoofing ineffective. Treat it as a demonstration of missing sender validation, not a delivery-guarantee tool.

## Typical use cases

### Send a spoofed message

```
./SimpleEmailSpoofer.py -e email.html -t user@example.com -f ceo@example.com -n "Jane Doe" -j "Q4 Compensation"
```

Outcome: the HTML in `email.html` is delivered to `user@example.com` from `ceo@example.com` showing display name "Jane Doe" with the given subject, relayed via localhost:25. Watch the receiving side to see whether the spoof survives (delivered/inbox) or is rejected/quarantined — that verdict is the finding.

### Send to a list of recipients with an attachment

```
./SimpleEmailSpoofer.py -e email.html -a targets.txt -f it-support@example.com -n "IT Support" -j "Password reset" --attach invoice.pdf --important
```

Outcome: one message per address in `targets.txt`, each carrying `invoice.pdf` and an importance-flagged header, through the default relay.

### Relay through an authenticated external SMTP server

```
./SimpleEmailSpoofer.py -e email.html -t user@example.com -f news@example.com -n "News" -j "Daily digest" -s smtp.example.com -p 587 --user user --pass Password1
```

Outcome: the message goes out through `smtp.example.com:587` with the given credentials instead of local port 25 — useful when your egress network blocks outbound SMTP or the lab requires an authenticated relay.

*No corpus evidence — derived from upstream documentation.*
