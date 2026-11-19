---
name: "lvm2"
categories:
  - forensics
version: "2.03.44-1"
homepage: "https://sourceware.org/lvm2/"
origin: "arch"
arch-repo: "core"
packaged-by: [arch, kali, nixpkgs]
arch-builddate: "2026-10-28"
build-system: autotools
license: GPL-2.0-only, LGPL-2.1-only
meta-checked: "2026-11-09"
github-repo: "lvmteam/lvm2"
github-stars: 154
github-pushed: "2026-10-09"
github-archived: false
github-issues: 43
github-match: "search"
gh-checked: "2026-11-07"
worth: 7.7
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 1653
gh-activity-checked: "2026-11-09"
---

# lvm2

> The Linux Logical Volume Manager toolset — pv/vg/lv commands, reused in forensics to examine, mount and repair LVM evidence.

## What it's for

LVM2 is the standard Linux userspace toolset (with device-mapper) for logical volume management — the `pv*`, `vg*` and `lv*` commands shipped by every major distribution. It matters to forensics whenever an acquired disk uses LVM: the reporting commands read the volume group layout straight off the evidence, activation exposes logical volumes as device-mapper devices you can mount read-only, `vgimportclone` lets a duplicated disk's volume group coexist with your own, and `vgcfgrestore` reads or reinstalls LVM's own archived metadata. It is a system administrator toolset, not a forensic one: the reporting commands are read-only, but activation updates host-side caches, and `vgimportclone` and `vgcfgrestore` write metadata to the disk — keep originals write-blocked and repair working copies.

## Typical use cases

### Find LVM volumes in a disk image

Attach an image and see whether it contains LVM physical volumes.

```
losetup --find --partscan evidence.dd
pvs -a
```

Outcome: the image is attached as a loop device with its partitions exposed, and `pvs -a` lists every LVM physical volume found on them — device, volume group name and size — identifying the loop devices that carry them.

### Read the volume layout without changes

Inspect the evidence read-only.

```
pvs --readonly
vgs --readonly
lvs --readonly
```

Outcome: prints the physical-volume, volume-group and logical-volume hierarchy (names, sizes, attributes) under locks that make no changes to the disks or to LVM state — the safe first look at an LVM disk.

### Activate a volume group to mount its logical volumes

Get filesystem access to logical volumes on the evidence.

```
vgchange -ay vg0
mount -o ro /dev/vg0/root /mnt/evidence
```

Outcome: device-mapper devices appear for each logical volume under `/dev/mapper` and the chosen one mounts read-only for examination; activation does not rewrite volume data, but host-side LVM caches do update, so attach the evidence write-blocked or work from a duplicate.

### Import a duplicated disk without name clashes

Work on a clone whose volume group name collides with yours.

```
vgimportclone --basevgname evidence_vg /dev/sdb
```

Outcome: renames the cloned disk's volume group (and its volumes) to `evidence_vg` so it can be activated alongside the local groups — note that this writes metadata to the cloned disk, so run it on a working copy, never on original evidence.

### Repair or review volume group metadata

Use LVM's own metadata backups.

```
vgcfgrestore --list vg0
vgcfgrestore -f /etc/lvm/archive/vg0_00002-1234567890.vg vg0
```

Outcome: the first command lists the archived metadata files for the group with their dates and UUIDs — read them diagnostically to see the layout LVM last recorded; the second reinstalls the chosen archive onto the physical volumes, restoring a group whose metadata area is damaged (a write operation — restore onto working copies).

*No corpus evidence — derived from upstream documentation.*
