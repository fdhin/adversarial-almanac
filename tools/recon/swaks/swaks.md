---
name: "swaks"
categories:
  - information-gathering
version: "20240103.0-3"
homepage: "https://jetmore.org/john/code/swaks/"
origin: "arch"
arch-repo: "extra"
packaged-by: [arch, kali, nixpkgs]
arch-builddate: "2025-05-03"
build-system: perl
license: GPL-2.0-or-later
meta-checked: "2026-11-09"
github-repo: "jetmore/swaks"
github-stars: 1352
github-pushed: "2024-03-05"
github-archived: false
github-issues: 47
github-match: "search"
gh-checked: "2026-11-07"
worth: 3.9
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# swaks

> Swiss Army Knife for SMTP — a command-line tool for driving, testing and probing mail servers one transaction at a time.

## What it's for

swaks (SMTP command-line test tool, by John Jetmore) speaks the full SMTP conversation on your behalf and prints the whole exchange — every command sent and every response received — so you can see exactly where a delivery succeeds or fails. It is the standard tool for testing mail relays: delivering real messages through a specific server, exercising authenticated submission, checking whether a relay accepts arbitrary recipients, or attaching files to see how a gateway treats them. It handles the protocol details (MIME structure for attachments, TLS negotiation, auth mechanisms) so a one-line invocation replaces a hand-crafted telnet session. It sends genuine mail — for delivery-acceptance testing without actually sending, stop the transaction early with --quit-after.

## Typical use cases

### Deliver a test message through a specific server

Send a crafted mail via a chosen SMTP host.

```
swaks --to user@example.com --from sender@example.com --server mail.example.com --body 'http://198.51.100.10/report'
```

Outcome: opens the connection to mail.example.com, runs HELO/MAIL/RCPT/DATA through to delivery, and prints the complete protocol transcript; the recipient receives the body text — the classic pattern for delivering a phishing-style link during an authorized engagement.

### Send mail with a custom subject header

Control headers the recipient will see.

```
swaks --to user@example.com --from support@example.com --header 'Subject: Urgent: action required' --body 'Please review.'
```

Outcome: the delivered message carries the given From address and Subject line; the transcript shows the DATA section being accepted. Re-send the same command a few times if a finicky gateway graylists the first attempts.

### Deliver a message with an attachment

Attach a file, as phishing simulations and AV/gateway tests need.

```
swaks --to user@example.com --from sender@example.com --header 'Subject: Invoice' --attach @invoice.docm
```

Outcome: swaks rewrites the message into a multipart/mixed MIME message containing invoice.docm and delivers it — the quick way to test how a mail gateway or endpoint treats a given file.

### Test authenticated submission

Verify credentials and AUTH on a submission port.

```
swaks --to user@example.com --from user@example.com --server smtp.example.com --port 587 --auth LOGIN --auth-user user --auth-password 'Password1'
```

Outcome: negotiates the LOGIN mechanism, authenticates as the given user and attempts delivery; the transcript shows exactly which AUTH mechanisms the server advertises and whether the credentials are accepted.

### Check recipients without delivering mail

Probe whether a server accepts an address, then stop.

```
swaks --to user@example.com --server mail.example.com --quit-after RCPT --hide-all
```

Outcome: the transaction is aborted right after the RCPT response, so no message is sent; a non-zero exit status means the recipient was rejected — wrapping this in a loop over an address file yields a list of verifiable recipients on that server.

*Corpus-grounded (--to/--from/--server/--body/--header delivery patterns); attachment, auth and quit-after options verified against the upstream reference.*
