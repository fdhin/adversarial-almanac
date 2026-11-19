---
tool: "jtagenum"
build-system: "other"
upstream: "https://github.com/cyphunk/JTAGenum"
recipe: "upstream"
license: "custom:unknown"
---

# Installing jtagenum

> Arduino sketch / Raspberry Pi script to discover JTAG pinouts and enumerate the instruction register on unknown hardware.

Source: HackTricks feed admission; upstream [`cyphunk/JTAGenum`](https://github.com/cyphunk/JTAGenum).

## Arduino (primary platform)

```bash
git clone https://github.com/cyphunk/JTAGenum
```

1. Open `JTAGenum.ino` in the Arduino IDE.
2. Edit the `pins[]` and `pinnames[]` arrays at the top to list the Arduino
   pins you wired to the target's test points.
3. Flash the sketch, then open a serial console at **115200 baud** and send
   `h` for the command menu.

Tested platforms: classic Arduino (5V), Teensy (3.3V), TI Tiva C/Stellaris
(3.3V), STM32 Blue Pill (3.3V). Match the target's I/O voltage — use a 3.3V
board or a level shifter when scanning 3.3V logic with a 5V Arduino.

## Raspberry Pi (experimental)

```bash
git clone https://github.com/cyphunk/JTAGenum
source JTAGenum.sh
scan
```

Edit the BCM pin definitions at the top of the script first. Results are
mixed per upstream; prefer the Arduino build for confirmation.

## Not packaged — and not a normal package

JTAGenum ships as source you flash to a microcontroller, so no distro
packages it. Checked 2026-11-13.

Backlinks: [[hardware]] · [[jtagenum]]
