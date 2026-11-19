---
name: "crackmapexec"
categories:
  - blackarch-scanner
version: "v6.0.1.r198.gda472cb"
homepage: "https://github.com/byt3bl33d3r/CrackMapExec"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-23"
build-system: scripts
license: BSD
meta-checked: "2026-11-09"
github-repo: "byt3bl33d3r/CrackMapExec"
github-stars: 9170
github-pushed: "2023-12-06"
github-archived: true
github-issues: 66
github-match: "exact"
gh-checked: "2026-11-07"
worth: 0.6
worth-basis: "historical (archived/gone upstream)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# crackmapexec

> Windows/AD network attack driver: validates credentials and NTLM hashes across SMB, WinRM, MSSQL, SSH and LDAP in one uniform command, then enumerates or dumps from whatever authenticates.

## What it's for

CrackMapExec (CME, by byt3bl33d3r — Marcello Salvati, who publishes under the handle mpgn and points the archived README's issue tracker at mpgn/CrackMapExec) wraps remote-authentication and post-exploitation primitives behind one CLI: pick a protocol, give it targets plus usernames, passwords or NTLM hashes, and it reports per-host authentication results — a valid login prints the status marker `(Pwn3d!)` — while optional flags layer enumeration and dumping on top. Its canonical job is credential validation and password spraying at subnet scale, but the same authentication layer drives share enumeration, SAM/LSA/NTDS dumping, remote command execution (`-x cmd`, `-X powershell-command`) and a module system (`-M`). It was inspired by CredCrack, smbexec and smbmap. The repository is archived (December 2023, "no longer maintained due to the existence of a hostile fork") and NetExec is the community-maintained successor — see the [[netexec]] note for the maintained lineage; everything here reflects the archived CME interface. Binaries: `crackmapexec` / `cme`.

## Typical use cases

### Validate credentials or spray a password over SMB

```
crackmapexec smb 198.51.100.0/24 -u users.txt -p 'Password1' -d CORP
```

Outcome: every user/password combination is tried against every host in the target range; output is one status line per host, with `(Pwn3d!)` marking successful authentication and the failure reason otherwise. `-d` sets the domain/NetBIOS name, `--local-auth` flips to local accounts. Either side accepts a list (`-u users.txt -p passwords.txt`); spraying stops at the first valid login unless `--continue-on-success` is set.

### Test credentials across other services

```
crackmapexec winrm 198.51.100.10 -u users.txt -p passwords.txt --continue-on-success
crackmapexec mssql 198.51.100.10 -u svc_mssql -p 'Password1'
crackmapexec ssh 198.51.100.10 -u user -p passwords.txt
crackmapexec ldap 198.51.100.10 -u user -p 'Password1'
```

Outcome: the same validation surface over WinRM, MSSQL, SSH or LDAP. A `(Pwn3d!)` on winrm means command execution is likely available; mssql results double as database-credential checks (`--port` selects a non-default port).

### Enumerate SMB shares and access rights

```
crackmapexec smb target.example.com -u 'guest' -p '' --shares
```

Outcome: lists the host's shares with READ/WRITE/ACCESS columns for the given identity — a null or guest session (`-u '' -p ''`) is often enough on misconfigured hosts. Sibling enumeration flags: `--sessions`, `--disks`, `--loggedon-users`, `--groups`, `--pass-pol`.

### Pass-the-hash authentication

```
crackmapexec smb 198.51.100.10 -u Administrator -H 13b29964cc2480b4ef454c59562e675c
```

Outcome: authenticates with an NTLM hash instead of a password — either a bare NTHASH or the `LM:NT` pair — printing `(Pwn3d!)` when the hash is accepted. Combine with `--shares`, `--sam` or `--lsa` for the standard hash-then-harvest chain.

### Dump local credential stores

```
crackmapexec smb 198.51.100.10 -u Administrator -p 'Password1' --sam
crackmapexec smb 198.51.100.10 -u Administrator -p 'Password1' --lsa
```

Outcome: `--sam` dumps local SAM hashes and `--lsa` LSA secrets using secretsdump-style methods, printed inline per host; both need administrative rights. Against a domain controller, `--ntds` (optionally `--ntds vss`) dumps the NTDS.dit, with `--ntds-history` and `--ntds-pwdLastSet` for password-history detail.

### Enumerate domain users from a domain controller

```
crackmapexec smb 198.51.100.10 -u user -p 'Password1' --users
```

Outcome: pulls the domain user list over SMB when run against a DC with an account that has listing rights; `--rid-brute` instead enumerates users by bruteforcing RID values when that right is missing.

### Spider shares for files by pattern

```
crackmapexec smb 198.51.100.10 -u user -p 'Password1' --spider C$ --pattern txt
```

Outcome: walks the share's file tree and reports matching file paths — `--pattern txt` matches any name containing "txt" (finds both sometxtfile.html and somefile.txt). Related flags: `--spider-folder` to set the start folder, `--content` to match file contents, `--exclude-dirs` to prune directories.

### Run post-exploitation modules

```
crackmapexec smb -L
crackmapexec smb 198.51.100.10 -u Administrator -p 'Password1' -M mimikatz --options
crackmapexec smb 198.51.100.10 -u Administrator -p 'Password1' -M mimikatz -o COMMAND='privilege::debug'
```

Outcome: `-L` lists the modules available for the protocol, `-M <name>` runs one across the target set, `--options` prints a module's parameters and `-o KEY=VALUE` sets them (e.g. the mimikatz module's `COMMAND`). Modules that postdate CME's archival — such as the LDAP `adcs` and `maq` modules often invoked under the crackmapexec name in newer material — belong to NetExec, the maintained successor.

### Execute commands on authenticated hosts

```
crackmapexec smb 198.51.100.10 -u Administrator -p 'Password1' -x whoami
crackmapexec winrm 198.51.100.10 -u Administrator -p 'Password1' -X '$PSVersionTable'
```

Outcome: `-x` runs a cmd command and `-X` a PowerShell command, printing `[+] Executed command` with the output inline per host — administrative rights are required, and the `(Pwn3d!)` marker shows exactly which hosts grant them. Available on smb, winrm, mssql and ssh; over SMB the default fails over wmiexec → atexec → smbexec, and `--exec-method wmiexec` pins one mechanism.

### Authenticate with Kerberos

```
crackmapexec smb dc01.example.com --kerberos --kdcHost dc01.example.com -u user -p 'Password1'
```

Outcome: `-k`/`--kerberos` switches the login to Kerberos (with `KRB5CCNAME` exported, an existing ccache is used instead of requesting a ticket) while `--kdcHost` names the domain controller tickets are requested from; the target must be addressed by the same hostname the ticket was issued for. Every enumeration flag and module rides on top exactly as with password auth.
