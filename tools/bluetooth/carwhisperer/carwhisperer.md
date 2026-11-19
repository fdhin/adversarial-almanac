---
name: "carwhisperer"
categories:
  - blackarch-bluetooth
version: "0.2"
homepage: "https://trifinite.org/stuff/carwhisperer/"
packaged-by: [blackarch]
blackarch-builddate: "2025-12-12"
build-system: perl
license: GPL-2.0-or-later
meta-checked: "2026-11-09"
github-repo: "yesimxev/carwhisperer-0.2"
github-stars: 13
github-pushed: "2023-01-19"
github-archived: false
github-issues: 0
github-match: "search"
gh-checked: "2026-11-09"
worth: 2.4
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# carwhisperer

> Connects to car kits and headsets that ship with standard pairing passkeys, then injects and records audio over their SCO link.

## What it's for

Demonstrating the risk of factory-default passkeys on Bluetooth hands-free units and car kits that have no display or keypad (trifinite.org, Martin Herfurt, 2005). The `carwhisperer` binary opens an RFCOMM control connection to the target and sets up SCO audio links, then plays a raw 8 kHz mono audio file into the target's speaker while writing everything its microphone picks up to a file. Pairing is answered by the `cw_pin.pl` helper, installed in place of the usual BlueZ PIN helper, which returns the standard passkey (typically `0000` or `1234`) matching the target's vendor OUI. It is a BlueZ 4-era classic Bluetooth tool; it does not attack random-passkey devices, and range matters — the original setup pairs a modified long-range dongle with a directional antenna.

## Typical use cases

### Connect to a headset or car kit and exchange audio

```
sudo carwhisperer 0 message.raw results/01.raw AA:BB:CC:DD:EE:01 1
```

Outcome: arguments are hci device number, raw audio file to play into the target, output file for recorded audio, target BD address, and RFCOMM channel. With `cw_pin.pl` answering the passkey request, the tool pairs, connects SCO, plays `message.raw` into the car and writes the microphone capture to `results/01.raw`.

### Sweep an area for vulnerable devices automatically

```
sudo ./cw_scanner
```

Outcome: the scanner script loops `hcitool -i hci0 inq --flush`, keeps only devices whose class of device matches headsets/hands-free units, and runs `carwhisperer` against each new find on RFCOMM channel 1, storing per-target recordings under `results/` and logs alongside. Run it from a working directory containing the built binary and a `message.raw` (the scripts also ship under `/usr/share/carwhisperer/scripts/`).

### Prepare audio for injection

```
sox -t wav -r 44100 -c 2 announcement.wav -t raw -r 8000 -c 1 -s -w message.raw
```

Outcome: converts any WAV announcement into the 8 kHz, 16-bit, mono raw PCM the tool plays; a captured recording converts back the same way (`sox -t raw -r 8000 -c 1 -s -w results/01.raw -t wav -r 44100 -c 2 out.wav`) so it can be played on a modern machine.

*No corpus evidence — derived from upstream documentation.*

*GitHub: `yesimxev/carwhisperer-0.2` is a third-party source mirror of the packaged 0.2 tarball (identical except a few spelling fixes; upstream is trifinite.org); stat fields predate this link and await recompute.*
