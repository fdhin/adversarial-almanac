---
name: "ipmipwn"
categories:
  - blackarch-cracker
version: "6.74a08a8"
homepage: "https://github.com/AnarchyAngel/IPMIPWN"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-25"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "AnarchyAngel/IPMIPWN"
github-stars: 8
github-pushed: "2016-03-09"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 1.8
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# ipmipwn

> One-shot IPMI cipher-0 chain: default-account discovery plus backdoor creation.

## What it's for

Automates exploitation of the IPMI 2.0 cipher 0 authentication bypass against
a BMC: it checks the target is alive on UDP 623 (via nmap), uses `ipmitool`
with cipher suite 0 (`-C 0`, which authenticates with any password) to find a
default account that can list users (root, admin, ADMIN, Administrator, USERID,
guest and friends), and then provisions a new administrator-level account with
a random password. Every created backdoor is appended to `loot.log` in the
current directory. It requires `ipmitool` and `nmap` on the host and is a
Python 2 script (installed as `ipmipwn`).

## Typical use cases

### Exploit cipher 0 and implant a backdoor account

Run the whole chain against a BMC.

```
ipmipwn 198.51.100.10
```

Outcome: checks UDP 623, walks the built-in default-account list with
cipher-0 `user list` queries, then creates a `backdoor<N>` user with
administrator privileges (privilege level 4) and a random 14-character
password. On success it prints the ssh login line to use and appends
`198.51.100.10:backdoorN:password` to `loot.log`; running it without an
argument prints the usage summary.
