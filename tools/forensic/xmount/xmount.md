---
name: "xmount"
categories:
  - forensics
version: "1.3.1+ds-1"
homepage: "https://www.sits.lu/xmount"
origin: "kali"
recipe: "kali-dsc"
packaged-by: [kali, aur]
build-system: cmake
meta-checked: "2026-11-09"
github-repo: "mika/xmount"
github-stars: 9
github-pushed: "2013-07-23"
github-archived: false
github-issues: 0
github-match: "search"
gh-checked: "2026-11-07"
worth: 2.0
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---
# xmount

> FUSE layer that presents forensic disk images (EWF, AFF, QCOW2, raw, ...) on the fly as raw, VDI, VMDK, VHD or DMG virtual disks.

## What it's for

xmount (written by Gillen Daniel, now maintained by his company SITS Sarl; GPLv3+) removes the slow convert-then-use step in disk forensics: it FUSE-mounts an input image — raw/DD, EWF/E01, AAEWF, AFF, AFF4, QCOW or VDI — and exposes it inside the mount point as a virtual disk in another format (raw, DMG, VHD, VDI, VMDK), computed on the fly. That lets an E01 acquisition be attached to QEMU/KVM/VirtualBox/VMware directly. Virtual write support redirects every change to a separate cache file so the evidence itself is never modified, and morphing functions can combine multiple input images into one, reconstruct RAID0, or expose only unallocated sectors of FAT/HFS images. Split input segments must all be passed — a shell glob like `E??` covers EWF segment files.

## Typical use cases

### Present an E01 acquisition as a raw disk

Get a raw DD view of an EWF image without ewfexport.

```
xmount --in aewf ./acquired_disk.E?? /mnt/xmount
```

Outcome: `/mnt/xmount` contains `acquired_disk.dd`, a virtual raw image served read directly out of the (possibly multi-segment, globbed) EWF files and usable with any raw-image tool.

### Expose an image as a virtual disk for a VM

Attach evidence to a hypervisor without conversion.

```
xmount --in aewf ./acquired_disk.E?? --out vdi /mnt/xmount
```

Outcome: the mount point holds `acquired_disk.vdi`, which VirtualBox attaches as a disk; `--out vmdk`, `--out vhd` and `--out dmg` serve VMware, Hyper-V-style VHD and DMG consumers the same way.

### Boot an acquired image with writes — evidence untouched

Let a VM modify a working copy while the original stays pristine.

```
xmount --in raw ./acquired_disk.dd --out vdi --cache ./disk.cache /mnt/xmount
```

Outcome: all writes the VM makes land in `disk.cache`; the input image is never altered, and the same cache file can be reused across output formats — delete it to return to a pristine state.

### Merge segments, reconstruct RAID, or view unallocated space

Reshape the input before presenting it.

```
xmount --in raw ./part0.dd ./part1.dd --out raw --morph combine /mnt/xmount
```

Outcome: `--morph combine` stitches both segments into one contiguous virtual image (`--morph raid` reconstructs a RAID0, `--morph unallocated` exposes only unallocated sectors of a FAT/HFS image).

*No corpus evidence — derived from upstream documentation.*
