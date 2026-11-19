---
name: "evil-winrm-py"
categories:
  - blackarch-windows
version: "1.7.0"
homepage: "https://github.com/adityatelange/evil-winrm-py"
packaged-by: [blackarch, aur, nixpkgs]
blackarch-builddate: "2026-10-06"
build-system: windows
license: MIT
meta-checked: "2026-11-09"
github-repo: "adityatelange/evil-winrm-py"
github-stars: 418
github-pushed: "2026-10-24"
github-archived: false
github-issues: 1
github-match: "exact"
gh-checked: "2026-11-07"
worth: 7.3
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 44
gh-activity-checked: "2026-11-09"
---

# evil-winrm-py

> Python WinRM shell for interactive remote command execution and file transfer against Windows hosts.

## What it's for

evil-winrm-py is a Python rewrite of the original Ruby evil-winrm, built for Windows and Active Directory pentesting: it opens an interactive PowerShell shell over the WinRM protocol (default port 5985, or 5986 with `--ssl`) and adds the conveniences that make a working session practical — file upload/download with progress bars and MD5 checksum verification, Tab completion of local and remote paths and cmdlets, in-memory execution of local PowerShell scripts, DLLs and EXEs on the remote host, command history and colorized output. It supports NTLM, pass-the-hash, certificate and Kerberos authentication. Reach for it when you hold credentials, an NT hash or a Kerberos ticket for an account with WinRM rights on a Windows host and need a full interactive session rather than one-off commands. It is a client only: it exploits nothing by itself and cannot reach hosts where WinRM is disabled or firewalled.

## Typical use cases

### Open a shell with a username and password

Authenticate to a WinRM-enabled host with plaintext credentials.

```
evil-winrm-py -i 198.51.100.10 -u user -p Password1
```

Outcome: after authentication, an interactive PowerShell prompt on the remote host opens; commands typed at the prompt execute remotely with their output returned inline.

### Connect with a Kerberos ticket

Reuse an exported TGT instead of a password, e.g. after obtaining a ticket for a domain account and exporting `KRB5CCNAME`.

```
evil-winrm-py -i 198.51.100.10 -k
```

Outcome: the tool authenticates via Kerberos (requires the optional kerberos/gssapi extra), prints `Connecting to '198.51.100.10:5985' as <user>@<REALM>` and drops into the interactive PowerShell session — no password is sent to the host.

### Pass the hash

Reuse a captured NT hash without knowing the plaintext password.

```
evil-winrm-py -i 198.51.100.10 -u user -H <nthash>
```

Outcome: NTLM logon succeeds with the hash and the interactive shell opens, enabling the usual post-exploitation workflow on the target.

### Run a local PowerShell script on the remote host

From inside the shell, execute a script that lives on your own machine without writing it to the target's disk.

```
runps /opt/tools/PowerView.ps1
```

Outcome: the script is read locally, executed remotely, and its functions remain loaded in the session — `[+] PowerShell script ran successfully.` is printed and functions like `Invoke-RunasCs` or `Get-Domain` can then be called directly at the prompt (`loadps` only loads functions without running the script; `loaddll` and `runexe` do the same for DLLs and EXEs in memory).

### Upload a file to the target

From inside the shell, copy a local binary to the remote filesystem.

```
upload /opt/tools/example.exe C:\Users\user\Documents\example.exe
```

Outcome: a progress bar with transfer speed, an MD5 checksum verification, and `[+] File uploaded successfully as: C:\Users\user\Documents\example.exe` — the binary is then runnable from the shell (`download <remote> <local>` is the reverse direction).
