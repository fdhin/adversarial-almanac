---
name: "owabf"
categories:
  - blackarch-cracker
version: "1.3"
homepage: "http://netsec.rs/70/tools.html"
packaged-by: [blackarch]
blackarch-builddate: "2025-12-22"
build-system: scripts
license: custom:unknown
github-repo: "dejanlevaja/owa-brute-forcer"
github-match: "exact"
meta-checked: "2026-11-09"
worth: 1.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-stars: 0
github-pushed: "2017-02-22"
github-archived: false
github-issues: 0
gh-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# owabf

> Single-purpose Python bruteforcer for Outlook Web Access (OWA) form logins.

*No corpus evidence — derived from upstream documentation.*

## What it's for

owabf is a small Python 2 script by Dejan Levaja (netsec.rs) that guesses credentials for Microsoft Exchange Outlook Web Access — the webmail login form — over HTTPS. You give it a server FQDN, a list of usernames, and either one shared password list or one password list per user (a file named after each user), and it posts each combination to the OWA form, parses the response and reports accepted logins. It can log progress and resume from that log, and pace requests with a random delay. It speaks only the OWA form login — the author's own TODO list still lists basic-auth support, multithreading and better error handling as missing — and it has no lockout awareness beyond the delay you configure.

## Typical use cases

### Brute-force OWA with shared user and password lists

One password list tried against every account.

```
owabf -s https://owa.corp.example.com -u users.txt -p passwords.txt
```

Outcome: attempts every username/password combination against the OWA form at owa.corp.example.com, auto-detecting the OWA form version when `-v` is not given, and prints each accepted credential pair as it is found.

### Try one dedicated password list per user

Each account gets its own candidate passwords, e.g. from a per-account password generator.

```
owabf -s https://owa.corp.example.com -u users.txt
```

Outcome: loops over users.txt and, for each user (say `jdoe`), reads candidate passwords from a file named `jdoe` in the working directory; if a per-user list is missing the run stops with a `There is no <name> passlist file` message, so generate the lists first.

### Throttle, log and resume a long run

Slow the attack down and survive interruptions.

```
owabf -s https://owa.corp.example.com -u users.txt -p passwords.txt -t 30 -l owa.log
owabf -s https://owa.corp.example.com -u users.txt -p passwords.txt -r owa.log
```

Outcome: the first command waits a random interval (up to about 30 seconds) between attempts and records progress to owa.log; the second continues from where the log left off after an interruption instead of restarting. `-v 1` / `-v 2` pins a specific OWA form generation when auto-detection picks wrong.
