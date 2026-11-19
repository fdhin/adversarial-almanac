---
name: "finduncommonshares"
categories:
  - blackarch-windows
version: "3.3.r1.gaef0f0d"
homepage: "https://github.com/p0dalirius/pyFindUncommonShares"
packaged-by: [blackarch, aur]
blackarch-builddate: "2026-10-03"
build-system: windows
license: GPL-2.0-only
meta-checked: "2026-11-09"
github-repo: "p0dalirius/pyFindUncommonShares"
github-stars: 439
github-pushed: "2026-10-01"
github-archived: false
github-issues: 1
github-match: "exact"
gh-checked: "2026-11-07"
worth: 6.4
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 8
gh-activity-checked: "2026-11-09"
---

# finduncommonshares

> Enumerates the non-default SMB shares across every computer in a Windows domain.

## What it's for

pyFindUncommonShares, by Podalirius (Rémi Gascou), is the Python equivalent of PowerView's `Invoke-ShareFinder`: it pulls the list of all computers from the domain controller via LDAP, then multithreadedly enumerates each machine's SMB shares and surfaces the "uncommon" ones — everything beyond the default administrative and IPC shares that are expected on every host. A single low-privileged domain user account is enough to run it, which makes it a quick first pass for locating shares with sensitive content, weak permissions or unexpected exposure in vast domains. Results can be filtered to shares the current user can actually read or write, and exported to JSON/XLSX/SQLite for reporting. It enumerates shares and access rights only — it does not read the files inside them.

## Typical use cases

### Find all writable shares in the domain

```
finduncommonshares -au user -ap Password1 -ad example.com --auth-dc-ip 198.51.100.10 --writable
```

Outcome: as each computer is checked, shares where the given account has write access are printed live; use `--readable` for read access instead, or omit both to list every share found.

### Report every share with your access rights

```
finduncommonshares -au user -ap Password1 -ad example.com --auth-dc-ip 198.51.100.10 --check-user-access
```

Outcome: each discovered share is annotated with whether the current user can read or write it; add `--print-unc` to also print ready-to-use `\\host\share\` paths, and `-iH` to drop hidden shares (ending in `$`) from the results.

### Produce a share report for the customer

```
finduncommonshares -au user -ap Password1 -ad example.com --auth-dc-ip 198.51.100.10 --export-xlsx shares.xlsx
```

Outcome: when enumeration completes, `shares.xlsx` is written containing IP, share name, comment, type flags, UNC path and access rights per share (`--export-json` and `--export-sqlite` produce the same data as JSON or a SQLite3 database).

### Enumerate a trusted domain with a foreign account

```
finduncommonshares -au 'EXAMPLE\user' -ap Password1 -ad target.example.com --auth-dc-ip 198.51.100.10 --check-user-access
```

Outcome: when `--auth-user` uses the `DOMAIN\user` format, that domain is used for LDAP and SMB authentication while shares are enumerated across `target.example.com` — useful over a trust relationship with an account from the first domain (quote the username so the shell preserves the backslash).

*No corpus evidence — derived from upstream documentation.*
