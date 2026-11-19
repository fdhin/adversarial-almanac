---
technology: "NTDS.dit / DC secrets"
kind: overview
audience: offensive-security
family: directory-services
layer: "credential store (AD database + replication interface)"
language: "n/a (ESE database + DRSUAPI; attack tooling Python/C)"
homepage: "https://learn.microsoft.com/en-us/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview"
github-repo: "https://github.com/fortra/impacket (secretsdump.py / DRSUAPI client)"
provenance: "corpus-grounded (re-gated command-context gate — 40 files / 461 machines: secretsdump 33, dcsync/DRSUAPI 24, DIT-theft via vssadmin/diskshadow/ntdsutil/IFM 8, credential-cache (cachedump/DCC2) 5; of the secretsdump files, 4 used it on LOCAL SAM hives only — that form belongs to [[windows-secrets]]; structure-pass hint was 9, a ~4x undercount — the marker set missed DRSUAPI-style dcsync lines and prose-form secretsdump usage) + upstream-verified: impacket secretsdump.py source (header documents both NTDS paths — DRSGetNCChanges replication vs vssadmin extraction; SAM/LSA include cached creds; flags -just-dc/-just-dc-user/-use-vss/-use-keylist/LOCAL mode), Microsoft AD schema (DS-Replication-Get-Changes 1131f6aa-9c07-11d1-f79f-00c04fc2dcd2 'Replicating Directory Changes'; DS-Replication-Get-Changes-All 1131f6ad-9c07-11d1-f79f-00c04fc2dcd2 'allows the replication of secret domain data'), learn.microsoft ntdsutil IFM page (Server 2012 IFM options; ADDSDeployment -installationmediapath/-syskey), hashcat example table (2100 DCC2 MS Cache 2, 1000 NTLM). Companion methodology: [[ntds-bug-bounty|NTDS bug bounty methodology]]."
---

# NTDS.dit / DC secrets

> The domain's credential ledger: one database holding every account's hash, one replication API that hands it to anyone with the right extended right — two independent roads to "every credential in the forest."

## What it is

`NTDS.dit` is the AD database (an ESE/Jet store under `C:\Windows\NTDS`), holding — among everything else — the NT hashes and Kerberos keys of every security principal, encrypted at rest with keys derived from the **SYSKEY/bootkey** (which itself lives in the DC's SYSTEM registry hive). Attacker-facing, the unit is three surfaces: **file theft** (copy the DIT + SYSTEM hive, decrypt offline), **DCSync** (ask a DC to *replicate* the secrets to you over the DRSUAPI `DRSGetNCChanges` call — the impacket secretsdump source literally documents this as its first method), and the **credential-cache chain** (domain cached credentials / DCC2 in local hives — the `LSA Secrets (including cached creds)` secretsdump handles). Plus **ntdsutil's IFM** ("install from media") mode, which builds a DIT+hives archive with a built-in tool.

The unit's center of gravity is rights, not exploits. DCSync needs two extended rights on the domain NC — `DS-Replication-Get-Changes` and `DS-Replication-Get-Changes-All` (Microsoft's schema page for the latter says it "allows the replication of secret domain data") — granted to Domain Admins/Enterprise Admins *and*, critically, to whatever your ACL findings gave you ([[ad-acl]]). File theft needs admin-on-DC for a consistent copy (the file is locked, hence VSS snapshots). Either road ends in the same place: the `krbtgt` hash (golden ticket, [[kerberos]]) or the Administrator hash (pass-the-hash, [[ntlm]]).

40 observed machines route through this surface — it is the standard end-game of AD chains (observed: `-just-dc-user` single-account dumps, diskshadow scripts, stolen-backup DITs, IFM archives).

