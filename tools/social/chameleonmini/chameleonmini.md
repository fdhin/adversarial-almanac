---
name: "chameleonmini"
categories:
  - blackarch-social
version: "613.ad0eece"
homepage: "https://github.com/emsec/ChameleonMini"
packaged-by: [blackarch]
blackarch-builddate: "2024-04-28"
build-system: make
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "emsec/ChameleonMini"
github-stars: 1877
github-pushed: "2023-08-29"
github-archived: false
github-issues: 75
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.7
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# chameleonmini

> Open NFC-security hardware (Rev G) plus firmware and host tooling: emulates and clones contactless cards, reads tags, and sniffs ISO14443A traffic.

## What it's for

A pocket-sized programmable NFC device for contactless-card security analysis. A slot can be configured to act as a MIFARE Ultralight or MIFARE Classic 1k/4k card (4-byte or 7-byte UID) with a chosen UID and an uploaded memory dump, sniff reader-card ISO14443A exchanges, or work as a reader itself. The device exposes a simple text command line over USB serial: `<COMMAND>=<VALUE>` sets a parameter, `<COMMAND>?` reads it, `<COMMAND>=?` lists valid values (e.g. `CONFIG=?`, `CONFIG=MF_CLASSIC_1K`), and every reply carries a numeric status (`100:OK`, `200:UNKNOWN COMMAND`, ...). The bundled ChamTool Python script wraps that console for scripting; memory dumps move over XMODEM. Official support covers the Rev G hardware from Kasper & Oswald — many third-party clones exist and are unsupported. Firmware flashing is covered by the upstream Getting Started documentation. Only use against cards and systems you are authorized to test.

## Typical use cases

### Configure a slot and set the emulated UID

```
chamtool -p /dev/ttyACM0 -i -c MF_CLASSIC_1K -U 04AABBCC112233
```

Outcome: connects to the device on the given serial port, prints firmware version info, switches the active slot to MIFARE Classic 1k emulation and sets its UID to the given hex value — the device now answers readers as that card. Commands run in the order given; the packaged script is `chamtool`, wrapping `Software/ChamTool/chamtool.py`.

### Upload a card dump so the device clones it

```
chamtool -p /dev/ttyACM0 -u card_dump.bin
```

Outcome: transfers the binary dump into the active slot's memory over XMODEM; after this the slot emulates the original card's contents, not just its UID.

### Download a slot's memory for offline analysis

```
chamtool -p /dev/ttyACM0 -d card_dump.bin
```

Outcome: pulls the active slot's memory image to `card_dump.bin` for inspection or archival, and `-l log.txt` fetches the device's RF sniff log the same way.

### Interrogate a card in the field

```
chamtool -p /dev/ttyACM0 -gu -D
```

Outcome: reads the UID of a card currently in range (`-gu`) and dumps the information the reader function can extract from it (`-D`, MIFARE Ultralight dump) — useful for inventorying badges before any clone attempt.

*No corpus evidence — derived from upstream documentation.*
