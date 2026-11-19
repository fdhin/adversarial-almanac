---
name: "firmware-mod-kit"
categories:
  - blackarch-firmware
version: "0.99.r119.gc72f45d"
homepage: "https://github.com/rampageX/firmware-mod-kit"
packaged-by: [blackarch, kali, aur]
blackarch-builddate: "2026-03-04"
build-system: make
license: MIT
meta-checked: "2026-11-09"
github-repo: "rampageX/firmware-mod-kit"
github-stars: 1007
github-pushed: "2026-02-17"
github-archived: false
github-issues: 62
github-match: "exact"
gh-checked: "2026-11-07"
worth: 5.8
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 4
gh-activity-checked: "2026-11-09"
---

# firmware-mod-kit

> Extract a Linux-based firmware image, edit its filesystem or web UI, and rebuild a flashable image — the classic Firmware Modification Kit, originally hosted on Google Code and continued as a GitHub mirror.

*No corpus evidence — derived from upstream documentation.*

## What it's for

A collection of scripts and utilities (originally by Craig Heffner, MIT licensed) to deconstruct and reconstruct firmware images for embedded devices, primarily Linux-based routers using TRX/uImage headers and SquashFS/CramFS filesystems. The workflow is extract → modify → rebuild: `extract-firmware.sh` splits an image into its component parts under a hard-coded `fmk/` working directory, you edit `fmk/rootfs` like any directory tree, and `build-firmware.sh` repacks it into a new flashable image. It is not a firmware identifier — use binwalk for reconnaissance first. On BlackArch the scripts are installed under `/usr/share/firmware-mod-kit/` rather than in `PATH`; from a checkout run them as `./script.sh`. Flashing rebuilt images can brick the device — the upstream README is emphatic about this.

## Typical use cases

### Extract a firmware image into its component parts

```
./extract-firmware.sh firmware.bin
```

Outcome: the image is split into `fmk/` — `fmk/rootfs` holds the unpacked filesystem and `fmk/image_parts` the kernel and header components. Works out of the box with most TRX/uImage-based images using SquashFS or CramFS; the older `old-extract.sh` remains included for formats the main script does not handle.

### Rebuild the firmware after modifying the filesystem

```
./build-firmware.sh
```

Outcome: repacks `fmk/` into `fmk/new-firmware.bin`, ready to flash. Run this after editing anything under `fmk/rootfs`, such as replacing a daemon with a cross-compiled one. `-nopad` skips padding the image back up to its original size; `-min` produces a smaller image via a 1 MB SquashFS block size at the cost of target-device CPU and RAM.

### Modify the DD-WRT web UI

```
./ddwrt-gui-extract.sh
```

Outcome: extracts the compressed Web GUI files from already-extracted DD-WRT firmware into a `www/` directory — edit them (no adding or deleting files), then run `./ddwrt-gui-rebuild.sh` followed by `./build-firmware.sh` to produce the patched image.

### Fix the checksum footer on a modified Linksys image

```
./linksys_footer.sh modified_firmware.img
```

Outcome: recalculates the Linksys custom footer checksum of an already-rebuilt image and writes the corrected file to `modified_checksum.img`, so the vendor updater accepts it.