## The anatomy that matters on an engagement

    ┌─ road 1: FILE THEFT (admin/SYSTEM on a DC, or a backup containing the files)
    │    locked file NTDS.dit  → copy via shadow copy:
    │      vssadmin create shadow /for=C:           → copy from \\?\GLOBALROOT\Device\HarddiskVolumeShadowCopyN
    │      diskshadow> set context persistent › script + robocopy
    │      ntdsutil: ac i ntds → ifm → create full C:\temp\ifm   (builds DIT+hives archive)
    │    also need: SYSTEM hive (bootkey)  [+ SECURITY for LSA secrets/trust keys]
    │    offline: secretsdump.py -system SYSTEM -ntds ntds.dit -security SECURITY LOCAL
    │    backups path: stolen backup archives carry ntds.dit+SYSTEM+SECURITY as-is
    │
    ├─ road 2: DCSYNC (replication rights, no code on the DC)
    │    rights: DS-Replication-Get-Changes (1131f6aa-…) + Get-Changes-All (1131f6ad-…)
    │    tools: secretsdump -just-dc[-user X] · mimikatz lsadump::dcsync /user:EXAMPLE\krbtgt
    │    works from any host that can reach the DC's DRUAPI over RPC ([[rpc]])
    │
    ├─ SYSKEY: bootkey in SYSTEM hive → PEK chain decrypting DIT secrets
    │    (IFM/ADDSDeployment still names it: -installationmediapath + -syskey)
    │
    └─ credential-cache chain (member hosts):
         NL$KM/Cached logons in SECURITY hive → $DCC2$ hashes → hashcat 2100
         (slow: salted per-username, PBKDF2-class iterations — crack selectively)

Three structural properties drive most findings:

- **The DIT is only as protected as its weakest copy.** Backups, IFM sets, and "Temporary" shadow copies of a DC hold the identical secrets with none of the DC's runtime protections (observed: two chains got the DIT from a backup folder without ever compromising a DC).
- **Replication is an authenticated API, not an admin console.** Any principal holding the two replication extended rights — including accounts that got them via ACL writes, group adds, or gMSA/OU control — can request secret-bearing "replication" of arbitrary accounts.
- **One hash beats every password.** The DIT's NT hashes authenticate directly (pass-the-hash, [[ntlm]]); the krbtgt hash *forges* authentication (golden ticket, [[kerberos]]). Neither needs a plaintext password ever to be cracked.

## Where you'll meet it

- **After any privilege escalation toward a DC** — the standard finishing move (14 exemplar engagements…).
- **With replication rights but no DC access** — ACL/OU/group compromise that grants Get-Changes(-All) makes DCSync the whole attack (observed: a compromised service account; a delegated path).
- **Backup infrastructure** — file-level DIT theft from stored backups.
- **Member-host footholds** — the DCC2 cache turns a stolen SECURITY hive into (slow) domain-credential cracks.

## Reading the DC's mind

1. **`secretsdump -just-dc-user Administrator` succeeding means the bound principal holds the replication extended rights** — the DC checked ACLs, not group membership; your audit target is the effective ACL ([[ad-acl]]).
2. **`[*] Using the DRSUAPI method to get NTDS.DIT secrets`** is secretsdump choosing replication over file theft — no touch on the DIT file, no VSS, nothing on disk on the DC.
3. **`DRSUAPI`-style output with `-use-vss` absent and a Kerberos ticket in play (`KRB5CCNAME=… secretsdump -k -just-dc`)** is DCSync riding a Kerberos session — NTLM blocking does not stop it.
4. **An IFM directory (`ntdsutil … ifm → create full`) appearing in an engagement is a self-service exfil primitive** — the tool Microsoft ships to move DCs around is the one that packages DIT+hives for you.
5. **`$DCC2$1024#username#hash` lines mean you hold a *cached-verification* hash, not a login hash** — it only cracks offline (hashcat 2100); it cannot be relayed or passed.

## Where to go next

[[ntds-bug-bounty|NTDS bug bounty methodology]] — snapshot matrix (vssadmin/diskshadow/ntdsutil), offline secretsdump workflow, DCSync rights audit with verified GUIDs, SYSKEY/PEK context, credential-cache cracking, and the krbtgt endgame.
