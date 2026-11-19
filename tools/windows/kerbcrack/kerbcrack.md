---
name: "kerbcrack"
categories:
  - blackarch-windows
version: "1.3d3"
homepage: "http://ntsecurity.nu/toolbox/kerbcrack/"
packaged-by: [blackarch]
blackarch-builddate: "2016-07-28"
build-system: windows
license: custom:unknown
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# kerbcrack

> Windows tool pair that sniffs Kerberos pre-auth logins off the wire and cracks the captured passwords offline.

## What it's for

KerbCrack (v1.3d3, Arne Vidstrom, 2002–2005) consists of two console programs: `kerbsniff`, which listens on the network and captures Windows 2000/XP-style Kerberos logins — the pre-authentication data carries a keyed hash of the user's password — and `kerbcrack`, which recovers the password from a capture file by dictionary or brute-force attack. It attacks captured pre-auth hashes, not the KDC itself, and targets the RC4-style pre-auth of the Windows 2000/XP/2003/Vista era. Both programs print short instructions when run without arguments.

## Typical use cases

### Capture Kerberos logins off the wire

```
kerbsniff capture.log
```

Outcome: starts the listener and writes captured Kerberos pre-authentication data to capture.log, keeping a running `Captured packets:` counter; the file is what kerbcrack consumes.

### Crack a capture file with a dictionary

```
kerbcrack capture.log -d words.txt
```

Outcome: loads the capture file and tests each captured login against the dictionary; recovered passwords are printed as found, with a running `Number of cracked passwords this far` count.

### Brute-force a capture file

```
kerbcrack capture.log -b2
```

Outcome: brute-forces each captured hash over the `-b2` character set (a-z, A-Z, 0-9); `-b1` covers letters only, `-b3` adds special characters, and an optional password-size argument caps the candidate length.

*No corpus evidence — derived from upstream documentation.*
