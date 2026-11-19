---
name: "ntlm-challenger"
categories:
  - blackarch-scanner
version: "8.bd61ef6"
homepage: "https://github.com/b17zr/ntlm_challenger"
packaged-by: [blackarch, nixpkgs]
blackarch-builddate: "2026-01-17"
build-system: scripts
license: MIT
meta-checked: "2026-11-09"
github-repo: "b17zr/ntlm_challenger"
github-stars: 152
github-pushed: "2022-11-10"
github-archived: false
github-issues: 1
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# ntlm-challenger

> Fires an anonymous NTLM negotiation at an HTTP, SMB or MSSQL endpoint and pretty-prints the NTLMSSP challenge — domain, computer names, build and negotiate flags.

## What it's for

ntlm_challenger is a single-purpose recon tool: it sends an NTLM negotiate message (a fake authentication attempt) to any endpoint that accepts NTLM authentication, receives the challenge message, and parses it into the details the server leaks before any credentials are checked — AD domain name, NetBIOS/DNS computer names, DNS tree name, Windows build version, and the negotiate flags the server supports. Because pre-authentication challenge data is rich, this is a cheap way to fingerprint Windows hosts and identify Exchange/AD endpoints during external or internal recon. It does not authenticate or test credentials; it only reads the challenge. One URL argument selects the protocol via scheme (`http(s)://`, `smb://`, `mssql://`). Upstream runs it as `python3 ntlm_challenger.py <URL>`; distro packages install the same script as `ntlm-challenger`.

## Typical use cases

### Probe an HTTP NTLM endpoint

```
ntlm-challenger 'https://mail.example.com/autodiscover/'
```

Outcome: prints the target's AD domain, Windows server version (e.g. "Server 2016 or 2019 / Windows 10 (build 18362)"), the full TargetInfo block (NB/DNS domain, computer and tree names, timestamp) and the negotiate flags — enough to confirm Exchange/AD and the internal naming scheme.

### Probe an SMB host

```
ntlm-challenger 'smb://198.51.100.10'
```

Outcome: connects over SMB via impacket, triggers the NTLM challenge, and prints the same parsed block for the file server — useful when only an IP is known and the hostname/domain is not.

### Probe an MSSQL server

```
ntlm-challenger 'mssql://198.51.100.20'
```

Outcome: negotiates NTLM with the SQL Server instance and prints its domain membership and host details, revealing the internal naming behind a database listener.

*No corpus evidence — derived from upstream documentation (README usage and output examples of b17zr/ntlm_challenger).*
