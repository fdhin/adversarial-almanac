---
name: "pyadrecon"
categories:
  - blackarch-windows
version: "0.10.2"
homepage: "https://github.com/l4rm4nd/PyADRecon"
packaged-by: [blackarch]
blackarch-builddate: "2026-02-15"
build-system: windows
license: MIT
meta-checked: "2026-11-09"
github-repo: "l4rm4nd/PyADRecon"
github-stars: 68
github-pushed: "2026-07-10"
github-archived: false
github-issues: 1
github-match: "exact"
gh-checked: "2026-11-07"
worth: 7.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 335
gh-activity-checked: "2026-11-09"
---

# pyadrecon

> LDAP-driven Active Directory survey that consolidates a whole domain into CSVs, an Excel workbook and an HTML dashboard.

## What it's for

PyADRecon (by l4rm4nd) is a Python 3 reimplementation of ADRecon for red and blue teams: over LDAP(S) it collects forest/domain structure, users, groups and memberships, computers, trusts, sites and subnets, domain controllers, GPOs, delegated rights, kerberoastable and AS-REP-roastable accounts and more, writing per-module CSVs plus a consolidated Excel report and an interactive HTML dashboard. It authenticates either with NTLM (username/password) or with Kerberos by handing it an existing TGT ccache file — the Kerberos path also succeeds against domain controllers that enforce LDAP channel binding, which breaks NTLM binds from ldap3. It always tries LDAPS on 636 first and falls back to plain LDAP 389 unless --ssl forbids the fallback. Modules are selectable with --collect; a few (for example the raw protected-groups dump) require domain-admin-level rights.

## Typical use cases

### Full domain survey with password authentication

```
pyadrecon -dc 198.51.100.10 -u user -p 'Password1' -d example.com
```

Outcome: collects every module over LDAP(S) and writes the CSV set, an Excel workbook and an HTML dashboard into a timestamped PyADRecon-Report-* directory.

### Recon with a Kerberos TGT (channel-binding safe)

```
pyadrecon -dc dc01.example.com -u user -d example.com --auth kerberos --tgt-file user.ccache
```

Outcome: binds with the supplied ticket instead of NTLM, which works against hardened DCs where plain NTLM LDAP binds fail with strongerAuthRequired.

### Collect only selected modules

```
pyadrecon -dc 198.51.100.10 -u user -p 'Password1' -d example.com --collect users,groups,kerberoastable,asreproastable
```

Outcome: limits the run to the named modules — a fast attack-surface pass over roastable and SPN accounts without a full collection.

### Rebuild the Excel report from existing CSVs

```
pyadrecon --generate-excel-from PyADRecon-Report-20260101-1200 -o report.xlsx
```

Outcome: merges an earlier output directory's CSV files into a fresh Excel workbook in standalone mode, with no connection to a domain controller.

*No corpus evidence — derived from upstream documentation.*
