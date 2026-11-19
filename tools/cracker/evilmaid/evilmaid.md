---
name: "evilmaid"
categories:
  - blackarch-cracker
version: "1.01"
homepage: "https://theinvisiblethings.blogspot.com"
packaged-by: [blackarch]
blackarch-builddate: "2025-12-14"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# evilmaid

> Invisible Things Lab's proof-of-concept "Evil Maid" bootable USB image: trojans a TrueCrypt boot loader to record the full-disk-encryption password.

*No corpus evidence — derived from upstream documentation.*

## What it's for

This is not a command-line tool but a research artifact: the `evilmaidusb-1.01.img` bootable disk image released by Joanna Rutkowska's Invisible Things Lab (the Qubes OS team) to demonstrate the Evil Maid attack against full disk encryption. Booting a target machine from this USB replaces the TrueCrypt boot loader on disk with a trojaned copy that records the volume password when the legitimate user next types it; the attacker boots the USB again later to retrieve it. The point of the release was to prove that FDE without a trusted boot path (measured/boot-attested boot, or keeping the boot partition on removable media) protects nothing against an attacker with brief physical access. It targets the TrueCrypt era (~2009-2010); it is preserved as historical material, and the package ships only the image — there is no CLI, nothing to configure, and writing it to media is a deployment step outside this note's scope.

## Inspecting the artifact

### Identify the shipped image

Confirm what the package actually installed.

```
file /usr/share/evilmaid/evilmaidusb-1.01.img
```

Outcome: reports the image's real format (a raw bootable disk image with an MBR boot sector), verifying you are looking at a disk image rather than a filesystem archive.

### Verify integrity against the packaging checksum

Check the image is the authentic 1.01 release.

```
sha512sum /usr/share/evilmaid/evilmaidusb-1.01.img
```

Outcome: prints the digest; compare it with the sha512sum pinned in BlackArch's `evilmaid` PKGBUILD, which is the authoritative reference for this release. A match confirms the image is byte-identical to the archived Invisible Things Lab release.
