---
name: "bios_memimage"
categories:
  - blackarch-cracker
version: "1.2"
homepage: "http://citp.princeton.edu/memory/code/"
packaged-by: [blackarch]
blackarch-builddate: "2018-11-29"
build-system: make
license: BSD
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# bios_memimage

> Princeton cold-boot toolkit: bootable PXE/USB "scraper" images plus host tools that dump a machine's RAM after a restart.

## What it's for

This is the reference code accompanying the Princeton research on DRAM persistence ("cold boot attacks"). It builds two standalone scraper images — a PXE one the target downloads over the network via DHCP/TFTP, and a USB/disk one booted from raw media — which run immediately after a reset and copy physical memory (the 640K of low memory plus extended memory up to about 3.5GB) either to a remote host or onto the boot device itself. The host-side utilities pxedump and usbdump collect or extract the resulting image. It is an acquisition tool, not a cracker: the dump it produces is what you later mine with key-finders such as aeskeyfind or rsakeyfind.

## Typical use cases

### Pull a RAM dump from a PXE-booted target

```
pxedump 198.51.100.10 > memorydump.dat
```

Outcome: handshakes with the scraper the target fetched via PXE and streams its memory blocks into memorydump.dat; when the dump completes the scraper powers the target off (APM) or reboots it.

### Turn a USB stick into a self-contained capture device

```
sudo dd if=/usr/share/bios_memimage/usb/scraper.bin of=/dev/sdX
```

Outcome: writes the USB scraper image to raw media; booting a target from that device dumps its RAM onto the same device, no network required.

### Extract the dump from the capture device

```
usbdump /dev/sdX > memorydump.dat
```

Outcome: reads the dump map from the device, validates its checksum, and writes the raw memory segments to memorydump.dat for offline analysis.

*No corpus evidence — derived from upstream documentation.*
