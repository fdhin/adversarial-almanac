---
name: "impacket-addcomputer"
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

# impacket-addcomputer

> Add or remove a domain computer account — MachineAccountQuota abuse in one command.

## What it's for

addcomputer.py creates (and with `-delete` removes) a machine account in Active Directory. Any authenticated domain user is normally allowed to create up to 10 computer accounts (MachineAccountQuota), so this one command turns ordinary credentials into a fresh controlled principal with a known password. That principal is the raw material for resource-based constrained delegation (paired with ntlmrelayx `--delegate-access`), shadow credentials, and relaying coerced authentication. Creation goes over SAMR on SMB by default; `-method LDAPS` creates the account over LDAP-over-TLS instead, which helps where SMB/SAMR is filtered and allows placing the account outside the default CN=Computers container with `-computer-group`. A trailing `$` on `-computer-name` is optional — it is appended automatically.

## Typical use cases

### Create a machine account with a known password

Standard SAMR creation using your own domain credentials.

```
impacket-addcomputer example.com/user:Password1 -dc-ip 198.51.100.10 -computer-name ATTACK -computer-pass 'Password1!'
```

Outcome: adds the computer account (sAMAccountName becomes `ATTACK$`); `example.com/ATTACK$:Password1!` now authenticates as a machine principal — hand it to ntlmrelayx `--delegate-access` as the delegating account, or use it for S4U abuse.

### Create over LDAPS into a specific container

When SAMR over SMB is blocked, or the account must land in a chosen OU/group.

```
impacket-addcomputer example.com/user:Password1@dc01.example.com -method LDAPS -computer-name PWN_PC -computer-pass 'Password1!' -computer-group 'OU=Servers,DC=example,DC=com'
```

Outcome: creates PWN_PC over LDAPS (TCP 636) with the account added to the given group/container instead of CN=Computers — the route that still works against DCs that block anonymous or hardened SAMR, and the one that lets you choose where the object lives.
