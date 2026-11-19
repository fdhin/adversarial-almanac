---
name: "domi-owned"
categories:
  - blackarch-webapp
version: "41.583d0a5"
homepage: "https://github.com/coldfusion39/domi-owned"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-12"
build-system: scripts
license: MIT
meta-checked: "2026-11-09"
github-repo: "coldfusion39/domi-owned"
github-stars: 118
github-pushed: "2020-07-10"
github-archived: false
github-issues: 10
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.5
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# domi-owned

> Attack toolkit for IBM/Lotus Domino servers: fingerprint, enumerate, brute force, dump hashes, and open a console.

## What it's for

Domi-owned compromises IBM/Lotus Domino servers (tested against 8.5.2 through 9.0.1 on Windows and Linux). It fingerprints the server and its names.nsf/webadmin.nsf exposure, enumerates accessible directories, reverse-brute-forces accounts, dumps account hashes (Domino 5, 6 and 8 formats) and provides an interactive Quick Console that executes native OS commands through webadmin.nsf. Authenticated actions need an account with access to the NSF databases; unauthenticated fingerprinting works when the databases are open.

## Typical use cases

### Fingerprint a Domino server

```
domi-owned fingerprint http://target.example.com
```

Outcome: reports the Domino version and whether names.nsf and webadmin.nsf require authentication — the triage step that decides which follow-up actions are possible.

### Enumerate accessible directories

```
domi-owned enumerate http://target.example.com --username user --password Password1 --wordlist dirs.txt
```

Outcome: walks the common and default Domino directories (your list via --wordlist) and reports which are reachable with the given credentials.

### Reverse brute force accounts

```
domi-owned bruteforce http://target.example.com usernames.txt --password Password1
```

Outcome: tries every username in the file against names.nsf with the given password (or username-as-password when --password is omitted) and reports the accounts that authenticate.

### Dump account hashes

```
domi-owned hashdump http://target.example.com --username user --password Password1
```

Outcome: prints every account with a non-empty hash and writes the hashes to separate out-files by hash type (Domino 5, 6 and 8), ready for offline cracking.

### Get an interactive command console

```
domi-owned quickconsole http://target.example.com --username user --password Password1
```

Outcome: opens the Domino Quick Console through webadmin.nsf as an interactive shell for native Windows/Linux commands, echoing output back (via a redirected log file that is removed on exit); type exit to quit.

_No corpus evidence — derived from upstream documentation (project README)._
