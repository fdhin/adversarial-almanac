---
name: "modifycerttemplate"
categories:
  - blackarch-windows
version: "7.4c35708"
homepage: "https://github.com/fortalice/modifyCertTemplate"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-13"
build-system: windows
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "fortalice/modifyCertTemplate"
github-stars: 145
github-pushed: "2023-06-26"
github-archived: false
github-issues: 1
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.0
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# modifycerttemplate

> Python utility to inspect and modify Active Directory certificate templates — built to turn WriteProperty rights over a template (ESC4) into privilege escalation.

## What it's for

modifyCertTemplate (Fortalice) aids operators in modifying ADCS certificate templates so that a created vulnerable state can be leveraged for privilege escalation, and in resetting the template to its previous state afterwards. Its sweet spot is the scenario where `WriteProperty` rights over a template have been compromised but the operator does not know which properties the right applies to: the tool can dump the template's ACL and cross-reference the ACE property GUIDs to determine exactly what is modifiable.

It speaks LDAP(S) directly (target argument is `[[domain/]username[:password]`, with NTLM hash, Kerberos and AES-key authentication options like other impacket-style tools) and is the Python sibling of the C# StandIn/PyWhisker ecosystem for ADCS abuse. It edits template objects only — pair it with a cert requesting tool to actually exploit the weakened template.

*No corpus evidence — derived from upstream documentation.*

## Typical use cases

### Dump a template's attributes

```
python3 modifyCertTemplate.py -template KerberosAuthentication example.com/user:Password1
```

Outcome: prints all attributes of the named certificate template from LDAP (add `-raw` for the raw values), giving the baseline to record before any modification and to restore afterwards.

### Check what you are allowed to modify

```
python3 modifyCertTemplate.py -template User -get-acl -dc-ip 198.51.100.10 example.com/user:Password1
```

Outcome: prints the template object's ACEs; cross-referencing the WriteProperty GUIDs in them against the template's writable properties tells you exactly which escalation-relevant attributes your compromised right can touch. Any other object's ACL can be checked the same way by passing its `-dn`.

### Make a template issue attacker-controlled subjects

```
python3 modifyCertTemplate.py -template User -add enrollee_supplies_subject -property msPKI-Certificate-Name-Flag example.com/user:Password1
```

Outcome: adds the `ENROLLEE_SUPPLIES_SUBJECT` flag to the template's name-flag property while preserving the existing flags; certificate requests against the template can now specify an arbitrary SAN (for example a DA), turning the write right into impersonation. Restore the original value with `-value` afterwards.

### Widen what the template can be used for

```
python3 modifyCertTemplate.py -template User -add "client authentication" -property pKIExtendedKeyUsage example.com/user:Password1
```

Outcome: appends the client-authentication EKU to the template's `pKIExtendedKeyUsage` list (again preserving existing entries), so certificates issued from it authenticate as clients; `-value` with an explicit OID list sets the whole attribute instead of appending.
