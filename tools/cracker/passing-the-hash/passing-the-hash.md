---
name: "passing-the-hash"
categories:
  - passwords
version: "0~2015.12.37+b1"
homepage: "http://passing-the-hash.blogspot.fr"
origin: "kali"
recipe: "kali-debian"
packaged-by: [kali]
build-system: autotools
meta-checked: "2026-11-09"
github-repo: "Sgt-Mac/passing-the-hash"
github-stars: 0
github-pushed: "2015-06-10"
github-archived: false
github-issues: 1
github-match: "search"
gh-checked: "2026-11-07"
worth: 2.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
---

# passing-the-hash

> pth-* wrappers around Samba, curl, sqsh and WMI clients that authenticate with a raw NTLM hash instead of a password.

*No corpus evidence — derived from upstream documentation.*

## What it's for

This Kali package ships the "Still Passing the Hash 15 Years Later" toolset (from Skip Duckwall and Chris Campbell's BlackHat USA 2012 work): rebuilt or wrapped versions of standard client tools whose password field accepts an NTLM hash pair, so you can authenticate with a dumped credential (e.g. an NT hash from an SAM/NTDS harvest) without knowing or cracking the plaintext. The password argument takes the 65-character `LM:NT` form — when you hold only the NT half, pad with the empty-LM constant `aad3b435b51404eeaad3b435b51404ee` (the example NT value below is the empty-password hash; substitute your captured pair). Installed commands include pth-curl, pth-smbclient, pth-rpcclient, pth-net, pth-smbget, pth-winexe, pth-wmic, pth-wmis and pth-sqsh. Current Samba has native `--pw-nt-hash` support covering part of this ground; the package stays relevant for the winexe/wmis/sqsh extras and for workflows already scripted around the `pth-` names.

## Typical use cases

### List and open SMB shares with a hash

Enumerate a host's shares, then drop into an administrative share.

```
pth-smbclient -L //198.51.100.10 -U 'CORP/user%aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0'
pth-smbclient //198.51.100.10/C$ -U 'CORP/user%aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0'
```

Outcome: authenticates to 198.51.100.10 as CORP\user using only the supplied hash (the wrapper preloads a patched Samba library into the stock smbclient) — the first command lists the host's shares, the second opens an interactive ftp-like session on C$; an invalid pair fails with NT_STATUS_LOGON_FAILURE.

### Get an interactive Windows shell via winexe

Run a remote command shell as the hash-authenticated user.

```
pth-winexe -U 'CORP/user%aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0' //198.51.100.10 cmd
```

Outcome: opens an interactive cmd.exe on 198.51.100.10 authenticated purely with the hash (the wrapper execs the separately installed winexe binary with the pass-the-hash shim preloaded) — a working shell confirms both the hash and the account's remote-login rights; the same syntax with `//host/share` style targets works for running any command instead of `cmd`.

### Authenticate to an NTLM-protected HTTP endpoint

Reuse the hash against web content.

```
pth-curl -v --ntlm -u 'user%aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0' http://198.51.100.10/
```

Outcome: performs NTLM HTTP authentication where the patched curl reads the password field as the 65-character LM:NT pair; the verbose trace shows the negotiate/challenge exchange ending in HTTP 200 for valid credentials (401 for an invalid pair) — handy for Intranet sites that accept only Integrated Authentication.

### Query MSSQL through FreeTDS/sqsh

Use the hash against a database listener.

```
pth-sqsh -S 198.51.100.10 -U user -P 'aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0'
```

Outcome: the wrapper points sqsh at a patched FreeTDS client library, which interprets the `-P` value as the LM:NT pair; on success you land at the sqsh SQL prompt against the instance (confirm with `SELECT @@version`) — a plain wrong password yields a login-failed error from the server.
