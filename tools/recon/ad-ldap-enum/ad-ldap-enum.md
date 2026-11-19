---
name: "ad-ldap-enum"
categories:
  - blackarch-recon
version: "90.caee43d"
homepage: "https://github.com/CroweCybersecurity/ad-ldap-enum"
packaged-by: [blackarch, nixpkgs]
blackarch-builddate: "2026-05-02"
build-system: scripts
license: MIT
meta-checked: "2026-11-09"
github-repo: "CroweCybersecurity/ad-ldap-enum"
github-stars: 313
github-pushed: "2026-05-01"
github-archived: false
github-issues: 1
github-match: "exact"
gh-checked: "2026-11-07"
worth: 5.3
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 2
gh-activity-checked: "2026-11-09"
---

# ad-ldap-enum

> Fast LDAP-based dump of Active Directory users, computers, and complete group membership to CSV (optionally Excel), built for large domains.

## What it's for

ad-ldap-enum collects users, computers, and their group membership from Active Directory by running targeted LDAP queries against a domain controller. It was written for large AD environments where enumeration through other tooling was too slow: it requests the specific attributes needed and builds out group membership directly, writing structured CSVs you can pivot on offline. It supports NTLM (password or NT hash), Kerberos, and anonymous binds, plain LDAP or LDAPS with channel binding, plus OPSEC controls (delay/jitter between queries, page-size tuning) for engagements where query noise matters. It is purely a collector — analysis happens in whatever consumes its CSV/Excel output. Since v3.0 credentials are given impacket-style (`domain/user:password@server`); the older `-d/-l/-u/-p` flag interface still works.

## Typical use cases

### Enumerate a domain with a user and password

The standard authenticated dump of a domain's users, computers, and groups.

```
ad-ldap-enum example.com/user:Password1@dc.example.com
```

Outcome: writes `Domain_Group_Membership.csv`, `Extended_Domain_User_Information.csv`, and `Extended_Domain_Computer_Information.csv` to the working directory; group membership per group lets you reconstruct who can access what without touching the DC again.

### Enumerate against a DC by address with classic flags

Point the dump at a domain controller you only know by IP.

```
ad-ldap-enum -d example.com -l 198.51.100.10 -u user -p Password1
```

Outcome: same three CSV outputs as the target-string form, using the long-form server and domain options; useful when the DC's FQDN differs from the domain name or only the address is known.

### Enumerate with Kerberos authentication

Use ticket-based auth inside a domain context instead of a password bind.

```
ad-ldap-enum example.com/user:Password1@dc.example.com -k
```

Outcome: authenticates with Kerberos against the FQDN-named DC (required for SPN resolution) and produces the same CSV set; combine with a credential cache where the environment provides one.

### Collect over LDAPS with channel binding

Match environments that enforce encrypted, channel-bound LDAP.

```
ad-ldap-enum example.com/user:Password1@dc.example.com --method ldaps --channel-binding tls-server-end-point
```

Outcome: connects over LDAPS (port 636) with the RFC 5929 `tls-server-end-point` binding, satisfying DCs that reject unbound or plaintext LDAP; output files are unchanged.

### Expand nested group membership

Resolve indirect membership through nested groups.

```
ad-ldap-enum example.com/user:Password1@dc.example.com -e
```

Outcome: the group-membership CSV lists members inherited through nesting, so effective access (not just direct membership) is visible — the difference between seeing a user in one group and seeing everything that group grants transitively.

### Throttle queries for OPSEC

Slow and randomize the LDAP traffic footprint.

```
ad-ldap-enum example.com/user:Password1@dc.example.com --delay 2 --jitter 0.3
```

Outcome: each LDAP query waits about 2 seconds ±30% jitter, trading run time for a traffic profile that looks less like an automated dump; `--page-size` additionally controls result batching per query. Add `-x` to also produce a single Excel workbook alongside the CSVs.

*Corpus evidence: 3 mentions across 1 corpus entry, including full command lines; examples above verified against upstream documentation.*
