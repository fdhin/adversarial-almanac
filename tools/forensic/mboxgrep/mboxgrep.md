---
name: "mboxgrep"
categories:
  - blackarch-forensic
version: "0.7.9"
homepage: "http://mboxgrep.sourceforge.net"
packaged-by: [blackarch]
blackarch-builddate: "2015-01-29"
build-system: autotools
license: GPL
meta-checked: "2026-11-09"
github-repo: "dspiljar/mboxgrep"
github-stars: 6
github-pushed: "2025-07-29"
github-archived: false
github-issues: 2
github-match: "search"
gh-checked: "2026-11-07"
worth: 2.8
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# mboxgrep

> grep(1)-like utility that selects e-mail messages from mailbox folders by regular expression.

## What it's for

mboxgrep scans mail stores and selects whole messages matching a pattern — like grep, but message-aware and format-aware. It reads and writes mbox (default), gzip- and bzip2-compressed mbox (zmbox, bz2mbox), MH, nnml, nnmh, and maildir stores; matching uses extended POSIX regular expressions by default, with basic and Perl regexps selectable (-G, -P). Matching can target the full message, headers only (-H), or body only (-B), and matching messages can be printed, counted (-c), copied into a new mailbox (-o), piped to a command (-p), or deleted (-d, with locking caveats). Typical niche: forensic triage of mail stores and bulk extraction of relevant correspondence without loading a mail client. Written and maintained by Daniel Spiljar.

*No corpus evidence — derived from upstream documentation.*

## Typical use cases

### Search a mailbox for matching messages

```
mboxgrep 'invoice' /var/mail/user
```

Outcome: prints each message in the mbox that matches the extended regexp, in full.

### Count matching messages

```
mboxgrep -c 'example.com' ~/mail/archive
```

Outcome: suppresses message output and prints only the number of matching messages in the mailbox.

### Match headers only, ignoring case

```
mboxgrep -H -i 'subject:.*urgent' /var/mail/user
```

Outcome: restricts matching to message headers and folds case, listing messages whose headers match regardless of body content.

### Extract matching messages into a new mailbox

```
mboxgrep -o matches.mbox 'security-alert' /var/mail/user
```

Outcome: writes each matching message into matches.mbox instead of stdout, producing a clean subset mailbox for review.

### Search a maildir recursively

```
mboxgrep -m maildir -r 'phishing' ~/Maildir
```

Outcome: interprets ~/Maildir as a maildir store, descends into its subfolders, and prints matching messages from all of them.
