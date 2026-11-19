---
name: "ghostpack"
categories:
  - blackarch-windows
version: "123.20a5f0a"
homepage: "https://github.com/r3motecontrol/Ghostpack-CompiledBinaries"
packaged-by: [blackarch]
blackarch-builddate: "2024-10-27"
build-system: windows
license: BSD3-Clause
meta-checked: "2026-11-09"
github-repo: "r3motecontrol/Ghostpack-CompiledBinaries"
github-stars: 1776
github-pushed: "2024-10-24"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.7
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# ghostpack

> Ready-to-run compiled .NET binaries of the GhostPack post-exploitation suite (Rubeus, SharpDPAPI, SharpChrome, Seatbelt and more).

## What it's for

This package spares you compiling the GhostPack family yourself: it ships
release binaries of the C# tools maintained under the GhostPack GitHub
organization by harmj0y and contributors — Rubeus, SharpDPAPI, SharpChrome,
SharpRoast, SharpUp, SharpDump, SharpWMI, SafetyKatz, Seatbelt, Certify,
ForgeCert, Koh, LockLess and RestrictedAdmin — in separate folders for
.NET v3.5, v4.5, v4.7.2 and v4.8.1. Each binary is an independent upstream
project with its own repository, README and flag surface; this collection
only re-packages them for execution on Windows targets (drop the matching
.NET-version folder onto a host and run the `.exe` from a command shell).
The tools are intended for authorized security testing only.

Corpus-grounded: practitioners run the collection's flagship members
directly from the toolkit folder, e.g. `Rubeus.exe kerberoast`,
`Rubeus.exe hash /password:...` and `Rubeus.exe silver /service:...`.

## Typical use cases

### Kerberoast service accounts with Rubeus

Request crackable service tickets for accounts with SPNs.

```
Rubeus.exe kerberoast /outfile:hashes.txt
```

Outcome: queries the domain for Kerberoastable accounts, requests their
service tickets, and writes RC4-encrypted ticket hashes to `hashes.txt`
for offline cracking (e.g. with a cracking tool of your choice).

### Derive hash forms of a known password

Turn a plaintext credential into the key formats Kerberos tools expect.

```
Rubeus.exe hash /password:Password1 /user:user /domain:example.com
```

Outcome: prints the RC4/NTLM and AES key derivations for the given
password and username, ready to paste into `/rc4:` or `/aes256:` options
of later Rubeus actions.

### Forge a silver ticket

Craft a service ticket for a specific SPN with a known service account
hash.

```
Rubeus.exe silver /rc4:NTLMHASH /user:user /service:MSSQLSvc/target.example.com:1433 /domain:example.com /sid:DOMAIN_SID
```

Outcome: forges a silver ticket for the `MSSQLSvc/target.example.com:1433`
service and prints it as a base64 `.kirbi`, granting access to that
service without contacting the KDC again.
