---
name: "ident-user-enum"
categories:
  - recon
version: "1.0-0parrot1"
homepage: "https://pentestmonkey.net/tools/user-enumeration/ident-user-enum"
origin: "parrot"
recipe: "parrot-repo"
packaged-by: [parrot]
build-system: scripts
meta-checked: "2026-11-09"
github-repo: "pentestmonkey/ident-user-enum"
github-stars: 36
github-pushed: "2015-05-29"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# ident-user-enum

> Queries the ident service (113/TCP) to reveal which OS-level user owns each listening TCP process on a host.

## What it's for

ident-user-enum (pentestmonkey) is a small Perl script that connects to a target's ident daemon and asks, for each TCP port you name, the operating-system username of the process listening there. On hosts that still run an ident service this turns a port scan into a username list, which is useful for user enumeration in the early stages of an assessment. It does nothing beyond ident lookups — no scanning, and ports whose process ident cannot identify come back as `<unknown>`.

## Typical use cases

### Enumerate users behind open ports

Ask the ident service which users own the services found open.

```
ident-user-enum.pl 198.51.100.10 22 25 80
```

Outcome: one line per requested port in the form `198.51.100.10:22  username` (or `<unknown>` when ident has no answer), yielding candidate usernames for further testing.

*No corpus evidence — derived from upstream documentation.*
