---
name: "parted"
categories:
  - forensics
version: "3.8-1"
homepage: "https://www.gnu.org/software/parted/parted.html"
origin: "arch"
arch-repo: "extra"
packaged-by: [arch, kali, nixpkgs]
arch-builddate: "2026-09-24"
build-system: autotools
license: GPL-3.0-or-later
meta-checked: "2026-11-09"
github-repo: "gitGNU/gnu_parted"
github-stars: 0
github-pushed: "2017-05-05"
github-archived: false
github-issues: 0
github-match: "search"
gh-checked: "2026-11-09"
worth: 1.5
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# parted

> GNU Parted — inspect and edit disk partition tables; forensically, its read-only commands map exactly what sits where before you image, carve or compute offsets.

## What it's for

GNU Parted creates, resizes, moves and deletes partitions on block devices, interactively or scripted (`-s`), with output in human, machine-parseable (`-m`) or JSON (`-j`) form. In forensic work the value is the read-only half: enumerating the partition layout of every attached disk (`-l`), and printing a device's table in exact byte (`unit B`) or sector (`unit s`) units so the start of each filesystem is known for imaging ranges and carving tools. `rescue` searches a range for a partition whose table entry is lost and restores the entry if it finds one — a recovery write, for working copies, not originals. Every mutating command (`mklabel`, `mkpart`, `rm`, `resizepart`, `set`, `toggle`) writes to the device, so against evidence media keep strictly to `print`/`list` or work on a write-blocked copy. The call shape is `parted [options] DEVICE COMMAND [PARAMETERS]`, e.g. `parted /dev/sdb unit B print`.

## Typical use cases

### Inventory all attached disks

First look at what is connected.

```
parted -l
```

Outcome: for every block device, its model, total size in bytes and sectors, the disklabel type, and the partition table (number, start, end, size, type, flags) — the triage view of a suspect disk or an image presented as a loop device.

### Get exact byte offsets for imaging and carving

```
parted -s /dev/sdb unit B print
```

Outcome: the partition table with start, end and size expressed in bytes rather than rounded units — feed those start offsets to [[ddrescue]]/[[dc3dd]] ranges or to a carving tool that wants raw offsets into the image.

### Work in sector units

```
parted -s /dev/sdb unit s print
```

Outcome: starts and ends in 512-byte sectors, the unit most forensic tools and documentation assume; sector arithmetic against other tools' output becomes exact.

### Machine-readable layout for scripts

```
parted -j /dev/sdb print
```

Outcome: the same layout as a JSON object (disks, partitions, offsets) for scripted triage pipelines; `-m` gives the compact colon-separated machine format instead.

### Rescue a lost partition entry — on a copy

```
parted -s /dev/sdc rescue 1MiB 100GiB
```

Outcome: parted scans between the bounds for a filesystem whose partition entry is missing and re-writes the entry into the table when found; this modifies the disk, so run it against a copy of the image, and re-extract the recovered filesystem afterwards.

*No corpus evidence — derived from upstream documentation.*

GitHub: gitGNU/gnu_parted is a third-party mirror of the Savannah (sv.gnu.org) parted git repository (last synced 2017; the packaged release is 3.8 from GNU ftp).
