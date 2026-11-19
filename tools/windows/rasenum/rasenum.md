---
name: "rasenum"
categories:
  - blackarch-windows
version: "1.0"
homepage: "http://www.cultdeadcow.com/tools/rasenum.html"
packaged-by: [blackarch]
blackarch-builddate: "2018-11-12"
build-system: windows
license: custom:unknown
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# rasenum

> Cult of the Dead Cow's RAS enumerator: dumps dial-up and VPN phonebook entries with whatever credentials the OS exposes.

## What it's for

RASEnum (by Sir Dystic of the Cult of the Dead Cow) lists the entries in Windows Remote Access Service phonebook files (.pbk) — dial-up, VPN and direct serial connections — with everything the RAS APIs will give up: it walks the entries returned by RasEnumEntries() and pulls each one's details via RasGetCredentials(). On Windows 9x it is paired with the era's cached-password retrieval (WNetEnumCachedPasswords); on Windows NT through the 2000 line the RAS credential calls still returned stored passwords in plaintext, while Windows 2000 and later hand back a handle instead of the password — on those systems only the remaining connection information is displayed. It is a legacy Windows command-line tool that takes no required arguments: run it bare and it enumerates the default RAS phonebook (usually %windir%\system32\ras\rasphone.pbk), or pass the path to another .pbk file to inspect that phonebook instead.

## Typical use cases

### List RAS connections and stored credentials

Run it on the target Windows system.

```
RASEnum.exe
```

Outcome: prints every RAS/VPN entry in the default phonebook with its connection properties and, on pre-Windows-2000 systems, the stored plaintext password for each entry — useful both for auditing saved dial-up/VPN credentials and for recovering them during authorized legacy-system assessments.

### Inspect a collected phonebook file

Point it at a .pbk recovered from another host or user profile.

```
RASEnum.exe phonebook.pbk
```

Outcome: enumerates the entries of the named phonebook file instead of the default RAS phonebook, so collected .pbk files can be inventoried without touching the system's own RAS configuration.

*No corpus evidence — derived from upstream documentation.*
