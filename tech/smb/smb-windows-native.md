---
technology: "SMB — Windows-native implementation"
kind: implementation
audience: offensive-security
family: directory-services
layer: "protocol implementation (os component: Server service / LanmanServer, srv2.sys)"
language: "C (kernel driver + service)"
homepage: "https://learn.microsoft.com/en-us/openspecs/windows_protocols/ms-smb2/"
provenance: "evidence-grounded (52 files pass the Windows-native gate: ms17_010/eternalblue strings, SMBv1 markers, NT LM 0.12, message_signing lines, crackmapexec/netexec probe lines with Windows builds, nmap microsoft-ds Windows banners) + upstream-verified: Microsoft Security Bulletin MS17-010 (Critical, 2017-03-14, KB4013389 — RCE via crafted messages to an SMBv1 server). EternalBlue context is upstream-history, evidence-silent (zero ms17_010/eternalblue markers in the evidence base) and labeled as such. Companion router: [[smb|SMB]]."
source: "six2dez/pentest-book (enumeration/ports.md, no license — rewritten with attribution), accessed 2026-11-13"
---

# SMB — Windows-native

> The Windows SMB stack has no config file — its misconfigurations are policy: guest enabled on a 2022 DC, SMBv1 left answering on a 2008 box, signing optional on members. The probe line hands you all three before you send a credential.

## What it is

Windows runs SMB in the Server service (LanmanServer) with SMB2+ handled by the `srv2.sys` kernel driver, listening on 445 (and 139 on legacy hosts). There is nothing to read and nothing to edit — the attack surface is the **default posture per build plus the estate's policy drift**, which is why the fingerprint is the whole game. Two probe outputs dominate the evidence base: nmap's `445/tcp open microsoft-ds Windows Server 2016 Standard 14393 microsoft-ds (workgroup: …)` banner line, and the crackmapexec/netexec SMB module banner `Windows Server 2022 Build 20348 x64 (name:DC01) (domain:example.com) (signing:True) (SMBv1:False)` — sometimes extended with `(Null Auth:True)` and `(NTLM:False)` fields. Read them as a target model: build era → exploit class; signing → relay viability; null/guest → pre-auth reach.

The Windows-native patterns split cleanly: **guest access on modern builds** (Windows Server 2022 DCs accepting `guest` with an empty password; guest-readable shares elsewhere), **null-session fields** (`(Null Auth:True)`), **SMBv1-era hosts** (Server 2008 R2 SP1 with `(SMBv1:True)`; 2008 R2, 2012 R2, and "Windows 7 - 10" banners), and **signing gaps on members** (`signing:False` — the relay opening; DCs observed are uniformly `signing:True`).

## The anatomy that matters on an engagement

    probe line: (name:) (domain:) (signing:X) (SMBv1:Y) (Null Auth:Z) (NTLM:…)
      signing:False → NTLM relay target (ntlm unit)
      SMBv1:True    → 2008/2012-era estate → MS17-010 question
      Null Auth:True / guest -p '' → pre-auth share + pipe enumeration
    session ladder: anonymous → guest → cred spray → Kerberos (over SMB with -k)
    IPC$ pipes → SAMR/LSAT/SRVS (user/group/share enum — rpc unit)
    admin on SMB → psexec/smbexec-class exec, secretsdump-class dumps (rpc/ntds units)

Three structural properties drive most findings:

- **Policy, not config, is the bug.** "Everyone/guest includes anonymous" and blank-password guest on a DC are estate decisions the probe line surfaces immediately (observed: four 2022-era DCs accepting guest).
- **The build string dates the exploit classes.** 2008 R2 with SMBv1 enabled is the MS17-010 generation; the evidence base never fires MS17-010 (patched estates), but the dialect marker still maps the era for every other legacy behavior.
- **Signing is enforced where it matters most.** DCs default `signing:True` (observed throughout); members with `signing:False` are the relay fish — check before you spray.

## Guest and null-session behavior

- **Guest with empty password**: `nxc smb 198.51.100.10 -u 'guest' -p '' --shares` returning `[+] EXAMPLE\guest:` means guest auth is live and shares/permissions follow — one observed chain walks HR data this way on a 2022 DC; another gets READ/WRITE on `homes` as guest.
- **Anonymous listing** (`(Null Auth:True)`): share enumeration without any credential (observed repeatedly) — combine with `--rid-brute` for user enumeration through the pipes.
- **Windows null sessions are restricted by default since XP/2003** (RestrictAnonymous et al.), so `smbclient -N -L` succeeding is itself a finding; where it fails but guest works, guest *is* the null session's successor.

## SMBv1 and the MS17-010 / EternalBlue context (upstream-verified, absent from the evidence base)

Microsoft Security Bulletin **MS17-010 — Critical**, published 2017-03-14 (KB4013389): remote code execution when an attacker sends **specially crafted messages to a Microsoft SMB 1.0 server**; the update "corrects how SMBv1 handles specially crafted requests". The associated leaked-NSA exploit (**EternalBlue**, later the WannaCry/NotPetya carrier) targets SMBv1 pool corruption and remains the canonical unauthenticated-Windows-RCE story. Operational framing, honestly labeled: the evidence base contains **zero** MS17-010/EternalBlue exploitation — patched targets — so on modern engagements treat `SMBv1:True` as an era marker and a hygiene finding, not an expected win; the RCE applies to unpatched hosts only.

## MS08-067 — the SMBv1-era ancestor (ports cheat-sheet addition, 2026-11-13)

Rewritten from six2dez/pentest-book enumeration/ports.md (no license — rewritten with attribution); CVE-2008-4250 verified against the NVD record, the `smb-vuln-ms08-067` NSE against the nmap repo. October 2008, bulletin MS08-067: the **Server service mishandles path canonicalization** in RPC-over-SMB — a crafted request executes code remotely on Windows 2000 SP4, XP SP2/SP3, Server 2003 SP1/SP2, Vista Gold/SP1, Server 2008 and 7 pre-beta, exploited in the wild by Gimmiv.A in its release month. Same operational framing as MS17-010 above: era history for the report's legacy section, checked today with one NSE line, real only on an unpatched-2008-era estate.

## Signing

- nmap's `smb2-security-mode` script prints `message_signing: required` (observed) or the historic `disabled (dangerous)` form; netexec prints `(signing:True/False)`.
- **Consequence map**: signing required → relay and tamper dead on this host; signing disabled (observed) → the host is a first-class NTLM-relay target — coordinate with the ntlm unit's relay workflow before wasting spray attempts.

## Workflow

```bash
# target model in one line
nxc smb 198.51.100.10 -u 'guest' -p '' --shares
#  → Windows Server 2022 Build 20348 (name:DC01) (domain:EXAMPLE.COM)
#    (signing:True) (SMBv1:False) (Null Auth:True)

nmap -p445 --script smb2-security-mode,smb-os-discovery 198.51.100.10

# guest/null triage then credentiated sweep
smbclient -L //198.51.100.10 -N
smbclient //198.51.100.10/share -U 'guest%'
nxc smb 198.51.100.10 -u users.txt -p 'Spring2026!' --continue-on-success --rid-brute 66000
```

Expected outputs: the probe line fields as above; `--shares` lists `Share Permissions Remark` with READ/WRITE columns; `[+] EXAMPLE\guest:` confirms guest auth; `--rid-brute` yields user names via SID cycling (the pipe mechanics live in the rpc unit).

## Where to go next

Back to the [[smb|SMB router]]; the Samba counterpart is [[smb-samba|Samba SMB]]. Relay consequences chain into the ntlm unit; pipe-level enumeration into the rpc unit.
