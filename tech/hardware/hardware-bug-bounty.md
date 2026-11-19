---
technology: "Hardware attack surface (area)"
kind: bug-bounty-methodology
audience: ai-agent
status: upstream-only
family: hardware
version: "1.0"
provenance: "upstream-only (pentest-book + primary sources); zero corpus evidence by design — owner-approved new-unit wave (PB-INGEST) 2026-11-13; command syntax grounded against upstream tooling during the wave: Proxmark3 command families per the RfidResearchGroup/proxmark3 repo (GPL-3.0 Iceman fork — bleeding-edge tree, expect command drift; the repo's own docs say so), OpenOCD/flashrom/binwalk per their docs, KillerBee per its repo. Zero CVEs by design"
source: "six2dez/pentest-book (others/hardware.md, no license — rewritten with attribution), accessed 2026-11-13"
---

# Hardware bench methodology

Companion overview / router: [[hardware]].

Scope: from a powered-off device on the bench (or a badge in hand) to its firmware image, its root console, or a working clone — the method shared by every device family the router dispatches to. Decision inputs per phase: is the header live, is the dump complete, is the credential real.

## Phase 1 — Board recon

Photograph both sides before touching anything (you will need the reference later). Then read the board:

- Unpopulated header pads and through-holes in rows of 4-6 (debug headers left by development).
- Chip markings: the 8-pin SOIC near the main SoC is usually SPI NOR flash (W25Qxx, MX25Lxx families); bigger BGA/QFP parts identify the SoC and its reference manual.
- Test points labeled TX/RX/Console/GND — a printed confession.
- Multimeter continuity from pads to a known ground (shield/USB shell) to build the pin map before anything is powered.

## Phase 2 — UART hunt

The cheapest win on any board: a boot console that was never disabled.

1. Identify pins with the meter: GND (continuity to ground), VCC (steady 3.3 V or 5 V), TX (fluctuates during boot), RX (idle, drops when the device echoes).
2. Connect a USB-TTL adapter (FTDI/CP2102 class) — device TX to adapter RX, common ground, NEVER backfeed VCC unless powering the board from it.
3. Attach at common baud rates — 115200 first, then 9600/19200/38400/57600:

```bash
screen /dev/ttyUSB0 115200
# or: minicom -D /dev/ttyUSB0 -b 115200
```

4. Read the boot log: U-Boot interrupting on a keypress is a bootloader shell (`printenv`/`bootcmd` manipulation, boot args to `init=/bin/sh`); a login prompt on the console is often root or a vendor default that firmware analysis (Phase 5) will confirm. An automated JTAGulator does this hunt for you across unknown headers.

## Phase 3 — JTAG / SWD debug

When there is no console, the debug port reads the CPU directly. Pin hunt: TDI/TDO/TMS/TCK (plus optional TRST) via JTAGulator or Bus Pirate; ARM boards increasingly expose 2-pin SWD instead. Tools: FT2232H breakout, J-Link, OpenOCD:

```bash
openocd -f interface/jlink.cfg -f target/stm32f1x.cfg
# then in arm-none-eabi-gdb:
#   target remote :3333
#   monitor reset halt
#   dump_image firmware.bin 0x08000000 0x10000
```

Halt-and-dump bypasses the firmware's permission model entirely — you are reading memory as the debugger, not as a user. Flash write/unlock takes the same path when in-scope.

## Phase 4 — SPI flash extraction

Skip the CPU entirely and read the chip holding the image. Clip a programmer (CH341A with a SOIC-8 test clip, Bus Pirate, or a Raspberry Pi GPIO header) to the powered-down chip:

```bash
flashrom -p ch341a_spi -r firmware_dump.bin
flashrom -p ch341a_spi -v firmware_dump.bin     # verify before trusting the dump
# Bus Pirate variant:
flashrom -p buspirate_spi:dev=/dev/ttyUSB0 -r firmware.bin
```

Read the chip markings first; a dump that verifies clean is the complete ground truth — firmware AND its config/state.

## Phase 5 — Firmware analysis

```bash
binwalk -e firmware_dump.bin          # carve signatures
binwalk -Me firmware_dump.bin         # recursive (nested compressed filesystems)
cd *_firmware_dump.bin.extracted/squashfs-root/ 2>/dev/null

grep -ri "password\|secret\|apikey\|token" . | head -50
cat etc/passwd etc/shadow 2>/dev/null
find . -name "*.conf" -exec grep -l -i "pass" {} \;
find . -name "*.pem" -o -name "*.key" -o -name "*.crt"
strings firmware_dump.bin | grep -iE "admin|root|pass|telnet|ftp"
```

What this yields, in experience order: hardcoded service credentials (valid against the live protocol surface of the units in the router), private keys and certs (device identity forgery, update-signature bypass), disabled-but-present services (telnetd/ftp busybox applets), and the vendor's build layout (for patch-diff analysis). firmwalker automates the same sweep; Diffing two vendor images is how the "fixed" bug gets found for real.

