---
name: "shareenum"
categories:
  - blackarch-scanner
version: "48.db728dd"
homepage: "https://github.com/CroweCybersecurity/shareenum"
packaged-by: [blackarch]
blackarch-builddate: "2021-12-12"
build-system: scripts
license: GPL3
meta-checked: "2026-11-09"
github-repo: "CroweCybersecurity/shareenum"
github-stars: 68
github-pushed: "2022-04-20"
github-archived: false
github-issues: 11
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.3
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# shareenum

> Linux/C reimplementation of share enumeration that walks SMB shares via libsmbclient and writes the tree to CSV.

## What it's for

ShareEnum (the Crowe Cybersecurity C rewrite, unrelated to the Sysinternals
GUI tool of the same name) enumerates shares, directories, and files on
Windows systems by connecting over SMB through Samba's libsmbclient and
recursively gathering the tree — deliberately avoiding the RPC approach
most share enumerators take, which the author found slower to implement
and to run. It accepts anonymous or DOMAIN\user sessions, passes NTLM
hashes, bounds recursion depth, and can pace itself across a file of
hundreds of targets, appending everything to one CSV. Reach for it when
you want a durable, scriptable inventory of share contents rather than an
interactive console; it does not attempt logins beyond the one account you
hand it.

## Typical use cases

### Anonymous share tree to CSV

Dump what a null session can see on one host.

```
shareenum -o shares.csv 198.51.100.10
```

Outcome: connects anonymously and writes every share, directory, and file
it can list to the CSV; the target can also be a hostname or a full share
path such as `smb://dc01.example.com/NETLOGON`.

### Credentialed enumeration as a domain user

Walk shares with a real account's access.

```
shareenum -o shares.csv -u EXAMPLE\\user -p Password1 198.51.100.10
```

Outcome: same recursive listing, but bounded by that user's permissions;
domain accounts go in `DOMAIN\\user` form so the SMB session negotiates
against the right domain.

### Pass-the-hash

Use a captured NTLM hash pair instead of a password.

```
shareenum -o shares.csv -u EXAMPLE\\user -p 'LMHASH:NTHASH' 198.51.100.10
```

Outcome: a `-p` value shaped `LMHASH:NTHASH` is detected and used for
pass-the-hash, enumerating with the hash's privileges without ever
needing the plaintext.

### Batch a target file with bounded recursion

Inventory many hosts without drowning in listings.

```
shareenum -o shares.csv -u EXAMPLE\\user -p Password1 -r 5 -t 10 -d 2 targets.txt
```

Outcome: each line of the target file is enumerated in turn, recursion
capped at five levels, ten-second connect timeout and a two-second pause
between targets; output appends to the CSV, and `-s N` can later resume
by skipping the first N input lines.

*No corpus evidence — derived from upstream documentation (README).*
