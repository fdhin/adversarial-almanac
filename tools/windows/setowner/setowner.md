---
name: "setowner"
categories:
  - blackarch-windows
version: "1.1"
homepage: "https://vidstromlabs.com/freetools/setowner/"
packaged-by: [blackarch]
blackarch-builddate: "2016-07-28"
build-system: windows
license: custom:unknown
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# setowner

> Tiny Windows console tool to read or reassign file ownership to any account, using the Restore-privilege trick.

## What it's for

SetOwner (by Arne Vidstrom, vidstromlabs.com) sets the owner of a file
to any account you name — not just an account you control. Plain
administrators normally can only take ownership themselves (SeTakeOwner-
Privilege); SetOwner uses the "Restore files and directories" user right
(SeRestorePrivilege), held by Administrators and Backup Operators by
default, to write an arbitrary owner. That makes it useful after
incidents or during forensics/backups to hand files back to their
original owners, and in lab work to demonstrate the difference between
the two privileges. It is a single small console executable with no
options beyond its two arguments; BlackArch ships it as
`/usr/share/windows/setowner/setowner.exe` and the author's site
currently distributes version 1.2.

*No corpus evidence — derived from upstream documentation.*

## Typical use cases

### Read a file's current owner

Check who owns a file before changing anything.

```
setowner c:\files\file.txt
```

Outcome: prints the owner of the file in `DOMAIN\account` form (or
`LOCAL\account` for local accounts) — the baseline before any change.

### Assign ownership to any account

Hand a file to a different user.

```
setowner c:\files\file.txt user1
```

Outcome: makes `user1` the owner of the file, provided your session
holds the "Restore files and directories" right; without it the tool
fails with "Insufficient user rights". Domain-qualified accounts work as
`setowner c:\files\file.txt domain2\user2`.