## Phase 6 — Badge and RFID access control

Identify the technology before choosing the attack — LF (125-134 kHz: EM4100, HID ProxCard II) is read-only cloning country; HF (13.56 MHz: MIFARE Classic, DESFire, iClass, NFC) runs from broken crypto to solid, and the card type decides everything.

Proxmark3 (RRG/Iceman firmware) is the workhorse — a reader, cloner, and emulator in one. Command-tree note: the Iceman repo is explicitly bleeding-edge; run `help` per subsystem and trust the on-device help over any cheat sheet, including this one:

```text
auto                     # identify what is in the field
lf search                # LF card sweep (EM4100/HID/etc.)
hf search                # HF card sweep (MIFARE family, etc.)
hf mf autopwn            # MIFARE Classic key recovery + dump
hf mf dump / hf mf cload # dump a card / load a clone
# LF cloning targets a writable T5577; HID brute forces facility codes
# (lf hid brute family) — lab/research use only, it jams real readers
```

Attack shape: read the badge (covert reader, or harvest at a long-range reader lane — HID MaxiProx-class antennas read at meters, concealable in a bag), clone to a writable card, present at the door. MIFARE Classic falls to key-recovery (Crypto-1's history); DESFire and modern iClass escalate to implementation and key-management flaws instead. The Flipper Zero packages the same read/clone/emulate loop plus sub-GHz and infrared in a pocket form factor.

The controller side is the same methodology one layer up: door controllers are embedded boards (Phases 2-5 apply — console on the controller is the whole estate), and the door hardware itself fails electronically: REX (request-to-exit) sensor abuse, mag-lock power cutting, exposed controller wiring. Test the reader-to-controller trust, not just the card.

## Phase 7 — RF and sub-GHz

Fixed-code RF (garage doors, gates, older doorbells) is capture-and-replay: record at the target frequency with an SDR (RTL-SDR class for receive, HackRF One for wideband, YARD Stick One for sub-GHz transmit), then replay verbatim. Universal Radio Hacker takes the capture to the bitstream to the replay. Rolling-code systems resist plain replay — treat any "rolling code bypass" as a per-protocol research question, not a default move.

Zigbee (2.4 GHz smart-home meshes): KillerBee suite — `zbstumbler` to map the network, `zbdump -c <channel> -w capture.pcap` to capture, `zbreplay -r capture.pcap` to replay; unencrypted or default-key meshes are the norm, not the exception, in cheap deployments. Z-Wave (908/868 MHz regional) needs a controller-class sniffer (Zniffer); look for unencrypted traffic the same way. 802.11-side work belongs to [[wifi]].

## Phase 8 — USB injection and implant hardware

Delivery tradecraft for everything above, and a bench deliverable in its own right.

- **Keystroke injection** (USB Rubber Ducky class, Bash Bunny, Flipper BadUSB): presents as a keyboard and types a payload — to the OS it is a human typing. DuckyScript compiles to an `inject.bin`; every payload is a PowerShell/cmd one-liner at heart, e.g. a reverse TCP client dialing `ATTACKER_IP:4444`, or `netsh wlan export profile key=clear` followed by exfil. Detection cost: raw keystroke injection lights up scripted-input heuristics — see [[edr]] for which sensors those are.
- **Network implants** (LAN Turtle class): a covert Ethernet adapter-shaped Linux box for the drop point — reverse shell autossh, credential capture, DNS spoofing as loadable modules, managed over SSH at its default address (vendor pattern 172.16.x.x — check the doc for the build).
- **Cable implants** (O.MG Cable class): a charging cable with a WiFi-driven payload board — join its default AP, drive the web UI, geofence or self-destruct the payload. Survives "it's just a cable" inspection.

## Phase 9 — Ethics, safety, and reporting

Badge cloning and RF work touch physical safety systems: announce the door you will test, never defeat fire-escape hardware, and return devices to their found state (photo the original jumper/header config in Phase 1 for this). Rolling-code brute forcing disrupts real users; dead-drop it in a lab. Report findings with the photo chain: header location, dump hash, and the exact credential/key that the firmware or badge yielded — hardware findings live or die on reproducibility.

## Tool appendix (prose)

Proxmark3 (RFID/NFC research, RRG/Iceman), Flipper Zero (pocket multi-tool: RFID, sub-GHz, IR, BadUSB), JTAGulator (automated debug-header discovery), Bus Pirate (universal bus probe), CH341A + SOIC clip (flash reader), FT2232H/J-Link/OpenOCD (JTAG/SWD), binwalk and firmwalker (firmware analysis), flashrom (SPI), URH (SDR demod/replay), RTL-SDR/HackRF One/YARD Stick One/Ubertooth (radios), KillerBee (Zigbee), USB Rubber Ducky/Bash Bunny/LAN Turtle/O.MG Cable (implants). Reference builds: AttifyOS collects the toolchain.
