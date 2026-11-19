---
name: "ldapconsole"
categories:
  - blackarch-networking
version: "2.1.0.r12.g1b0886a"
homepage: "https://github.com/p0dalirius/ldapconsole"
packaged-by: [blackarch]
blackarch-builddate: "2026-05-02"
build-system: scripts
license: GPL-2.0-only
meta-checked: "2026-11-09"
github-repo: "p0dalirius/ldapconsole"
github-stars: 70
github-pushed: "2026-04-18"
github-archived: false
github-issues: 4
github-match: "exact"
gh-checked: "2026-11-07"
worth: 5.8
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 9
gh-activity-checked: "2026-11-09"
---

# ldapconsole

> Interactive LDAP console for exploring a Windows domain with password, pass-the-hash or Kerberos authentication.

## What it's for

ldapconsole (by Podalirius) runs custom LDAP queries against a Windows domain controller from a Python CLI. In its default interactive mode it opens a colored console with preset queries for common domain objects; in non-interactive mode (`-q`) it runs a single LDAP filter and prints results, optionally writing them straight to an XLSX spreadsheet (`-x`). Authentication covers plain username/password, LM:NT hash pass-the-hash (`-H`), Kerberos ticket from the local ccache (`-k`), or AES keys, and LDAPS is available with `--use-ldaps`. Reach for it when enumerating AD needs ad-hoc filters and attributes rather than a fixed report; it does not modify directory data — it is read-only querying. The BlackArch package wraps the script as `ldapconsole`.

## Typical use cases

### Open the interactive console against a domain

```
ldapconsole -u user -p 'Password1' -d corp.example.com --dc-ip 198.51.100.10
```

Outcome: authenticates over LDAP to the controller and drops into the interactive console, where preset queries enumerate users, groups, computers and more, with colored output.

### Run one LDAP filter and export matching objects to Excel

```
ldapconsole -d corp.example.com -u user -p 'Password1' --dc-ip 198.51.100.10 -q '(objectCategory=Computer)' -a operatingSystem -a operatingSystemVersion -x computers.xlsx
```

Outcome: executes the filter non-interactively, prints the requested attributes per matching object and writes the same table to `computers.xlsx` for reporting; `-a` can be repeated to add columns.

### Authenticate with a captured NT hash (pass-the-hash)

```
ldapconsole -d corp.example.com -u user -H :NTHASH --dc-ip 198.51.100.10
```

Outcome: authenticates without knowing the plaintext password using the `LMHASH:NTHASH` format (empty LM half here), then behaves exactly like a password session — interactive console by default, `-q` for scripted queries.

### Use Kerberos tickets or LDAPS

```
KRB5CCNAME=user.ccache ldapconsole -d corp.example.com -u user -k --no-pass --dc-ip 198.51.100.10
```

Outcome: pulls credentials from the Kerberos ccache instead of a password (combine with `--use-ldaps` to tunnel the same session over TLS on 636) — the standard path when a ticket was harvested separately.

*No corpus evidence — derived from upstream documentation.*
