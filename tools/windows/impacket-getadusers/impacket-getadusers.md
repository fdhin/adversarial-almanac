---
name: "impacket-getadusers"
categories:
  - corpus-windows
homepage: "https://github.com/fortra/impacket"
origin: "corpus"
github-repo: "fortra/impacket"
packaged-by: []
meta-checked: "2026-11-09"
github-commits-year: 169
gh-activity-checked: "2026-11-09"
worth: 6.5
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
---

# impacket-getadusers

> Lightweight LDAP dump of AD user accounts — names, mail, pwdLastSet and lastLogon — from any valid credentials.

## What it's for

impacket-getadusers enumerates user objects in a domain over LDAP and prints a compact table with sAMAccountName, mail, pwdLastSet and lastLogon. By default it lists only users that have a mail attribute; `-all` returns every user, which makes it a cheap first-step username enumerator when you hold any valid domain credentials (password, NTLM hash or Kerberos ticket). It is purely read-only enumeration — no SPN/kerberoast data, no group or ACL information; reach for bigger collection tools once you know the user list.

## Typical use cases

### Enumerate all domain users

```
impacket-getadusers example.com/jdoe:Password1 -all -dc-ip 198.51.100.10
```

Outcome: prints a table of every user in the domain (including those without a mail attribute) with columns for sAMAccountName, mail, pwdLastSet and lastLogon — a ready-made username list for spraying or targeting, and stale/never-logged-on accounts stand out immediately.

### Enumerate users with an NTLM hash (no plaintext)

```
impacket-getadusers example.com/jdoe -hashes :5f4dcc3b5aa765d61d8327deb882cf99 -dc-ip 198.51.100.10
```

Outcome: the same enumeration using pass-the-hash authentication — confirms the hash is valid for the domain while collecting the user list, useful when only a hash was captured.

*Commands verified against the current impacket source (`examples/GetADUsers.py`).*
