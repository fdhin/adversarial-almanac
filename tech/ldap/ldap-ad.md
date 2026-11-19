---
technology: "LDAP — Active Directory surface"
kind: implementation
audience: offensive-security
family: directory-services
layer: "protocol implementation (AD DS database access — every DC)"
language: "n/a (server side is the NTDS database; attack tooling in Python/PowerShell)"
homepage: "https://learn.microsoft.com/en-us/openspecs/windows_protocols/ms-adts/"
github-repo: "https://github.com/CroweCybersecurity/ad-ldap-enum (representative enumerator)"
provenance: "corpus-grounded (48 files / 461 machines pass the AD-LDAP command-context gate: bloodyAD 20, ldapsearch 18, AD-module/PowerView cmdlets 10, windapsearch 6, ad-ldap-enum 1, ldapdomaindump 1) + upstream-verified: windapsearch README (bind behavior, --user-spns/--unconstrained-*/--custom), ad-ldap-enum README (msldap-based CLI, null/username/DN bind, channel-binding options), Microsoft AD schema pages (DS-Replication-Get-Changes Rights-GUID 1131f6aa-9c07-11d1-f79f-00c04fc2dcd2, DS-Replication-Get-Changes-All 1131f6ad-…), OWASP LDAP-injection rules, ADV190023. Companion router: [[ldap|LDAP]]. Writes cross-referenced to [[ad-acl]]; roast targets to [[kerberos]]."
---

# LDAP — Active Directory surface

> One authenticated bind turns a DC into a read-everything map of users, groups, SPNs, ACLs and delegation flags — and the same protocol is how every ACL-abuse write is actually delivered.

## What it is

AD's LDAP is the read/write front end of the NTDS database on every DC: 389/tcp for plain LDAP + STARTTLS, 636 for LDAPS, 3268/3269 for the global catalog (a forest-partial replica that answers cross-domain queries). The field's dominant pattern is trivial to state: **anonymous bind sometimes leaks the rootDSE, then any single domain credential dumps the estate, then bloodyAD-style tools write the privilege escalation**. Three naming contexts matter — the **domain NC** (`DC=example,DC=com`: users/groups/computers), the **configuration NC** (`CN=Configuration,DC=example,DC=com`: sites, AD CS objects, extended-rights GUIDs) and the **schema NC** — and the rootDSE's `namingContexts` attribute lists all of them before you authenticate.

The bind types you'll actually use: **anonymous** (`-x`, no DN/password — probe only), **simple** (`-D user@example.com -w …`, or `DOMAIN\user` UPN/Down-Level forms), and **SASL/GSSAPI** (Kerberos ticket binds — free signing/sealing without touching NTLM; observed: one hardened estate sets `dns_canonicalize_hostname=false` plus ldapsearch's `-N` to stop hostname canonicalization breaking Kerberos service tickets, and runs bloodhound-python with `--ldap-channel-binding` against hardened DCs; another observed estate is AES-only). Against a DC, machine-account binds (`dc01$@example.com`) read the same tree — the field's ADCS/shadow-credential chains all end with one.

## The anatomy that matters on an engagement

    anonymous bind ─► rootDSE: namingContexts / defaultNamingContext   (free base DN)
    credentialed bind (simple | SASL) ─► search the domain NC:
        users          (objectClass=user)      → sAMAccountName, memberOf, userAccountControl
        SPN scan       (servicePrincipalName=*) → kerberoast target list ([[kerberos]])
        delegation     msDS-AllowedToDelegateTo / msDS-AllowedToActOnBehalfOfOtherIdentity
        bit flags      (userAccountControl:1.2.840.113556.1.4.803:=N)
        UAC bits: 0x400000 no-preauth · 0x20000 never-expire · 0x2 disabled
        ACLs          nTSecurityDescriptor read (raw / sdflags=4) → [[ad-acl]]
        config NC     CN=Configuration,… → AD CS templates ([[ad-cs]]), extended-rights GUID map
    modify:  group add (bloodyAD) · SPN write · RBCD attribute write — [[ad-acl]] primitives
    GC 3268: cross-domain object resolution without chasing referrals

Three structural properties drive most findings:

