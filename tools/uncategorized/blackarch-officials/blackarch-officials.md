---
name: "blackarch-officials"
categories:
version: "20260910"
homepage: "https://www.blackarch.org/"
packaged-by: [blackarch]
blackarch-builddate: "2026-09-09"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# blackarch-officials

> BlackArch meta-package that pulls in the distro's official security toolset in one pacman transaction.

## What it's for

A pure meta-package: it ships no files, no commands and no code — only a large dependency list (afl++, aircrack-ng, bettercap, binwalk, checksec and hundreds of others) mirroring BlackArch's "official tools" selection from the Arch/BlackArch repositories. Its only role is provisioning: installing it populates a workstation with the official tool collection in one operation instead of selecting packages one by one. There is nothing to run here; for any behavioral question — what a tool does, its flags, its use cases — consult the individual tool's note. Getting it onto a system is covered in install.md.

*No corpus evidence — derived from upstream documentation.*
