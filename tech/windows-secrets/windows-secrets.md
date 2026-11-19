---
technology: "Windows secret stores (LSASS, SAM/SECURITY hives, LSA secrets, DPAPI)"
kind: overview
audience: offensive-security
family: windows
layer: "OS credential storage (local attack surface)"
language: "C/C++ (system components)"
homepage: "https://learn.microsoft.com/windows-server/security/credentials-protection-and-management/"
provenance: "evidence-grounded (16 files with command-context evidence after the merge: mimikatz/dpapi chains; LSASS minidump + pypykatz and autologon; hive extraction; LSA-secret autologon; impacket-dpapi) + upstream: Microsoft Learn (credentials protection & management, DPAPI CryptProtectData, AD DPAPI backup keys, netsh wlan, autologon registry); gentilkiwi mimikatz module docs. Verified 2026-11-12."
---

# Windows secret stores

> Windows caches every credential it has ever needed again — in a process you can dump, in registry hives you can copy, and in blobs an AD administrator can decrypt by design — so one administrator foothold is rarely one set of credentials.

## What it is

This unit is the **local credential-store attack surface** of a Windows host: the places the OS itself keeps secrets so that logons, services, and applications "just work". Five stores matter, and they interlock. **LSASS** (Local Security Authority Subsystem) holds live logon sessions — including recoverable cleartext, NT hashes, Kerberos tickets, and DPAPI master keys — for every session since boot. The **SAM hive** holds local account NT hashes, encrypted with a boot key derived from the **SYSTEM** hive; the **SECURITY** hive holds LSA secrets — service-account passwords, machine-account material, and the AutoLogon `DefaultPassword`. **DPAPI** (Data Protection API) wraps user/application secrets (saved browser passwords, RDP `.rdg` files, WiFi PSK XML, credential files under `%APPDATA%\Microsoft\Credentials`) in per-user master keys. And the **Winlogon registry keys** keep AutoLogon passwords in cleartext because the machine must type them at boot.

The design brief was "recoverable by the right principal," and that is the operator takeaway: the right principal is *whatever already owns the machine or the domain*. DPAPI even has an explicit escrow — per Microsoft's documentation, every domain controller holds **domain-wide DPAPI backup keys** generated once at domain creation, precisely so any domain admin can unwrap any user's master key. Defender-side hardening exists but is opt-in per component: LSA protection (`RunAsPPL`, Windows 8.1+) blocks non-protected processes from dumping LSASS, and Credential Guard virtualizes it — on hosts where those are off (most Server-era boxes observed), a local SYSTEM foothold converts directly into every credential the box has seen.

The unit deliberately **merges** the credential stores: the merged scope — LSASS dumps, hive extraction, LSA secrets, DPAPI, autologon, wireless keys — is exercised end-to-end in real estates.

## The anatomy that matters on an engagement

    logon (interactive / service / network)
      → LSASS process memory  ── procdump / MiniDump / mimikatz sekurlsa ──> cleartext, NTLM, Kerberos, DPAPI keys
    boot
      → SYSTEM hive  ── boot key ─┐
      → SAM hive     ── hashes ───┤→ secretsdump LOCAL / mimikatz lsadump::sam → local accounts
      → SECURITY hive ─ LSA secrets (service pwds, AutoLogon DefaultPassword, machine account)
    user session
      → %APPDATA%\...\Protect\<SID>\  master keys ── dpapi::masterkey (user pw or SHA1) ─┐
      → %APPDATA%\Microsoft\Credentials  blobs      ── dpapi::cred / SharpDPAPI ────────┤→ saved app secrets
      → domain escrow: DC backup keys (dpapi::backupkeys) unlocks ANY user's master key ─┘

Three structural properties drive most findings:

- **Everything is decryptable by SYSTEM or a domain admin by design.** The question after a foothold is never "is there more here" but "which store do I open first".
- **The stores cross-pollinate.** An LSASS dump contains DPAPI master keys; the SYSTEM hive decrypts SAM and LSA secrets; a user's password or the DC backup keys decrypt DPAPI blobs. Each store you open unlocks the next.
- **Secrets outlive their sessions.** AutoLogon passwords sit in the registry until changed; DPAPI blobs sit in profiles forever; only LSASS memory is wiped at reboot.

## Where you'll meet it

- **Post-foothold on any Windows host** — the universal second step: dump LSASS or pull hives, and the box's own cache names the next hop. Implication: the default after an Evil-WinRM shell is `reg save hklm\sam`/`system` (or `secretsdump` over the wire) before anything exotic.
- **Forensic shares and artifact drops** — one observed chain: a zipped `lsass.DMP` on a backup share; parse offline with pypykatz, no live access to the host at all. Artifact repositories are the credential store's shadow.
- **Workstations with saved application state** — browsers (Chrome/Edge logins via `dpapi::chrome`), RDP managers, WiFi profiles (`netsh wlan show profile key=clear`). Implication: a user-context shell (no admin needed) opens the DPAPI store for that user — creds first, escalation later.
- **Domain-adjacent boxes** — a DC (or anything with the domain backup keys) is a universal decryptor for every user's DPAPI data in the domain.

## Reading the system's mind

Five facts predict what a Windows target will hand over — each expands into a phase of [[windows-secrets-bug-bounty|the methodology]]:

1. **LSASS holds only what has logged on since boot** — dump timing matters; a service that ran last week left its password in LSA secrets, not in LSASS.
2. **SAM hashes need the SYSTEM hive, never one without the other** — `reg save` both, or let secretsdump fetch both over SMB.
3. **AutoLogon means the password is in the registry in cleartext** — Microsoft documents this consequence itself; winPEAS/Seatbelt-style enumeration prints it, secretsdump/mimikatz print it as the `DefaultPassword` LSA secret (observed repeatedly).
4. **DPAPI decryption follows the key, not the file** — with the user's password, their SID, or (from a DC) the domain backup keys, every blob in every profile falls; without a key, blobs are inert.
5. **`RunAsPPL`/Credential Guard change the *how*, not the *whether*** — protected LSASS pushes you to hive extraction, LSA secrets, or pypykatz-over-a-copy; the rest of the surface stays open.

## Where to go next

[[windows-secrets-bug-bounty|Windows secret stores methodology]] — LSASS dump acquisition and parsing (live mimikatz, offline pypykatz), hive extraction with `reg save` and offline/remote secretsdump, LSA secrets and the AutoLogon password, DPAPI unlocking (user key, machine key, domain backup keys), wireless profile recovery, and the tool workflows in prose.
