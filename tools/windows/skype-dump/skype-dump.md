---
name: "skype-dump"
categories:
  - blackarch-windows
version: "0.1"
homepage: "http://packetstormsecurity.com/files/119155/Skype-Hash-Dumper.0.html"
packaged-by: [blackarch]
blackarch-builddate: "2018-11-15"
build-system: windows
license: custom:unknown
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# skype-dump

> Demonstrates decrypting the MD5 password hash that the Windows Skype client stored in its configuration file.

## What it's for

skype_dump (Kevin Devine, insecurety.net, December 2012) is a proof-of-concept shipped as C++ source plus a prebuilt Windows executable. The Windows Skype client of that era stored the account password as an MD5 hash, encrypted, inside its XML configuration file; skype_dump locates that data, derives the key, decrypts it and prints the recovered MD5 hash (the Linux client of the time did not encrypt it at all). It cannot reverse the hash to plaintext by itself — the point is that the recovered hash is directly crackable offline, and John the Ripper ships a module for exactly this scheme. The same code also runs the derivation forward, so a username/password pair can be hashed and compared against what the configuration file would store. Useful when auditing old Windows images or legacy Skype credential material; irrelevant to current Skype, which no longer stores credentials this way.

## Typical use cases

### Recover the password hash from the local Skype configuration

```
skype_dump
```

Outcome: locates the local Skype configuration file, decrypts the stored credential material and prints the account name with its MD5 password hash — ready to feed to John the Ripper's Skype format or an equivalent offline cracker.

### Verify a known credential against the stored scheme

```
skype_dump user Password1
```

Outcome: runs the derivation algorithm forward for the given username and password and prints the resulting hash, confirming whether the pair matches what the configuration mechanism would have stored.

*No corpus evidence — derived from upstream documentation.*
