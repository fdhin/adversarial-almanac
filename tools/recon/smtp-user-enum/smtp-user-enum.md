---
name: "smtp-user-enum"
categories:
  - blackarch-recon
version: "1.2"
homepage: "https://pentestmonkey.net/tools/user-enumeration/smtp-user-enum"
packaged-by: [blackarch, kali, nixpkgs]
blackarch-builddate: "2024-12-14"
build-system: scripts
license: GPL-2.0-or-later
meta-checked: "2026-11-09"
github-repo: "pentestmonkey/smtp-user-enum"
github-stars: 181
github-pushed: "2022-04-20"
github-archived: false
github-issues: 3
github-match: "search"
gh-checked: "2026-11-07"
worth: 2.8
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# smtp-user-enum

> SMTP username enumeration through EXPN, VRFY or RCPT TO — written for the default Solaris SMTP service, useful wherever those verbs answer.

## What it's for

smtp-user-enum guesses valid usernames on an SMTP server by asking the server itself: EXPN expands a mailing address into its members, VRFY verifies a recipient directly, and RCPT TO probes whether the server accepts mail for an address (which needs a believable MAIL FROM via -f). It was built against the default Solaris smtpd, where these commands are enabled out of the box, but works against any MTA that answers them. Supply a single user (-u) or a file of users (-U), and a single server (-t) or a file of servers (-T); -D appends a domain to every name, turning username enumeration into email-address enumeration.

## Typical use cases

### Confirm one full address via RCPT TO

The quiet probe — needs no EXPN/VRFY support on the server.

```
smtp-user-enum -M RCPT -f test@example.com -u target@example.com -t 198.51.100.10
```

Outcome: completes a MAIL FROM/RCPT TO exchange and reports whether the server accepts mail for the address; differing responses between a known-good and a guessed address reveal which recipients exist.

### Enumerate a user list with VRFY

```
smtp-user-enum -M VRFY -U users.txt -t 198.51.100.10
```

Outcome: issues VRFY for every name in the file and prints per-user results, distinguishing confirmed, rejected and ambiguous replies into a valid-username list.

### Guess email addresses instead of bare usernames

```
smtp-user-enum -M EXPN -D example.com -U users.txt -t 198.51.100.10
```

Outcome: appends example.com to each candidate (foo becomes foo@example.com) and expands the resulting addresses, recovering the mailbox list behind a domain.

### Sweep several mail servers

```
smtp-user-enum -M RCPT -U users.txt -T mailhosts.txt
```

Outcome: repeats the enumeration against every host in the file, producing per-server acceptance so gateways and backends can be compared.

*Grounded in corpus command-line evidence; case-folding corrected (-M for mode, since -m is max-processes) and flags verified against the packaged script's usage text.*
