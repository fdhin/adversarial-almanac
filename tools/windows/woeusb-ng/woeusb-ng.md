---
name: "woeusb-ng"
categories:
  - windows
version: "0.2.12-1parrot2"
homepage: "https://github.com/WoeUSB/WoeUSB-ng"
origin: "parrot"
recipe: "parrot-repo"
packaged-by: [parrot, aur, nixpkgs]
build-system: python
meta-checked: "2026-11-09"
github-repo: "WoeUSB/WoeUSB-ng"
github-stars: 2220
github-pushed: "2026-01-25"
github-archived: false
github-issues: 67
github-match: "exact"
gh-checked: "2026-11-07"
worth: 6.0
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 2
gh-activity-checked: "2026-11-09"
---

# woeusb-ng

> Linux utility that turns a Windows ISO or DVD into a bootable Windows USB installer.

## What it's for

WoeUSB-ng (a Python rewrite of WoeUSB) creates bootable Windows installation USB drives from Linux — from a Windows Vista through Windows 10 ISO image or a physical DVD, in legacy/MBR or native-UEFI (FAT-target) boot modes. It is not a security tool: it appears in pentest distributions purely as supporting infrastructure for building and rebuilding Windows lab and analysis machines. It ships two front-ends: the `woeusb` command-line tool (device mode wipes and repartitions the whole USB stick; partition mode writes into an existing partition, keeping other data) and `woeusbgui` for the same operations via a small GUI. Device mode is destructive — double-check the target disk before running it.

## Typical use cases

### Write a Windows ISO to a USB stick (device mode)

The standard bootable-installer flow: whole-disk, wipes the stick.

```
sudo woeusb --device Win10.iso /dev/sdX --tgt-fs NTFS
```

Outcome: partitions and formats the entire target disk, copies the installation media, and installs a bootloader; on completion the stick boots the Windows setup in UEFI (and MBR where supported). `--tgt-fs NTFS` selects the filesystem (FAT is the default; NTFS avoids FAT's 4 GB file limit for large install images).

### Add Windows setup files to an existing partition (partition mode)

Keep the rest of a multi-purpose USB stick and just make one partition bootable.

```
sudo woeusb --partition Win10.iso /dev/sdX1
```

Outcome: copies the ISO's contents onto the existing partition and sets up boot without repartitioning the disk; other partitions and their data remain intact.

*No corpus evidence — derived from upstream documentation (WoeUSB-ng README and the packaged argparse options: `--device/-d`, `--partition/-p`, `--tgt-fs`, `--verbose`).*
