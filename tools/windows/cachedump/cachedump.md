---
name: "cachedump"
categories:
  - blackarch-windows
version: "1.1"
homepage: "https://packetstormsecurity.com/files/36781/cachedump.1.zip.html"
packaged-by: [blackarch]
blackarch-builddate: "2018-11-15"
build-system: windows
license: custom:unknown
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# cachedump

> Dump Windows domain logon cache entries (MSCASH hashes) from a live Windows 2000/XP/2003 host running as SYSTEM.

*No corpus evidence — derived from the packaged upstream source (CacheDump 1.1 by Arnaud Pilon, Thales Security Systems, distributed via Packet Storm).*

## What it's for

When a domain-joined machine authenticates a user while the domain controller is unreachable, LSASS validates the logon against cached credentials stored in `HKLM\SECURITY\CACHE\NL$1..NL$10` (up to ten entries by default). Those entries hold the username, domain and an MSCASH hash — `MD4(MD4(password) || lowercase(username))` — encrypted with a machine-specific LSA key. CacheDump runs on the target Windows host: it drops a temporary NT service (instead of DLL injection) to run inside LSASS's context, reads the static LSA key from LSASS memory, decrypts every cache entry and prints them in pwdump-style lines ready for offline cracking. It requires Administrator/SYSTEM on the host, targets Windows 2000/XP/2003, and does not touch the domain controller itself — it is the classic companion to dumping a laptop's last-used domain credentials.

## Typical use cases

### Dump the cached domain credentials

```
cachedump.exe
```

Outcome: installs the temporary service, reads the LSA key from LSASS, decrypts each `NL$n` entry and prints one `user:mscash-hash:domain` line per cached logon (e.g. `user:2d9f0b052932ad18b87f315641921cda:lab:lab.internal`); the service is stopped and removed on exit. Run from an elevated prompt on the target host.

### Troubleshoot a failed dump

```
cachedump.exe -vv
```

Outcome: prints every step of the dump process (service install, LSA key retrieval, per-entry decryption), showing exactly where a dump that returned no lines failed; `-v` gives one level less detail. If a previous run died and left its service installed, `cachedump.exe -K` kills and removes it so the next run starts clean.

### Crack the dumped hashes offline

```
john --format=mscash mscash.txt
```

Outcome: John the Ripper loads the `user:hash` lines copied from CacheDump's output and runs dictionary/brute-force attacks against each MSCASH hash (which is salted with the username), printing recovered plaintexts per username.
