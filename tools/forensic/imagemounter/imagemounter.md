---
name: "imagemounter"
categories:
  - blackarch-forensic
version: "413.383b30b"
homepage: "https://github.com/ralphje/imagemounter"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-17"
build-system: python
license: MIT
meta-checked: "2026-11-09"
github-repo: "ralphje/imagemounter"
github-stars: 126
github-pushed: "2023-02-09"
github-archived: false
github-issues: 6
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.0
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# imagemounter

> One command to mount a forensic disk image's full stack — container, volume system, nested LVM/RAID and filesystems — and to clean it all up afterwards.

## What it's for

imagemounter (by ralphje) is the `imount` command-line utility and underlying Python package for mounting and unmounting forensic disk images. Given an EnCase/EWF, AFF, VMDK or raw dd image it layers the available helpers (xmount, ewfmount, affuse, vmware-mount), detects the volume system (DOS, BSD, Sun, Mac, GPT), detects and mounts nested LVM volume groups, reconstructs Linux Software RAID arrays, and mounts FAT, NTFS, ext, XFS, UFS, HFS+ and LUKS volumes. In default mode it mounts everything it can on temporary mountpoints and unmounts on exit, so analysis never leaves mounted leftovers. It mounts and structures — parsing file contents remains the job of your analysis tools.

## Typical use cases

### Mount every volume in an image

Hand it an image file and let it work out the stack.

```
imount disk.E01
```

Outcome: each volume found in the image is mounted on its own `/tmp/im_*` mountpoint with filesystem and size reported; press Enter to unmount everything, Ctrl-C to leave it mounted for later cleanup with `imount -u`.

### Check which backing tools are available

Verify the helper ecosystem before a session.

```
imount --check
```

Outcome: a system check listing which external tools (xmount, ewfmount, affuse, mmls and friends) are installed and which are missing, so mount failures during real work can be diagnosed in advance.

### Keep volumes mounted for manual analysis

Mount into a stable directory that survives the command.

```
imount -k -md /mnt/case1 disk.raw
```

Outcome: volumes mount under `/mnt/case1` and stay mounted after the program exits (`-k`); add `--carve` to run carving of unallocated space, `--vshadow` to also mount volume shadow copies.

### Assemble a RAID or reconstruct the full tree

Rebuild multi-device structures before analysis.

```
imount -r disk1.raw disk2.raw disk3.raw
```

Outcome: Linux Software RAID members are reassembled and the full filesystem tree is mounted across all partitions (implies `-s`); useful when a single logical volume was spread over several disks.

*No corpus evidence — derived from upstream documentation.*