- **Authenticated read is universal.** Every "Domain Users" member can pull the full user/group/computer/SPN/delegation picture (windapsearch's README states it plainly: any valid domain account can query "any AD related information"); nothing about mass enumeration is admin-gated.
- **Anonymous binds are the classic legacy hole.** Observed: estates where anonymous bind is allowed but most queries still need creds, and estates where `-x` with no `-D` walks the whole tree anonymously. Modern DCs default-disable anonymous read; the nmap-era `389/tcp open ldap` line never tells you which estate you're in — the bind test does.
- **The same connection that enumerates escalates.** bloodyAD turns an ACL finding into a group-membership add or attribute write over LDAP; the enum tool and the abuse tool share one bind.

## Enumeration patterns (field-verified)

**1. Bind-or-bust triage.** `ldapsearch -x -b '' -s base namingContexts` → if the rootDSE answers, immediately try anonymous search of the base DN; if that fails, you need creds (observed: one estate rejects anonymous at bind).

**2. windapsearch patterns.** `windapsearch -d example.com --dc-ip 198.51.100.10 -u user@EXAMPLE.COM -p 'Password123!' -U` (users), `-G` (groups), `-C` (computers), `--da` (nested Domain Admins), `--user-spns` (Kerberoast list), `--unconstrained-users`/`--unconstrained-computers` (delegation), `-m GROUP` (members), `--custom '(&…)'` (raw filter). Anonymous mode still returns the domain functionality level — a free fingerprint.

**3. ad-ldap-enum pattern.** `ad-ldap-enum -l 198.51.100.10 -d example.com -u user -p 'Password123!'` — recursive group-membership CSVs (`Domain_Group_Membership.csv`, `Extended_Domain_User_Information.csv`); `-n` for null auth; `--method ldaps` + `--channel-binding none` when the DC enforces defaults that break older clients. Observed: one engagement (finding servers when names are unknown).

**4. Raw ldapsearch against AD**:
```bash
ldapsearch -x -H ldap://198.51.100.10 -D 'user@example.com' -w 'Password123!' \
  -b 'DC=example,DC=com' '(&(objectClass=user)(!(userAccountControl:1.2.840.113556.1.4.803:=2)))' \
  sAMAccountName memberOf servicePrincipalName userAccountControl
```
The `:1.2.840.113556.1.4.803:=` (bit-AND) / `:1.2.840.113556.1.4.804:=` (bit-OR) matching rules are how AD filter-speak expresses flag math; `4194304` = DONT_REQ_PREAUTH (AS-REP roast candidates), `524288` = TRUSTED_FOR_DELEGATION.

**5. SPN scanning via LDAP.** `servicePrincipalName=*` queries replace port scans for service discovery — the attribute tells you which accounts run which services (MSSQL, HTTP, CIFS…) without touching the hosts; it doubles as the Kerberoast target list. Windows-side: `Get-ADUser -Filter {ServicePrincipalName -like "*"}` or `setspn -Q */*` (observed: `setspn -Q */*` checks; windapsearch --user-spns).

**6. ACL read via GUID.** Raw `nTSecurityDescriptor` (read with sdflags=4 for the DACL) is GUID-soup; resolve the `rightsGuid` values against the **extended-rights container** in the config NC (`CN=Extended-Rights,CN=Configuration,DC=example,DC=com`) — e.g. the DCSync pair verified from Microsoft's schema pages: `DS-Replication-Get-Changes` = `1131f6aa-9c07-11d1-f79f-00c04fc2dcd2`, `DS-Replication-Get-Changes-All` = `1131f6ad-9c07-11d1-f79f-00c04fc2dcd2` (see [[ntds]]). This GUID-join is exactly what BloodHound does for you.

**7. BloodHound ingest context.** SharpHound/bloodhound-python collect over this same LDAP surface (users, groups, ACLs with GUID resolution, sessions, delegation attributes) — running a collector is an LDAP read storm from one bind (dozens of observed engagements via the [[ad-acl]] unit). bloodhound-python takes the same `-d/-u/-p/--dc-ip` bind parameters as every tool above, and can pass the hash or a ccache (`--hashes`/`-k`) when that's what you hold.

**8. The AD PowerShell module / PowerView** are LDAP clients too: `Get-ADUser -Filter *`, `Get-ADComputer -Filter *`, `Get-DomainObject`, `Get-DomainGPO` — same binds, same filters, just typed in PowerShell over WinRM.

## Write surface (the abuse side)

- **bloodyAD** — group-member adds (`add groupMember`), UAC bit clears (`set userAccountControl`), SPN/attribute writes, DNS record adds; takes password, NT hash (`-H`), or Kerberos (`-k`) binds. What each write *requires* is the [[ad-acl]] unit's whole subject.
- **Shadow-credential writes** (`msDS-KeyCredentialLink`) start as an LDAP attribute write (observed: via certipy) → [[kerberos]].
- **GPO reads/abuse** start from `gpcFileSysPath` in LDAP → [[gpo]].

## What the DC's answers mean

1. **rootDSE answering anonymous = the estate pre-2012 or loosened;** error = modern defaults; either way you learned it for free.
2. **`1.2.840.113556.1.4.803:=4194304` hits = AS-REP roast list** ([[kerberos]]); `TRUSTED_FOR_DELEGATION`/`msDS-AllowedToDelegateTo` hits = delegation chains ([[kerberos]]).
3. **`msDS-SupportedEncryptionTypes` on an account** predicts its roast etype (RC4 vs AES) before you request a ticket.
4. **The config NC answering AD CS objects** (`CN=Public Key Services,CN=Services,…`) means certificate-template enumeration is one search away → [[ad-cs]] (certipy `find` is exactly this search).
5. **LDAP bind errors are account-state oracles**: `NT_STATUS_ACCOUNT_DISABLED` / `…LOCKED_OUT` / `…PASSWORD_MUST_CHANGE` each name a distinct hygiene problem to exploit.

## Where to go next

- [[ldap|LDAP router]] — bind ladder mechanics, injection basics, the OpenLDAP thin surface.
- [[ad-acl]] — the rights behind every write listed here.
- [[kerberos]] / [[ntds]] / [[ad-cs]] — the attacks whose target lists LDAP hands you.
