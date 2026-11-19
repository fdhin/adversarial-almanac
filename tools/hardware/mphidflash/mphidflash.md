---
name: "mphidflash"
gentoo-category: "dev-embedded"
version: "1.6"
homepage: "https://github.com/AdamLaurie/mphidflash"
origin: "pentoo"
recipe: "pentoo-ebuild"
packaged-by: [pentoo, aur]
build-system: make
license: GPL-3
meta-checked: "2026-11-09"
github-repo: "AdamLaurie/mphidflash"
github-stars: 35
github-pushed: "2023-07-24"
github-archived: false
github-issues: 8
github-match: "search"
gh-checked: "2026-11-07"
worth: 2.8
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# mphidflash

> Flashes firmware over the Microchip USB HID bootloader found on PIC16/PIC18/PIC24 (and PIC32 ports such as UBW32) — no programmer hardware needed beyond a USB cable.

## What it's for

Reprogramming PIC microcontrollers whose firmware contains the Microchip USB HID Bootloader (from the Microchip USB Library — not the MPLAB Harmony/AN1388 PIC32 HID bootloader, which speaks a different protocol despite sharing the same default product ID). The board enumerates as a USB HID device (default VID 0x04D8, PID 0x003C) and this tool erases, writes and verifies Intel HEX images over that interface, optionally resetting the board afterwards so the new firmware starts. Reach for it when developing on bootloader-equipped PIC boards: it replaces a PICkit/ICSP programmer for routine iteration, and it also unlocks configuration memory before writing when the bootloader requires it. It is a single-purpose flasher — no debugging, and nothing for non-Microchip bootloaders.

## Typical use cases

### Flash a firmware image and reset the board

Put the board in bootloader mode (usually by holding a button at power-up), then:

```
mphidflash -r -w firmware.hex
```

Outcome: the tool finds the HID bootloader device, erases program memory, writes `firmware.hex`, verifies the write (verification is automatic unless `-n` is given), and `-r` resets the device so the freshly written application starts immediately.

### Target a board with non-default USB IDs

When the bootloader was built with custom VID/PID values:

```
mphidflash -v 0x1234 -p 0x0001 -w firmware.hex
```

Outcome: the device search uses the given vendor and product IDs instead of the Microchip defaults, so the write proceeds against boards whose descriptor was customised — the same erase/write/verify sequence as the default case.

### Erase only, or unlock configuration memory

```
mphidflash -e
mphidflash -u -w firmware.hex
```

Outcome: `-e` alone erases the code space and exits; adding `-u` unlocks configuration memory before the erase/write so configuration words in the HEX file are programmed too (required by some bootloader versions — `-s` additionally signs the flash where the bootloader demands it).

*No corpus evidence — derived from upstream documentation.*
