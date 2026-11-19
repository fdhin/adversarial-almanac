---
name: "mailsend-go"
categories:
  - blackarch-spoof
version: "154.99ff34e"
homepage: "https://github.com/muquit/mailsend-go"
packaged-by: [blackarch]
blackarch-builddate: "2026-10-31"
build-system: go
license: MIT
meta-checked: "2026-11-09"
github-repo: "muquit/mailsend-go"
github-stars: 196
github-pushed: "2026-11-01"
github-archived: false
github-issues: 36
github-match: "exact"
gh-checked: "2026-11-07"
worth: 6.9
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 21
gh-activity-checked: "2026-11-09"
---

# mailsend-go

> Multi-platform command-line SMTP mailer in Go — text/HTML bodies, attachments, custom headers and bulk recipient lists, with STARTTLS/SSL, basic auth and XOAUTH2.

## What it's for

Sending mail from scripts and terminals over SMTP: one static Go binary replaces the older C `mailsend` (which is feature-frozen). Options are grouped into pseudo-subcommands on one command line — `auth` (credentials or `-oauth2` token), `body` (message text, HTML, or a file), `attach` (repeatable, with inline support for embedded images), and `header` (repeatable custom headers). Well-known providers can be preconfigured with `-use` (gmail, yahoo, outlook, gmx, zoho, aol), which fills in the server, port and SSL settings. Secrets can be kept off the command line via the `SMTP_USER_PASS` and `SMTP_OAUTH_TOKEN` environment variables. It can also just interrogate a server: `-info` prints capabilities, and `-printCerts` dumps the TLS certificate chain.

## Typical use cases

### Inspect an SMTP server before sending

```bash
mailsend-go -info -smtp smtp.example.com -port 587 -printCerts
```

Outcome: connects, negotiates (STARTTLS here; add `-ssl` with port 465 for implicit TLS), prints the server's supported extensions and the certificate chain, then exits without sending anything. `-use outlook` would substitute the provider's standard host/port/SSL values automatically.

### Send a plain-text message

```bash
mailsend-go -sub "Nightly report" -smtp smtp.example.com -port 587 \
  auth -user user@example.com -pass 'Password1' \
  -from user@example.com -to target@example.com \
  body -msg "report attached\nsee attachment"
```

Outcome: authenticates via ESMTP and delivers the message; the `\n` inside `-msg` becomes a real newline in the body. Setting `SMTP_USER_PASS` avoids the password on the command line.

### Send HTML mail with an attachment

```bash
mailsend-go -sub "Report" -smtp smtp.example.com -port 587 \
  auth -user user@example.com -pass 'Password1' \
  -from user@example.com -to target@example.com \
  body -msg "<b>hello, world!</b>" \
  attach -file report.pdf
```

Outcome: delivers an HTML body (MIME type detected automatically) with the PDF base64-attached as a regular attachment; repeat the `attach` block for multiple files, or add `-inline` inside it to embed an image in the page.

### Send to a list of recipients

```bash
mailsend-go -sub "Newsletter" -smtp smtp.example.com -port 587 \
  auth -user user@example.com -pass 'Password1' \
  -from user@example.com -to target@example.com \
  body -msg "monthly news" \
  -list recipients.txt
```

Outcome: each line of `recipients.txt` in `Name, email` form gets the message; blank names are fine (line starts with a comma). Useful for bulk delivery without a mailing-list server.

### Deliver with XOAUTH2 instead of a password

```bash
export SMTP_OAUTH_TOKEN='your_access_token'
mailsend-go -sub "Test" -smtp smtp.gmail.com -port 587 \
  auth -user user@gmail.com -oauth2 \
  -from user@gmail.com -to target@example.com \
  body -msg "<b>hello, world!</b>"
```

Outcome: authenticates with the Gmail XOAUTH2 SASL mechanism using the token from the environment variable (no password involved), then sends the HTML body. Token acquisition is handled by the companion `oauth-helper` tool, which pipes straight into this flow.

*No corpus evidence — derived from upstream documentation.*
