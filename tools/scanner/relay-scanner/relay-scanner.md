---
name: "relay-scanner"
categories:
  - blackarch-scanner
version: "1.7"
homepage: "http://www.cirt.dk"
packaged-by: [blackarch]
blackarch-builddate: "2025-07-18"
build-system: perl
license: custom:unkown
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# relay-scanner

> CIRT.dk RelayScanner, a Perl SMTP relay tester that tries to relay mail through a server in as many bypass ways as possible.

## What it's for

Tests an SMTP server for open-relay misconfigurations the way a spammer would:
instead of one `RCPT TO` probe, it runs a battery of relay-bypass test cases
(loaded from the plugin set shipped with it) against the configured mail server
and reports which attempts succeed. This finds relays that look closed to a
naive check but still accept mail for carefully crafted recipient forms, which
is exactly what matters when auditing a customer mail server before spammers
find the same gap. It is a 2005-era tool by Dennis Rand (CIRT.DK), packaged by
BlackArch as the `relay-scanner` command; its licensing header is a custom
nonstandard banner rather than a recognized license. It only tests mail
relaying — it is not a general SMTP enumeration or brute-force tool.

## Typical use cases

### Run the full relay-bypass battery against one mail server

Describe the target in a small configuration file, then let the scanner try
every technique.

```
cat > mail.example.com.txt <<'EOF'
CustomerEmailServer=mail.example.com
CustomerEmail=admin@example.com
CustomerDomain=example.com
TesterEmail=relay-test@example.com
Port=25
EOF
relay-scanner --load mail.example.com.txt
```

Outcome: the scanner connects to `mail.example.com:25` and walks its plugin
test cases, printing for each attempt whether the server accepted or refused
the relay; any accepted attempt identifies a working open-relay path, and the
test-case name tells you which recipient rewriting the server fell for.

### Tune pacing for slow or rate-limiting MX servers

Slow the scan down when the target delays banners or throttles connections.

```
relay-scanner --load mail.example.com.txt --delay 5 --timeout 30
```

Outcome: the scanner pauses 5 seconds between test cases (default 10) and waits
up to 30 seconds per connection (default 10), which keeps graylisting or
tarpitting from producing false negatives; `--nocheck` additionally skips the
initial 220-banner validation for servers that delay their greeting.

*No corpus evidence — derived from upstream documentation (the packaged
RelayScanner 1.7 script itself — its help output and GetOptions block — and the
BlackArch PKGBUILD).*
