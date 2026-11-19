---
name: "mimikatz"
categories:
  - blackarch-windows
version: "2.2.0_20220919"
homepage: "https://github.com/gentilkiwi/mimikatz"
packaged-by: [blackarch, kali, nixpkgs]
blackarch-builddate: "2024-03-16"
build-system: windows
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "gentilkiwi/mimikatz"
github-stars: 21958
github-pushed: "2026-10-30"
github-archived: false
github-issues: 199
github-match: "exact"
gh-checked: "2026-11-07"
worth: 7.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 13
gh-activity-checked: "2026-11-09"
---

# mimikatz

> The reference Windows post-exploitation console: pulls passwords, NTLM hashes, Kerberos keys and tickets, DPAPI blobs and certificate private keys out of LSASS memory, the registry, and Active Directory replication.

## What it's for

mimikatz (Benjamin Delpy, gentilkiwi) reads the secrets Windows keeps for its own use. It is an interactive console addressed with `module::command /argument:value` statements, one module per secret store: LSASS memory (`sekurlsa`), the SAM database and LSA secrets (`lsadump`), Kerberos tickets (`kerberos`), DPAPI-protected blobs (`dpapi`), certificate stores (`crypto`), Windows Vault / Credential Manager (`vault`), and domain-controller replication over DRSR (`lsadump::dcsync`). It also forges and injects credentials — pass-the-hash, pass-the-ticket, golden tickets, the skeleton key — so it is as much a lateral-movement primitive as a dumper. It does not gain initial access and does not touch the network on its own: you run it on a host you already control, most commands need an elevated process, and EDR products watch its signatures closely. It is the standard tool for demonstrating what an Administrator on one Windows host can do to an entire domain.

## Typical use cases

### Enable debug privilege and dump logon passwords from LSASS

The canonical first move of a mimikatz session: acquire SeDebugPrivilege, then read every credential the Local Security Authority still holds.

```
mimikatz # privilege::debug
mimikatz # sekurlsa::logonpasswords
```

Outcome: `privilege::debug` prints `Privilege '20' OK` (the error `RtlAdjustPrivilege (20) c0000061` means the console is not running elevated — relaunch as administrator); `sekurlsa::logonpasswords` then prints one block per logon session (user, domain, SID) with per-provider secrets: msv NTLM/SHA1 hashes, and wdigest/kerberos/tspkg/ssp/credman plaintext passwords where the provider kept them. On Windows 8.1+ wdigest plaintext is absent unless `UseLogonCredential` is 1, but hashes and Kerberos keys still come out. Two honest footnotes from upstream: `standard::coffee` (typable as bare `coffee`) is an easter egg that only prints an ASCII coffee cup — a liveness check, not a privilege operation — and the same instructions can be run one-shot from the shell as `mimikatz.exe "privilege::debug" "sekurlsa::logonpasswords" exit`. Without debug rights the dump fails with `ERROR kuhl_m_sekurlsa_acquireLSA ; Handle on memory (0x00000005)`; a minidump of LSASS can be processed offline instead with `sekurlsa::minidump lsass.dmp`.

### Dump the local SAM database

Reads local account hashes either live on the host or offline from saved registry hives.

```
mimikatz # lsadump::sam /sam:SamBkup.hiv /system:SystemBkup.hiv
```

Outcome: prints the `SysKey`/`SAMKey` and one `RID / User / LM / NTLM` block per local account (RID 500 = the local administrator), ready for pass-the-hash against the host. Online, the same command without arguments works only with SYSTEM rights — `token::elevate` first, or run from a `psexec -s` shell; otherwise expect access denied (`0x00000005`). The hive files come from `reg save HKLM\SAM SamBkup.hiv` and `reg save HKLM\SYSTEM SystemBkup.hiv` executed on the target, which is what makes the offline form usable from any other machine.

### DCSync a single account from a domain controller

Makes a DC replicate one directory object over DRSUAPI (the protocol DCs use between themselves) and prints its secrets.

```
mimikatz # lsadump::dcsync /user:krbtgt /domain:corp.example.com /dc:dc01.corp.example.com
```

Outcome: prints the account's SAM name, RID and SID, then its `NTLM` and `LM` hashes and Kerberos keys (`aes256_hmac`, `rc4_hmac_nt`, ...) — for `krbtgt` that is the raw material of a golden ticket; for `administrator` it is a direct pass-the-hash credential. `/domain` and `/dc` are optional (current domain, auto-detected DC by default); `/user:krbtgt@corp.example.com` also works, `/all` dumps every object, `/csv` emits machine-parsable lines. Requires an account with replication rights (Domain Admin or a DC machine account).

