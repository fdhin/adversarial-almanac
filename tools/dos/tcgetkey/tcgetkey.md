---
name: "tcgetkey"
categories:
  - blackarch-dos
version: "0.1"
homepage: "http://packetstormsecurity.com/files/119146/tcgetkey.1.html"
packaged-by: [blackarch]
blackarch-builddate: "2023-03-01"
build-system: make
meta-checked: "2026-11-09"
github-repo: "Seabreg/tcgetkey"
github-stars: 0
github-pushed: "2019-04-12"
github-archived: false
github-issues: 0
github-match: "search"
gh-checked: "2026-11-07"
worth: 1.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# tcgetkey

> Recovers TrueCrypt keys from FireWire-acquired Linux memory dumps and decrypts the volume with them.

## What it's for

Despite being filed under DoS packaging, this is a physical-memory forensics
proof of concept by Milen Rangelov: it demonstrates that a running TrueCrypt
setup leaks its keys into RAM, and that those keys are recoverable over the
FireWire bus. It ships two cooperating programs — `get_mem_dump` (acquires a
physical memory image via FireWire using libforensic1394) and `tcgetkey`
(scans the dump for candidate keys using Shannon-entropy analysis, then
decrypts a TrueCrypt container with the recovered keys). It works only
against little-endian Linux memory images and does not support hidden
volumes. For authorized research on your own machines only.

## Typical use cases

### Acquire a memory image over FireWire

Grab physical memory from the target host through its FireWire port.

```
sudo get_mem_dump memory_dump.image
```

Outcome: writes a raw physical memory image to `memory_dump.image` in the
current directory; the file size reflects the target's installed RAM.

### Extract TrueCrypt keys and decrypt the container

Scan the dump for master keys and decrypt the seized volume.

```
tcgetkey memory_dump.image tc.image decrypted.image
```

Outcome: entropy-scans `memory_dump.image` for TrueCrypt key material; if
keys are found it decrypts `tc.image` into `decrypted.image` and prints
follow-up mounting instructions. If no keys match, it reports failure and
produces no usable output image.

### Mount the decrypted volume

Loop-mount the decrypted image to inspect the filesystem.

```
sudo losetup /dev/loop0 decrypted.image && sudo mount /dev/loop0 /mnt/decrypted
```

Outcome: attaches the decrypted image to a loop device and mounts it at
/mnt/decrypted, exposing the recovered volume's files.

*No corpus evidence — derived from upstream documentation.*
