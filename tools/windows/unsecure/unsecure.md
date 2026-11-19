---
name: "unsecure"
categories:
  - blackarch-windows
version: "1.2"
homepage: "http://www.sniperx.net/"
packaged-by: [blackarch]
blackarch-builddate: "2018-11-13"
build-system: windows
license: custom:unknown
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# unsecure

> Legacy Windows NT network-login brute-forcer that fills in login "masks".

## What it's for

UnSecure v1.2 is a small Windows-era (NT/2000) brute-forcing tool: given a network login template ("mask") it systematically tries combinations to guess valid account credentials against remote Windows authentication. It belongs to the late-1990s/early-2000s packetstorm generation of NT crackers and is long unmaintained — the author's site (sniperx.net) is gone and the packaged archive (`uns12exe.zip`) survives on file mirrors. No usage documentation is retrievable from upstream, so this note is description-only: treat any binary of this age as untrusted legacy software (run it only in an isolated lab, if at all) and prefer modern maintained equivalents such as [[crackmapexec]]-style brute forcing for the same goal.

*No corpus evidence — derived from upstream documentation (BlackArch package description and PKGBUILD source archive); no verifiable CLI.*