### Inject a Kerberos ticket into the session (pass-the-ticket)

Loads an existing `.kirbi` ticket into the current logon session instead of authenticating with a password.

```
mimikatz # kerberos::ptt administrator@krbtgt-corp.example.com.kirbi
```

Outcome: `Ticket '...' successfully submitted for current session` — subsequent processes (e.g. `dir \\dc01\c$`) authenticate under the ticket's identity with no credentials involved. Multiple filenames or a directory (all `.kirbi` inside) are accepted; tickets from other tools must be in KRB_CRED format (RFC 4120 §5.8), which is what kekeo/Rubeus exports are.

### Harvest Kerberos session keys from LSASS

`sekurlsa::ekeys` lists the raw Kerberos encryption keys cached for each logon session — the input for pass-the-key and overpass-the-hash.

```
mimikatz # privilege::debug
mimikatz # sekurlsa::ekeys
```

Outcome: per logon session, a `Key List` of `aes256_hmac` / `aes128_hmac` / `rc4_hmac_nt` / `rc4_hmac_old` values. The AES keys let you request tickets directly (pass-the-key / overpass-the-hash, e.g. via kekeo or Rubeus) without ever touching NTLM; `rc4_hmac_nt` is simply the account's NTLM hash. Same elevation and minidump support as `sekurlsa::logonpasswords`.

### Export certificates with their private keys

Walks a certificate store and writes the public and private parts of each certificate to disk.

```
mimikatz # crypto::certificates /systemstore:local_machine /store:my /export
```

Outcome: lists each certificate (subject, provider, CNG/CAPI type, size, exportability) and, for exportable entries, writes a `.der` (public) and a `.pfx` (private key + cert, password `mimikatz`) pair per certificate into the current directory. Keys flagged non-exportable can often still be exported after `crypto::capi` and `crypto::cng` patch the crypto providers (both need debug privilege). The PFX re-enters elsewhere with `certutil -user -p mimikatz -importpfx cert.pfx` — the standard way to carry certificate-based access (EFS files, AD CS abuse) to another host.

### Read Windows Credential Manager entries

Enumerates saved credentials — generic ones in the clear, domain-type ones after an LSASS patch.

```
mimikatz # vault::cred
```

Outcome: one block per entry — `TargetName`, `UserName`, `Type` (1 = generic, 2 = domain_password), `Persist`, and the `Credential` secret. Generic entries show their secret immediately; domain-password entries show an empty secret because Windows only lets authentication packages read that blob type — `vault::cred /patch` patches LSASS to lift the restriction (intrusive: it modifies lsass.exe in memory, so prefer the DPAPI route when possible). Pair with `vault::list` for Windows Vault / web credentials.

### Impersonate SYSTEM

Borrows a SYSTEM token for commands that need more than the Administrators group.

```
mimikatz # privilege::debug
mimikatz # token::elevate
```

Outcome: mimikatz attaches a SYSTEM impersonation token to its working thread (`User name : AUTORITE NT\Système ... -> Impersonated !`); following commands such as `lsadump::sam`, `lsadump::secrets` or `lsadump::trust` run with full SYSTEM rights without an external `psexec -s`. `token::whoami` shows process vs thread tokens, `token::revert` drops back, and debug privilege is required to grab the token in the first place.

### Skeleton-key a domain controller

Patches a DC's Kerberos validation in memory so one extra fixed password authenticates as any account.

```
mimikatz # privilege::debug
mimikatz # misc::skeleton
```

Outcome: mimikatz opens the DC's lsass.exe, locates the `kdcsvc.dll` crypto table and swaps the KDC key handling (`[KDC] keys patch OK`); afterwards every account in the domain still accepts its real password AND authenticates with the literal password `mimikatz`. The patch is memory-only — a DC reboot reverts it — and it must be run on the DC itself, elevated, as the classic demonstration of domain-wide credential injection. `/letaes` restricts the patch to the RC4 path on old systems.

### Decrypt a DPAPI credential blob

Turns a raw `%APPDATA%\Microsoft\Credentials\*` blob into plaintext once its masterkey is in mimikatz's cache.

```
mimikatz # dpapi::cred /in:"%appdata%\Microsoft\Credentials\0f8e5d8f9b7e4c3d2a1b0c9d8e7f6a5b"
```

Outcome: parses the DPAPI blob (version, masterkey GUID, algorithm ids) and appends `Decrypted Credential:` with `UserName:` and `Password:` in clear text, provided the matching masterkey is cached (`dpapi::cache` lists cache state). Masterkeys are unlocked separately with `dpapi::masterkey /in:"%appdata%\Microsoft\Protect\<SID>\<guid>" /sid:S-1-5-21-1004-2005-3006-1001 /password:Password1` (or `/hash:`, or `/rpc` which asks a domain controller to decrypt the domain key over MS-BKRP) — once cached, every blob under that key decrypts offline. This is the recommended alternative to `vault::cred /patch`.

