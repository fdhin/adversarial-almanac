---
name: "jtagenum"
categories:
  - hardware
homepage: "https://github.com/cyphunk/JTAGenum"
origin: "hacktricks"
recipe: "upstream"
packaged-by: []
build-system: other
meta-checked: "2026-11-13"
github-repo: "cyphunk/JTAGenum"
github-stars: 806
github-archived: false
github-match: "exact"
gh-checked: "2026-11-13"
worth: 4.7
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-13"
license: "custom:unknown"
---

# jtagenum

> Arduino sketch (and experimental Raspberry Pi shell script) that finds JTAG pinouts on unknown hardware by brute-forcing every pin combination, then enumerates the instruction register for undocumented commands.

## What it's for

Embedded devices frequently expose JTAG test points with no documentation of which pad carries which line. JTAGenum turns a cheap microcontroller (5V classic Arduino, 3.3V Teensy/Tiva/Blue Pill, or a Raspberry Pi via the experimental `JTAGenum.sh`) into a pinout detector: wire as many target pins as you can reach to the scanner's pins, and it brute-forces all combinations to identify TCK, TMS, TDI and TDO, reads IDCODE registers, dumps the boundary-scan register, and brute-forces the instruction register to surface undocumented instructions. It is the software-only alternative to a JTAGulator — at the cost of needing voltage matching (5V Arduino vs 3.3V target) and hand-editing a `pins[]` array before each run. It does not read or write flash itself; once the pinout is known you switch to OpenOCD/urjtag against the discovered lines.

## Typical use cases

### Open the scanner's serial console

```
(minicom -D /dev/ttyUSB0 -b 115200) ; # then send: h
```

Outcome: after flashing `JTAGenum.ino` and wiring the target's candidate pins to the Arduino pins listed in the sketch's `pins[]`/`pinnames[]` arrays, the 115200-baud serial console responds to `h` with the one-character menu (verbose, loopback, scan, IR brute force, boundary scan, idcode, pull-ups) — every function below is invoked by sending its single character.

### Detect loopback pairs that would poison the scan

```
l
```

Outcome: JTAGenum drives the test pattern onto every pin and watches every other pin for an echo; reported loopback pairs (and cross-talking wires) must be removed from `pins[]` before the real scan, otherwise they surface as false JTAG lines — run it twice, with pull-up resistors off and on, to separate real connections from floating-wire noise.

### Find the JTAG pinout

```
r
s
```

Outcome: with internal pull-ups enabled, the scan brute-forces every pin combination while shifting the test pattern through Shift-IR; the console prints which pins act as TCK/TMS/TDI/TDO. If an IDCODE register exists, TMS is trustworthy too; on bypass-default silicon only TCK/TDI/TDO are certain.

### Read a chip's IDCODE

```
i
```

Outcome: iterates all TCK/TDO pin pairs and prints any output that changes state — a stable non-trivial bit pattern is the JTAG IDCODE, to be checked against the vendor's part documentation to confirm you are talking to the chip you think you are.

### Hunt undocumented instructions and dump the boundary register

```
y
x
```

Outcome: with `pins[]` redefined as [0]=TCK [1]=TMS [2]=TDO [3]=TDI and `IR_LENGTH` set, `y` steps the instruction register through every value and reports what each one drives onto the data register (undocumented test/debug instructions), while `x` shifts out the boundary-scan register (default length 1800 bits) showing the live logic level of the target's pins.

### Scan from a Raspberry Pi instead

```
source JTAGenum.sh
scan
scan_idcode
```

Outcome: the experimental shell version bit-bangs the BCM GPIO pins defined at the top of the script (no Arduino needed, 3.3V logic); `DEBUG=1 scan` exercises the logic with no Pi attached, `VERBOSE=1 scan` adds detail — upstream notes results on the Pi are mixed, so treat findings as leads to confirm with the Arduino build.

*No corpus evidence — derived from upstream documentation.*
