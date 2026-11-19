---
name: "python2-ldapdomaindump"
categories:
  - blackarch-scanner
version: "0.9.4"
homepage: "https://pypi.org/project/ldapdomaindump/#files"
packaged-by: [blackarch, arch, nixpkgs]
blackarch-builddate: "2025-09-22"
arch-builddate: "2025-12-20"
build-system: python
license: MIT
meta-checked: "2026-11-09"
github-repo: "dirkjanm/ldapdomaindump"
worth: 4.5
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-stars: 1436
github-pushed: "2025-04-06"
github-archived: false
github-issues: 28
github-match: "exact"
gh-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# python2-ldapdomaindump

> ldapdomaindump — dumps Active Directory users, groups, computers, trusts and policies over LDAP into HTML, JSON and greppable files.

## What it's for

Any authenticated domain user can read a surprising amount of the directory over LDAP; ldapdomaindump (dirkjanm) collects it and makes it readable. It writes per-category files — `domain_users`, `domain_groups`, `domain_computers`, `domain_policy`, `domain_trusts` — plus grouped views (users by group, computers by OS), each in HTML, JSON and tab-delimited grep formats. It authenticates with a password or an NTLM hash, supports `ldap://` and `ldaps://` connection strings, and is a standard first-recon step inside a domain: the HTML files are easy to browse and the grep/JSON files are easy to search. This note covers the Python 2 flavored 0.9.4 package (the PyPI sdist the distros package); the current upstream series is Python 3.

## Typical use cases

### Dump the domain with credentials

The standard authenticated recon dump against a domain controller.

```
ldapdomaindump -u 'CORP\user' -p 'Password1' 198.51.100.10
```

Outcome: connects to the DC over LDAP and writes the `domain_*` file set (each `.html`, `.json` and `.grep`) into the current directory — open `domain_users.html` to browse users, or grep the `.grep` files for names and attributes.

### Authenticate with a hash instead of a password

Pass-the-hash recon without cleartext credentials.

```
ldapdomaindump -u 'CORP\user' -p 'aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0' 198.51.100.10
```

Outcome: the `-p` value is interpreted as an LM:NTLM pair (the example uses the well-known empty-hash placeholder, not a real credential) and NTLM authentication proceeds exactly as with a password, producing the same file set.

### Dump over LDAPS and resolve computer names

Use the encrypted listener and enrich computer records.

```
ldapdomaindump -u 'CORP\user' -p 'Password1' -r -n 198.51.100.10 ldaps://dc1.corp.example.com
```

Outcome: connects via the `ldaps://` connection-string form, and `-r` resolves each computer's `DNSHostName` using the resolver at `-n` (a DC IP), folding addresses into the output — note that `-r` can be slow and generate significant DNS load on large domains.

### Keep the dump small on big domains

Trim queried attributes and choose the output directory.

```
ldapdomaindump -u 'CORP\user' -p 'Password1' -o dump/ --minimal 198.51.100.10
```

Outcome: only the minimal attribute set is queried (the fields shown in the HTML/grep output), cutting memory and network use, and all files are written into `dump/` instead of the current directory.

*Grounded in corpus usage of the `ldapdomaindump` binary (an authenticated dump against a domain controller, then reading `domain_users.html` for user triage) plus the upstream README at dirkjanm/ldapdomaindump; flags verified against its argparse help.*
