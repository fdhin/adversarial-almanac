---
name: "rfidler"
gentoo-category: "net-wireless"
version: "0.01_p20150718"
homepage: "https://github.com/ApertureLabsLtd/RFIDler"
origin: "pentoo"
recipe: "pentoo-ebuild"
packaged-by: [pentoo]
build-system: python
license: GPL-2
meta-checked: "2026-11-09"
github-repo: "ApertureLabsLtd/RFIDler"
github-stars: 489
github-pushed: "2024-09-22"
github-archived: false
github-issues: 16
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.3
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# rfidler

> Open-source hardware LF RFID reader/writer/emulator (PIC32-based, from Aperture Labs) with a serial CLI — a software-defined RFID workbench for 125 kHz tags.

## What it's for

The RFIDler is a physical device, not just a program: a Microchip PIC32 board with an LF (125 kHz) coil that can act as reader, writer, sniffer and tag emulator simultaneously, exposing a text command interface over USB-serial CDC at 115200 baud (or 3.3V UART pins). Because its modulation, clock, rate and encoding parameters are all software-settable (`SET MOD`, `SET FC`, `SET RATE`, Manchester/BiPhase...), it works with arbitrary LF tag formats instead of a fixed few — HID Prox, Indala, EM4x02, FDX-B, Q5/T5557, Hitag2 and more, including raw coil sampling (`ANALOGUE`, `PLOT`), clock detection, PWM sniffing for Hitag2 cracking, and UID-only one-shot emulation. The repository contains the firmware source (build in MPLAB X), the bootloader flow for flashing, and a Python wrapper (`python/rfidler.py`) that sends the same commands programmatically. It cannot do HF/13.56 MHz NFC — that is a different device class.

## Typical use cases

### Open the device CLI and see what it can do

Attach the board and get an interactive shell.

```
minicom -D /dev/ttyACM0 -b 115200
```

Outcome: connects to the RFIDler's console — press ENTER once for the full HELP command listing (switching it to human CLI mode); without the ENTER you stay in API mode where every command is answered `.` on success, `!` on failure, `?` if unrecognized, which is the mode scripts want.

### Identify and read a tag from the shell

Auto-detect the format, then dump blocks.

```
./rfidler.py /dev/ttyACM0 AUTOTAG "READ 0"
```

Outcome: the Python wrapper sends the commands sequentially — `AUTOTAG` sweeps the known tag types and reports which matched the tag on the coil, then `READ 0` reads block 0 into the device's virtual tag buffer and returns it; unrecognized commands pass straight through to the device, quoted so their arguments survive.

### Clone one tag's identity onto another

Copy a read tag to a writable target of a different type.

```
./rfidler.py /dev/ttyACM0 "SET TAG EM4X02" COPY "SET VTAG Q5" CONVERT Q5 CLONE
```

Outcome: sets the source format, `COPY` reads the physical tag into the virtual tag, `SET VTAG`/`CONVERT` re-encode it for the Q5 (T5557) target, and `CLONE` writes the virtual tag to the card now placed on the coil — the standard workflow for moving a UID between incompatible LF formats.

### Watch reader/tag traffic and emulate

Sniff a live transaction or stand in for a card.

```
./rfidler.py /dev/ttyACM0 SNIFFER
```

Outcome: puts the coil in sniffer mode, continuously reporting UIDs as readers interrogate tags nearby; `READER` is the mirror mode (continuously acquiring tag UIDs as a reader would), and `EMU <uid>`/`EMULATOR` replay a configured UID or the whole virtual tag to present yourself as the card.

*No corpus evidence — derived from upstream documentation.*
