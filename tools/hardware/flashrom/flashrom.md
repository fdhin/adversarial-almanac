---
name: "flashrom"
categories:
  - hardware
version: "1.8.0-1"
homepage: "https://www.flashrom.org/"
origin: "arch"
arch-repo: "extra"
packaged-by: [arch, kali, nixpkgs]
arch-builddate: "2026-08-27"
build-system: other
meta-checked: "2026-11-09"
github-repo: "flashrom/flashrom"
github-stars: 1190
github-pushed: "2026-11-05"
github-archived: false
github-issues: 80
github-match: "search"
gh-checked: "2026-11-07"
worth: 8.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 294
gh-activity-checked: "2026-11-09"
---

# flashrom

> Identifies, reads, writes, erases and verifies SPI/LPC/FW/parallel flash chips — the standard tool for BIOS/UEFI, coreboot and firmware chip work, in-system or with an external programmer.

## What it's for

Getting firmware in and out of flash chips. It drives hundreds of chips through dozens of programmers: the host chipset's own SPI controller (`internal`), cheap USB SPI adapters (`ch341a_spi`, `ft2232_spi`), the Bus Pirate (`buspirate_spi`), Raspberry Pi GPIO (`linux_spi`), network cards, and more. Typical jobs: dump a BIOS chip for offline analysis, flash a coreboot/UEFI image, unlock or check write-protection, or recover a bricked board with an external clip. It works in-system (dangerous on laptops — the embedded controller may react badly, and a bad write can brick the board), so always read a backup before writing. It is a chip-level tool: it moves bytes, it does not parse firmware layouts (that is what UEFITool/binwalk are for), though it can act on firmware regions via layout files.

## Typical use cases

### Probe and identify the chip

```
sudo flashrom -p ch341a_spi
```

Outcome: flashrom initialises the CH341A programmer, probes the chip and prints what it found (e.g. "Found Winbond flash chip 'W25Q128.V' (16384 kB)") plus the detected sections. Run with no operation flag it stops here — a safe first contact that changes nothing. `-L` lists all supported programmers and chips offline.

### Back up the current firmware image

```
sudo flashrom -p ch341a_spi -r bios_backup.bin
```

Outcome: the full chip contents are read into `bios_backup.bin` and automatically verified against the chip after reading. Keep this file — it is the recovery image if anything later goes wrong.

### Write and verify a new firmware image

```
sudo flashrom -p ch341a_spi -w coreboot.rom
```

Outcome: the image is erased-programmed into the chip and then verified automatically (disable with `-n`). A mismatch aborts with the offending addresses; `-f` forces the operation past flashrom's sanity checks when you know better than the chip database.

### Read the chip through the running system's own SPI controller

```
sudo flashrom -p internal --flash-name
sudo flashrom -p internal -r host_fw_backup.bin
```

Outcome: `--flash-name` reports the chipset-attached chip's name and size without touching it; the second command reads it from inside the running OS — the fastest path on servers/desktops where the firmware is not write-protected, and the first step when auditing or dumping a machine's own BIOS.

### Flash only one region defined by the firmware layout

Write just the BIOS region of an Intel descriptor-mapped chip instead of the whole 16 MB:

```
sudo flashrom -p ch341a_spi -l layout.txt -i bios -w bios_region.bin
```

Outcome: only the region named `bios` in the layout file is written (Intel images can instead use `--ifd` to take the layout from the chip's firmware descriptor). This avoids rewriting the descriptor and ME regions, which is how bricks happen.

*No corpus evidence — derived from upstream documentation.*
