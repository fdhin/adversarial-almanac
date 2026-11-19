---
name: "lodowep"
categories:
  - blackarch-cracker
version: "1.2.1"
homepage: "http://www.cqure.net/wp/lodowep/"
packaged-by: [blackarch]
blackarch-builddate: "2016-12-31"
build-system: scripts
license: GPL
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# lodowep

> Audits account password strength on Lotus Domino web servers by testing user/password lists against the web login.

## What it's for

Lodowep is a Java tool by Patrik Karlsson (cqure.net) for analyzing password strength of accounts on a Lotus Domino web server. Given a file of usernames, a file of passwords and a target URL, it attempts the combinations over HTTP against Domino web logins, supporting both session-based and basic authentication. Concurrency, a report file and verbosity are configurable. It is a login-audit tool for a legacy platform — it does not touch Domino's proprietary Notes RPC ports, only the web interface. The original cqure.net site is retired; the tool ships as `lodowep.jar` in distro packages.

## Typical use cases

### Test a user list against a password list

```
java -jar lodowep.jar -u users.txt -p passwords.txt -t http://target.example.com/webmail/mailbox.nsf
```

Outcome: attempts each user/password combination against the Domino web login URL; accounts that authenticate with weak passwords are reported, proving the password policy is too lax.

### Run with parallel threads, a report file, and verbosity

```
java -jar lodowep.jar -u users.txt -p passwords.txt -t http://target.example.com/webmail/mailbox.nsf -n 10 -r report.txt -v
```

Outcome: `-n 10` runs 10 concurrent threads, `-r report.txt` writes results to a report file, and `-v` increases verbosity (given twice, debug output).

*No corpus evidence — derived from upstream documentation.*