### Forge a golden ticket

Mint a Kerberos TGT for any account, given the krbtgt key.

```
mimikatz # kerberos::golden /user:administrator /domain:corp.example.com /sid:S-1-5-21-1004-2005-3006-1001 /krbtgt:<krbtgt-ntlm-hash> /id:500 /ptt
```

Outcome: writes a forged TGT (default file `ticket.kirbi`) claiming the chosen user, RID (`/id:500` = Administrator) and supplementary `/groups:`; `/ptt` injects it into the current session instead of writing a file, so subsequent Kerberos traffic runs as the forged identity. The key argument accepts `/krbtgt:` or its alias `/rc4:` (the NTLM hash) or `/aes256:`; `/sids:<parent-sid>-519` adds the parent domain's Enterprise Admins group for child-to-parent escalation, and `/target:` + `/service:` forge a service ticket (silver) rather than a TGT.

### Pass the hash into a new process

Start a process that authenticates with a stolen NTLM hash.

```
mimikatz # sekurlsa::pth /user:administrator /domain:corp.example.com /ntlm:<ntlm-hash> /run:powershell.exe
```

Outcome: spawns `powershell.exe` (cmd.exe by default) with a fake identity patched in memory to use the supplied hash — its outgoing SMB/HTTP authentications happen as that user, no password involved. `/impersonate` instead launches the current executable under the new identity, and `/aes256:<key>` together with `/rc4:` performs overpass-the-hash, obtaining Kerberos tickets from the AES key directly.

### Dump LSA secrets offline from registry hives

Read service passwords and DPAPI keys from saved SYSTEM/SECURITY hives.

```
mimikatz # lsadump::secrets /system:C:\temp\system.hiv /security:C:\temp\security.hiv
```

Outcome: computes the SysKey from the SYSTEM hive, decrypts the SECURITY hive's LSA secrets, and prints them — service-account passwords, autologon credentials, and DPAPI machine keys; with no hive arguments the same command runs live against the registry under SYSTEM. Sibling `lsadump::cache` unlocks NL$KM and dumps the domain cached credentials (MSCache v2 / DCC2 hashes), which crack offline as hashcat mode 2100.

### Extract Azure AD/Entra PRT material via CloudAP

Pull Primary Refresh Token secrets from an Entra-joined machine's CloudAP data.

```
mimikatz # privilege::debug
mimikatz # sekurlsa::cloudap
mimikatz # dpapi::cloudapkd /context:<context> /derivedkey:<32-byte-hex-key> /prt:<prt>
```

Outcome: `sekurlsa::cloudap` (Windows 10 build 1909+) lists the CloudAP logon session's cached key material from LSASS; `dpapi::cloudapkd` then works on that material — `/keyvalue:<base64>` with `/unprotect` decrypts the stored key, or `/context:` plus `/derivedkey:` (64 hex chars) and `/prt:` derive and verify the PRT context used to forge continuation cookies. The sibling `dpapi::cloudapreg` reads the same structures from the registry instead of memory.

### Clear a Windows event log

Erase a log, e.g. to cover post-exploitation activity in an authorized engagement.

```
mimikatz # event::clear /log:Security
```

Outcome: opens the named event log (Security by default), prints its record count, clears it via ClearEventLog, and prints the new count — a destructive and itself-detectable action (the clear leaves its own trace). `event::drop` is the experimental alternative that patches the Events service so new records are not written instead of erasing old ones.

### Strip LSASS protection with the mimidrv driver

Remove process protection (PPL) that blocks an LSASS dump.

```
mimikatz # !+
mimikatz # !processProtect /process:lsass.exe /remove
mimikatz # !-
```

Outcome: `!+` installs and starts mimikatz's kernel driver (mimidrv.sys must sit next to mimikatz.exe; needs an elevated console and is loudly flagged by EDR); `!processProtect /process:<name> /remove` clears the protection flag on the named process so dumpers can open LSASS again, and `!-` unloads the driver afterwards. The same `!` surface exposes inspection commands such as `!process` and `!ssdt`.

*Corpus evidence: command lines and console transcripts in the corpus index; every example above verified against the gentilkiwi mimikatz wiki and current module sources (`privilege`, `sekurlsa`, `lsadump`, `kerberos`, `crypto`, `vault`, `token`, `misc`, `dpapi`).*
