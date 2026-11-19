---
name: "smbclient-ng"
categories:
  - blackarch-networking
version: "3.1.0.r4.g755200b"
homepage: "https://github.com/p0dalirius/smbclient-ng"
packaged-by: [blackarch, nixpkgs]
blackarch-builddate: "2026-10-04"
build-system: python
license: GPL-3.0-only
meta-checked: "2026-11-09"
github-repo: "p0dalirius/smbclient-ng"
github-stars: 1078
github-pushed: "2026-09-29"
github-archived: false
github-issues: 6
github-match: "exact"
gh-checked: "2026-11-07"
worth: 7.5
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 21
gh-activity-checked: "2026-11-09"
---

# smbclient-ng

> Interactive, impacket-powered SMB client with a shell that behaves like a modern FTP client.

## What it's for

smbclient-ng is a Python interactive SMB client built on impacket, intended as a faster and friendlier alternative to the classic smbclient: connect to a share host, walk directories, read and write files, list shares and ACLs, and manage parallel sessions from one prompt. It supports password, NT-hash (pass-the-hash), Kerberos and AES-key authentication. It does not perform exploitation — it is a file-share and enumeration workhorse for administration or post-compromise work.

## Typical use cases

### Open an interactive shell on a share host

```
smbclientng --host 198.51.100.10 --domain LAB --user Administrator --password 'Password1'
```

Outcome: drops into an interactive prompt on the target; `shares` lists what the host serves, `use SHARE` enters a share, then `dir`, `get -r folder`, `put file`, `acls`, `metadata` and `tree` operate on it directly.

### Authenticate with an NT hash instead of a password

Reuse a captured hash (pass-the-hash) to open the same shell.

```
smbclientng --host 198.51.100.10 --domain LAB --user svc_backup --hashes :NTHASH_HERE
```

Outcome: opens the interactive shell authenticated via the supplied LM/NT hashes — no plaintext password required.

### Run a single command without the shell

Script one action against a host and exit.

```
smbclientng --host 198.51.100.10 --no-pass -C "shares"
```

Outcome: executes the `shares` command non-interactively, prints the host's share list, and exits — suitable for loops and automation.

*No corpus evidence — derived from upstream documentation.*
