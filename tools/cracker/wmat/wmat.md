---
name: "wmat"
categories:
  - blackarch-cracker
version: "0.1"
homepage: "http://netsec.rs/70/tools.html"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-14"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# wmat

> Web-mail account tester: runs username/password lists against a webmail login page described by an XML pattern file.

## What it's for

WMAT (Web Mail Auth Tool, Ivan Markovic) automates credential testing against webmail login forms. Targets are not hardcoded: each webmail type is described by an XML "pattern" file that declares the form action, field names, request method and a success marker, and patterns for Horde (IMP), SquirrelMail, MDaemon, Kerio and others ship with the package — supporting a new webmail is a matter of writing another XML file. It speaks plain HTTP or SSL (prefix the URL with `https://`), can route everything through an authenticated proxy, honors a per-request timeout, and has a "passsorter" mode that derives simple password guesses from the usernames themselves when no password file is at hand. It only tests web login forms, not SMTP/IMAP/POP authentication.

## Typical use cases

### Test account lists against a Horde webmail login

Feed user and password files plus the matching pattern.

```
wmat -u users.txt -p passwords.txt --url https://webmail.example.com --pattern /usr/share/wmat/patterns/horde.wmat.xml -w hits.txt
```

Outcome: submits each username/password pair to the pattern's login action and evaluates its success marker; working credentials are printed and written to `hits.txt` (with `-w`), and `-t N` adjusts the per-request timeout in seconds. The `https://` prefix switches the engine into SSL mode.

### Spray default passwords derived from usernames

Skip the password file entirely and let passsorter generate candidates.

```
wmat --passsorter users.txt --url https://webmail.example.com --pattern /usr/share/wmat/patterns/dummy.wmat.xml --bell
```

Outcome: generates simple password guesses from each username (typical factory-default transformations) and rings the terminal bell on every valid login — a quick check for untouched webmail accounts before a real wordlist run.

### Route all attempts through an authenticated proxy

Keep your own address out of the target's logs.

```
wmat -u users.txt -p passwords.txt --url http://webmail.example.com --pattern /usr/share/wmat/patterns/mdaemon.wmat.xml --proxy 198.51.100.10:8080 --proxyup user:Password1
```

Outcome: every login request is tunneled through the given proxy, authenticating with the `--proxyup` credentials when the proxy requires them, while results are reported exactly as in a direct run.

*No corpus evidence — derived from upstream documentation.*
