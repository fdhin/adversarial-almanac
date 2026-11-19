---
name: "gmsadumper"
categories:
  - blackarch-misc
version: "18.e03187c"
homepage: "https://github.com/micahvandeusen/gMSADumper"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-12"
build-system: scripts
license: GPL3
meta-checked: "2026-11-09"
github-repo: "micahvandeusen/gMSADumper"
github-stars: 389
github-pushed: "2024-02-12"
github-archived: false
github-issues: 5
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.3
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# gmsadumper

> Read and parse every gMSA password blob a domain principal is allowed to access.

## What it's for

Group Managed Service Accounts (gMSAs) store their service-account password in Active Directory as a secret blob that only principals listed in the account's msDS-GroupMSAMembership attribute may read. gMSADumper authenticates to a domain controller over LDAP, enumerates those blobs, and parses out each gMSA's password (returned as an NT hash usable directly for pass-the-hash). It is a post-exploitation step for exactly one situation: you hold credentials for a principal (user, group member, or machine account) that can read one or more gMSA passwords and want to turn that into the service account's hash. It does not attack LDAP authentication itself — you must already have usable credentials, a ticket, or an NT hash.

## Typical use cases

### Dump gMSA passwords with user credentials

Authenticate with a username and password and list every gMSA password the account may read, targeting a domain controller by IP.

```
gmsadumper -u user -p Password1 -d corp.example.com -l 198.51.100.10
```

Outcome: prints each readable gMSA account name with its password as an NT hash, ready for pass-the-hash against services running as that account.

### Read a machine account's own gMSA password

A compromised host's machine account (trailing $) can dump the gMSA passwords that host is authorized to use — useful when the machine runs services under a gMSA.

```
gmsadumper -u 'web01$' -p 'Password1' -d corp.example.com
```

Outcome: prints the gMSA account names and NT password hashes readable by that computer account, typically the gMSA used by services on that host.

### Dump with Kerberos authentication

Use an existing Kerberos ticket environment (for example after ticket acquisition on a domain-joined host) instead of plaintext credentials.

```
gmsadumper -k -d corp.example.com -l dc01.corp.example.com
```

Outcome: authenticates via Kerberos and prints the gMSA names and NT hashes the ticket's principal may read.

### Pass-the-hash into the dump

Authenticate with a captured NT hash (LM:NT form) rather than a password.

```
gmsadumper -u user -p e52cac67419a9a224a3b108f3fa6cb6d:8846f7eaee8fb117ad06bdd830b7586c -d corp.example.com -l dc01.corp.example.com
```

Outcome: prints the gMSA names and NT password hashes accessible to that account, chaining one captured hash into service-account hashes.
