---
name: "proxmark3"
categories:
  - rfid
version: "4.20142-2"
homepage: "https://github.com/RfidResearchGroup/proxmark3"
origin: "arch"
arch-repo: "extra"
packaged-by: [arch, kali, nixpkgs]
arch-builddate: "2025-12-14"
build-system: make
license: GPL2
meta-checked: "2026-11-09"
github-repo: "RfidResearchGroup/proxmark3"
github-stars: 6213
github-pushed: "2026-11-07"
github-archived: false
github-issues: 48
github-match: "exact"
gh-checked: "2026-11-07"
worth: 8.7
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 2776
gh-activity-checked: "2026-11-09"
---

# proxmark3

> Firmware, flasher, and interactive client for the Proxmark3 RFID/NFC research device (RfidResearchGroup "Iceman" fork).

## What it's for

The Proxmark3, originally built by Jonathan Westhues, is the standard open-hardware instrument for RFID security research: it can sniff, read, emulate and clone tags across low frequency (125/134 kHz) and high frequency (13.56 MHz) ranges. This is the RfidResearchGroup "Iceman" repository — the actively maintained edition of the client and firmware, with the broadest command set plus Lua/Python scripting and standalone modes. All work happens in the interactive client console (the `pm3 -->` prompt) attached to the device over USB; typical targets include MIFARE Classic/Ultralight, iCLASS, HID Prox, Indala, T55xx, Hitag and ISO7816 smart cards. It cannot do anything without the physical device attached. The full command set ships in the repository's `doc/commands.md`, with a condensed cheat sheet in `doc/cheatsheet.md`.

## Typical use cases

### Connect the client to the device

```
proxmark3 /dev/ttyACM0
```

Outcome: opens the interactive console bound to the Proxmark3 on that port; `hw version` and `hw tune` inside the console confirm the firmware is alive and show antenna tuning (LF/HF voltage should read roughly 20-45+ V).

### Identify an unknown card

```
hf search
lf search
```

Outcome: the client interrogates the tag and reports which HF (13.56 MHz) or LF (125 kHz) technology it is — the first command to run against any unknown card, since every later step depends on the tag type.

### Read MIFARE Classic tag details

```
hf mf info
```

Outcome: prints tag information (type, UID, memory layout) and reports whether the card is a "magic" card with writeable UID — which decides whether it can be cloned onto directly.

### Recover all keys and dump a MIFARE Classic card

```
hf mf autopwn --1k -f mfc_default_keys
```

Outcome: runs key recovery against the default-key dictionary, extracts every sector key, and writes dump and key files (`hf-mf-<UID>-dump.bin`, `hf-mf-<UID>-key.bin`) ready to feed into `hf mf eload`.

### Emulate a dumped MIFARE card

```
hf mf eload -f hf-mf-A29558E4-dump.bin
hf mf sim -u a29558e4
```

Outcome: loads the dump into the device's emulator memory, then answers real readers as that card, UID included — the Proxmark3 acts as the cloned tag without any physical card.

### Read and clone a low-frequency HID Prox card

```
lf hid read
lf hid clone -r 200670012d
```

Outcome: decodes the card's Wiegand payload from the RF field, then writes that raw value to a writable T55xx tag to produce a physical clone.

### Convert facility and card codes to raw Wiegand data

```
wiegand encode -w H10301 --fc 101 --cn 1337
```

Outcome: prints the raw Wiegand hex for the given facility code and card number, usable directly with the `lf hid sim`/`lf hid clone` commands; `wiegand decode --raw 2006f623ae` performs the inverse.

*No corpus evidence — derived from upstream documentation.*
