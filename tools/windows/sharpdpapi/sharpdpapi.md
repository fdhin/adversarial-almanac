---
name: "sharpdpapi"
categories:
  - windows
homepage: "https://github.com/GhostPack/SharpDPAPI"
origin: "hacktricks"
recipe: "upstream"
packaged-by: []
build-system: dotnet
license: BSD3-Clause
meta-checked: "2026-11-13"
github-repo: "GhostPack/SharpDPAPI"
github-match: "exact"
github-stars: 1470
gh-checked: "2026-11-13"
worth: 4.9
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-13"
---

# sharpdpapi

> C# DPAPI triage toolkit: decrypt Windows masterkeys, Credential Manager blobs, Vaults, RDP .rdg files, Chrome/Edge data and certificates — with or without domain-admin privileges.

## What it's for

SharpDPAPI (GhostPack, harmj0y) is a C# port of Mimikatz' DPAPI
functionality for post-exploitation triage. It decrypts the DPAPI-protected
secret stores of a Windows user or machine: masterkey files first, then
everything layered on them — Credential Manager entries, Vaults, RDCMan
saved RDP passwords, KeePass configs, Chrome/Edge logins+cookies (via the
bundled SharpChrome sub-project) and filesystem certificate private keys.
It accepts the domain DPAPI backup key (retrievable with domain-admin
rights), user passwords, NT hashes, DPAPI prekeys, or raw GUID:SHA1
masterkey mappings harvested from LSASS. Reach for it once you have a
foothold and want saved credentials without touching every application
individually; for on-disk browser data see also [[donpapi]] and
[[hackbrowserdata]], and [[ghostpack]] for precompiled drops of this
suite.

*No corpus evidence — derived from upstream documentation.

## Typical use cases

### Dump the domain DPAPI backup key

Domain-admin step one — one key decrypts every domain user's masterkeys,
forever.

```
SharpDPAPI backupkey /nowrap
```

Outcome: retrieves the domain controller's backup key and prints the
base64 PVK (one line with `/nowrap`); save it — it never changes and can
be reused offline with `/pvk:BASE64...` on every later engagement in that
domain. `/server:dc01.corp.example.com` targets a specific DC,
`/file:key.pvk` writes a file instead.

### Full user triage with the backup key

Decrypt everything DPAPI-protected for the current (or a copied) user.

```
SharpDPAPI triage /pvk:key.pvk
```

Outcome: decrypts reachable user masterkeys with the domain key, then
dumps and decrypts Credential Manager entries, Vaults, RDG files and
Chrome logins/cookies in one pass — saved plaintext is printed per source.
`triage` wraps the individual `masterkeys`/`credentials`/`vaults`/`rdg`
commands; point it at another user's folder with `/target:C:\Users\user\`.

### Decrypt masterkeys with a password or NT hash

Operate with only user-level credentials (no domain key).

```
SharpDPAPI masterkeys /password:Password1
SharpDPAPI masterkeys /ntlm:<nthash>
```

Outcome: derives the user's masterkey SHA1 prekeys from the password (or
NT hash) and decrypts the local masterkey files, printing
`{GUID}:SHA1` pairs — feed those to `credentials`, `vaults`, `rdg` or
`triage` (or `/mkfile:FILE`) to decrypt the layered secrets; works
remotely against `/server:SERVER` with admin access.

### SYSTEM-context machine triage

Dump machine-scoped DPAPI secrets.

```
SharpDPAPI machinetriage
```

Outcome: elevates to SYSTEM to pull the DPAPI_SYSTEM LSA secret, decrypts
machine masterkeys, then machine Credentials and Vaults — the same flow as
user `triage` for services and machine accounts.

### Recover saved RDP credentials unprivileged

Grab RDCMan passwords from a user context without touching LSASS.

```
SharpDPAPI rdg /unprotect
```

Outcome: uses `CryptUnprotectData()` to decrypt saved RDP passwords from
`.rdg`/`RDCMan.settings` files — valid only when run as the user who
saved them, but needs no elevation and no LSASS access;
`/target:C:\path\to\file.rdg` selects a specific file.

### Extract browser logins and cookies (SharpChrome)

Decrypt Chrome/Edge/Slack data files with a domain key.

```
SharpChrome logins /pvk:key.pvk
SharpChrome cookies /url:"^https://.*\.example\.com" /format:json
```

Outcome: the first dumps saved browser credentials as CSV (default) or
table; the second filters cookies by URL regex and emits
EditThisCookie-importable JSON (`/setneverexpire` extends their expiry).
`/browser:edge` switches to Edge, `/target:FILE` points at copied
`Login Data`/`Cookies` files from another host.
