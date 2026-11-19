---
name: "nullinux"
categories:
  - blackarch-recon
version: "124.9f8727a"
homepage: "https://github.com/m8r0wn/nullinux"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-13"
build-system: scripts
license: MIT
meta-checked: "2026-11-09"
github-repo: "m8r0wn/nullinux"
github-stars: 580
github-pushed: "2024-06-19"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.4
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# nullinux

> SMB null-session enumerator: pulls users, groups, shares, and OS/domain info from Linux/Samba hosts via anonymous or credentialed sessions.

## What it's for

nullinux (m8r0wn) enumerates internal SMB services. Given one or multiple hosts, it attempts an anonymous (null) session when no credentials are supplied, or an authenticated one when they are, and enumerates OS and domain information, users and groups (including multi-threaded RID cycling to brute-force user accounts out of the SAM/AD database), shares, and the contents of share root directories. It is a wrapper around Samba's `smbclient` and `rpcclient`, so those tools and their behaviors are the engine underneath. Output is console listings plus a deduplicated `nullinux_users.txt` file formatted for feeding password spraying or brute-force tooling. It does not exploit anything — it only reads what the session is allowed to read, which on misconfigured hosts is everything.

## Typical use cases

### Full null-session enumeration of a host

Sweep users, groups, and shares in one shot.

```
nullinux target
```

Outcome: attempts an anonymous SMB session and reports OS/domain details, enumerated domain users and groups, share names, and root-directory listings of accessible shares; found users are appended to `nullinux_users.txt`, deduplicated across runs — a one-command answer to "what does this box leak anonymously?"

### Shares or users only

Narrow the enumeration to one category.

```
nullinux -shares -users target
```

Outcome: runs only the share enumeration and user/group enumeration modules (either flag alone restricts to that module), skipping the rest — quicker and quieter when you already know what you need.

### RID cycling for hidden usernames

Recover user accounts that listing APIs do not return.

```
nullinux -r -range 500-1050 -T 20 target
```

Outcome: performs RID cycling only (`-r`), stepping relative IDs 500 through 1050 with up to 20 threads (`-T`, default 15) to guess valid account names from SID responses even when user enumeration is blocked; successful names land in `nullinux_users.txt`. `-q` is the fast pre-tuned variant of this.

### Enumerate with credentials

Use a known account for deeper listing.

```
nullinux -u user -p 'Password1' -shares -v -o nullinux_users.txt target
```

Outcome: authenticates as the given user (uppercase `-U`/`-P` also accepted), lists shares visible to that account with verbose output (`-v`), and writes the users file to the explicit name given by `-o` instead of the default.

*No corpus evidence — derived from upstream documentation (usage and flags from the packaged README's argument reference).*
