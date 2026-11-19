---
name: "gparted"
categories:
  - forensics
version: "1.8.1-2"
homepage: "https://gparted.org/"
origin: "arch"
arch-repo: "extra"
packaged-by: [arch, kali, nixpkgs]
arch-builddate: "2026-04-27"
build-system: autotools
license: GPL-2.0-or-later, GFDL-1.2-or-later
meta-checked: "2026-11-09"
github-repo: "GNOME/gparted"
github-stars: 386
github-pushed: "2026-10-29"
github-archived: false
github-issues: 0
github-match: "search"
gh-checked: "2026-11-07"
worth: 7.9
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 365
gh-activity-checked: "2026-11-09"
---

# gparted

> GNOME Partition Editor: a GUI front end to libparted for creating, resizing, moving, copying and deleting partitions.

## What it's for

GParted is the GNOME Partition Editor — a graphical application for creating, reorganizing and deleting disk partitions while preserving their contents. It supports create, delete, resize, move, check, label and copy operations on partitions and the filesystems inside them, and works on hard disks, USB flash drives and memory cards alike; typical jobs are making space for another operating system, reorganizing disk usage, and imaging a partition by copying it. The workflow is entirely graphical: pick a device, queue operations, review the pending changes, then apply them as one transaction. The `gparted` command itself is only a launcher — a wrapper script that gains root privileges (via pkexec, gksudo, sudo, run0 or similar), temporarily inhibits automounting and device activation, refuses to start a second instance, and then runs the real binary `gpartedbin`; the documented command-line surface is just optional device names. It reorganizes existing structure — it does not search for lost partitions by signature (that is gpart/testdisk territory).

## Typical use cases

### Launch the partition editor

Start the GUI with all detected devices.

```
gparted
```

Outcome: prints the GParted version, the linked libparted version and enabled features to the terminal, then prompts for root authorization (when not already root) and opens the graphical editor with the first detected disk selected; all further operations happen in the GUI.

### Open the editor restricted to specific devices

Limit the device list so the wrong disk cannot be touched.

```
gparted /dev/sdb /dev/sdc
```

Outcome: the editor's device dropdown contains only the named disks — the one form of argument the launcher accepts beyond plain invocation, per the `gparted [device]...` synopsis in the manual page.

*No corpus evidence — derived from upstream documentation.*
