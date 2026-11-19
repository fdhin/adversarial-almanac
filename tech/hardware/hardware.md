---
technology: "Hardware attack surface (area)"
kind: router
audience: offensive-security
status: "upstream-only — owner-approved new-unit wave (PB-INGEST) 2026-11-13; router — device families split per unit"
family: hardware
layer: physical/embedded device access — debug consoles (UART / JTAG / SWD / SPI), firmware extraction and analysis, badge and RF access control (RFID/NFC, sub-GHz), USB implants — the layer beneath every device-protocol unit in this vault
provenance: "upstream-only (pentest-book + primary sources); zero corpus evidence by design — owner-approved new-unit wave (PB-INGEST) 2026-11-13; tool facts grounded during the wave: Proxmark3 identity and command-tree volatility verified against the RfidResearchGroup/proxmark3 repo (Iceman fork, GPL-3.0, bleeding-edge command tree); OpenOCD / flashrom / binwalk usage per their upstream docs. Carries zero CVEs by design — the unit documents device access mechanics, not version-gated bugs"
source: "six2dez/pentest-book (others/hardware.md, no license — rewritten with attribution), accessed 2026-11-13"
---

# Hardware attack surface — the unit beneath the protocol

> Every device unit in this vault — the BMC, the printer, the camera, the PLC — is a Linux or RTOS box wearing a protocol; open the case and there is a second, older interface set underneath: unpopulated debug headers, firmware flash chips, and badge readers that trust a 30-year-old RF card. Network controls stop mattering the moment you hold the board.

## What this page is

This is the **area router** for physical and embedded device access. The vault's device units document what each product exposes on the wire; this family documents what the device exposes on the bench: serial consoles that were never meant to face users, flash chips that hold the whole firmware image, RF badges that clone for pennies, and USB form factors built for implant work. When the network path is dead, filtered, or you are onsite with the hardware in hand, this is the layer that decides the engagement.

Scope line: this unit covers the *electronic* attack surface — debug interfaces, firmware media, RF access control, implant hardware. Mechanical lockcraft and pure physical entry (bypass tools, under-door techniques) are a separate discipline and out of this vault's shape; the moment a door is held by a badge reader and a controller, though, the reader and controller are electronics and the badge is a token — that part is ours, in the methodology.

## The bench dispatch — WHICH device is in front of you?

The engagement's first question, same as any area page. Identify the device class, then go to the unit that documents its network surface; this page's methodology covers what all of them share once the case is open.

| Device in hand | Its network/protocol surface | Page |
|---|---|---|
| Server lights-out / management card | IPMI / RMCP+ over UDP 623, per-vendor HTTP/SSH/VNC | [[ipmi]] |
| Printer / MFP | Raw 9100 printing, PJL/PCL/PostScript, telnet/SNMP admin | [[jetdirect]] |
| IP camera / NVR / doorbell cam | RTSP 554/8554, P2P camera stacks | [[rtsp]] |
| PLC / RTU / gateway / HMI | Modbus, DNP3, EtherNet/IP, BACnet, OPC UA, IEC 104 | [[ics]] |
| AP / embedded radio | 802.11 capture and auth attacks | [[wifi]] |
| Generic embedded Linux / IoT appliance | whatever it ships — start at the firmware | [[hardware-bug-bounty]] |

Vendor-agnostic embedded devices (smart-home hubs, kiosks, medical and lab gear, anything with a case you're allowed to open) have no protocol unit — for them the *bench* is the primary surface, and the methodology file is the whole attack.

## The hardware-inherent surface

Four families recur on essentially every board; the methodology carries the working detail.

- **Debug consoles (UART, JTAG/SWD).** Vendors ship bootloaders and shells on 4-5 exposed pads or headers because development needs them and a production respin is expensive. A UART console found on a camera or printer is a root shell on a device whose network daemon would have taken a week; JTAG/SWD goes further — halt the CPU, dump flash, bypass the firmware's own permission model entirely.
- **Firmware media (SPI/NAND flash).** The 8-pin SOIC chip on the board holds everything: firmware, hardcoded credentials, certificates, the config the network unit keeps referencing. Desolder-free clip readers make this a ten-minute operation, and it is the ground truth the other units' CVEs live inside.
- **Badge / RF access control (RFID/NFC, sub-GHz).** Access-control estates run on cards whose readable technologies (EM4100, HID Prox, MIFARE Classic) predate modern threat models. LF cards clone to cheap writables outright; MIFARE Classic's key weaknesses are its own genre; fixed-code sub-GHz (garage, gate, older door RF) replays verbatim. This is the layer where "physical access controls" meet "weak cryptography."
- **Implant and injection hardware (USB).** Keystroke-injection dongles and cable implants are how bench capabilities walk out to the client floor — a rubber-ducky-class device is a keyboard to the OS, and a cable with a WiFi implant is a persistence device that outlives reimaged machines. Covered as tradecraft in the methodology, with its detection cost noted against [[edr]].

## Where you'll meet it

- **Onsite / internal engagements** with physical access agreed in scope: the fastest root on an appliance is usually a header, not a CVE.
- **Red-team physical access** where the deliverable is a badge clone or a dropped implant, not a shell.
- **Hardware product assessments / IoT reviews** — the whole engagement is this page.
- **Dead-end network findings** — when the protocol unit's surface is patched and filtered, the firmware on the bench frequently still carries the hardcoded account the patch cycle missed.

## Where to go next

The phased bench method — board recon, UART hunt, JTAG/SWD debug, SPI flash dump, firmware analysis, badge cloning, RF replay, implant tradecraft — is in [[hardware-bug-bounty]].
