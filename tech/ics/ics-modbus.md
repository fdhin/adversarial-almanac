---
technology: "Modbus TCP"
kind: bug-bounty-methodology
audience: ai-agent
family: network-services
role: implementation-variant
layer: application protocol over TCP 502 (MBAP + PDU); serial RTU/ASCII variants behind gateways; Modbus/TCP Security (TLS) on 802
standards: "Modbus Application Protocol spec V1.1b3, Modbus over Serial Line V1.02, Modbus/TCP Security spec"
status: upstream-only
source: "rewritten from HackTricks (src/network-services-pentesting/pentesting-modbus.md), CC BY-NC 4.0, accessed 2026-11-13"
provenance: "upstream-only (HackTricks + primary sources); zero corpus evidence by design. nmap modbus-discover NSE args checked against nmap.org/nsedoc; protocol facts pinned to the Modbus.org specification set. Bucket-2 new-unit wave 1, 2026-11-13."
---

# Modbus TCP — implementation methodology

Companion router: [[ics|ICS area router]] (dispatch table, safety doctrine). This page covers the
1979 Modicon protocol in its dominant TCP form: what the MBAP header leaks, how to walk unit IDs,
which function codes read versus write, and how to validate write reachability without changing a
process value. Target model: PLCs, gateways, meters, and RTUs speaking plaintext 502/tcp.

## Triage table

| Surface | Gate | Pre-auth? | Payoff |
|---|---|---|---|
| Device identification (FC 0x2B/0x0E) | none — answered by default | yes | Vendor/product/firmware — Info/CVE index |
| Register/coil reads (FC 1–4) | none in base protocol | yes | Full live process state — High |
| Unit-ID enumeration | gateway in path | yes | Hidden serial devices behind a bridge — Med |
| Write function codes (FC 5/6/15/16) | **authorization only** | yes | Physical actuation — Critical, gated |
| Replay of captured writes | no anti-replay in protocol | capture needed | Silent process change — Critical |
| Modbus/TCP Security (802) | TLS+X.509 deployed | no | Rare; check plaintext sibling 502 anyway |

## Phase 0 — Fingerprint

```bash
nmap -sV -p 502 198.51.100.10
nmap -p 502 --script modbus-discover 198.51.100.10
nmap -p 502 --script modbus-discover --script-args='modbus-discover.aggressive=true' 198.51.100.10
```

`modbus-discover` enumerates live slave IDs and pulls device-identification objects (vendor,
product code, revision). Metasploit carries two scanners for the same job (`modbusdetect`,
`modbus_findunitid`). A listener on 502 that answers a valid read with a proper exception
response is still a confirmed Modbus endpoint — error responses enumerate too.

## Phase 1 — Passive recon

Modbus on 502 is plaintext and unauthenticated; a SPAN capture of the HMI/SCADA polling loop
typically yields every parameter you need before you send anything:

```bash
tshark -r capture.pcap -Y modbus -T fields \
  -e ip.src -e ip.dst -e modbus.unit_id \
  -e modbus.func_code -e modbus.reference_num -e modbus.word_cnt
```

Extract, in order of value: the in-use **unit IDs**, which **function codes** the process actually
exercises, the **register ranges** polled by the master (the live register map), and whether
**write FCs** appear at all (their rarity is what makes a replayed write stand out in logs — or
let it blend in). Display-filter set for triage: `modbus`, `modbus.func_code == 3`,
`modbus.func_code == 16`, `modbus.exception_code`.

## Phase 2 — Read-only enumeration

The MBAP header framing: transaction ID, protocol ID (`0x0000`), length, **unit ID**. Native
TCP devices frequently ignore the unit ID; **TCP-to-RTU gateways do not** — there the unit ID
routes the frame to a downstream serial device, and enumerating unit IDs 1–247 (walking the same
TCP endpoint) is how you find everything behind the bridge.

```python
from pymodbus.client import ModbusTcpClient

client = ModbusTcpClient("198.51.100.10", port=502)
client.connect()
print(client.read_coils(address=0, count=16, device_id=1))
print(client.read_holding_registers(address=0, count=10, device_id=1))
print(client.read_device_information(device_id=1))
client.close()
```

Addressing trap, twice over: product documentation numbers registers one-based (`40001`) while
the protocol and libraries are zero-based — a documented `400001` holding register is offset
`0` on the wire. And the current pymodbus names the unit argument `device_id`; older releases
called it `slave` or `unit` — match the installed version.

The oida scanner's modbus module covers the same ground with typed decoding (modbus install
extra: `oida-ics[modbus]`): `-i` for device identification, `--scan-fc` to enumerate which
function codes answer, `-U --unit-range 1-247` for gateway unit walks, `-r 0-100 -d f32` to read
holding registers decoded as floats (`-d` takes f32/f64/i16..u64/str/hex/bcd; `-e` sets byte/word
order). On solar/battery sites, the SunSpec model makes the register map self-describing: a
compliant device parks the marker `SunS` at base 40000/0/50000, and oida's `-S` walks the
model chain from there; `--sunspec-assess` flags writable control models (grid connect/disconnect,
curtailment, battery charge) without writing them.

## Phase 3 — Write surface (gated)

The write function codes — `0x05` single coil, `0x06` single register, `0x0F` multiple coils,
`0x10` multiple registers, `0x16` mask-write, `0x17` read/write multiple — are physical
actuations. Rules of engagement from the router apply: approved window, process owner on the
loop, before/after values logged. The least-destructive validation is a **same-value write**:
read the register, write its own value back, confirm the normal response. oida implements
exactly this as `--test-write` (with `--test-write-thorough --restore-on-exit` for servers that
reject identical writes) — and it is still a live write, so it stays behind `--confirm`.

Realistic write-adjacent test cases, in the order defenders actually see them: query flooding,
false-data injection, brute-force writes, replay of captured commands, malformed
length/frame handling. Memory-corruption CVE hunting is the rare path; the protocol's
authorization model is usually the finding.

## Phase 4 — Fuzzing and packet crafting (lab-first)

scapy's modbus contrib layer builds frames for anything a client library will not express
(odd function codes, malformed lengths, replay with minimal edits):

```python
from scapy.contrib.modbus import ModbusADURequest, ModbusPDU2B0EReadDeviceIdentificationRequest
from scapy.all import sr1
pkt = ModbusADURequest(transId=1, unitId=1) / ModbusPDU2B0EReadDeviceIdentificationRequest()
resp = sr1(pkt, timeout=2)
resp.show() if resp else print("no answer")
```

Malformed-frame tolerance belongs on a lab rig or an explicitly authorized resilience window —
Modbus TCP stacks behind production processes are not fuzz targets. The smod framework
(function-code enumeration, UID brute force, fuzzing modules) and the modbuster CLI exist for
dedicated Modbus attack work.

## Command quick reference

```bash
nmap -p 502 --script modbus-discover 198.51.100.10          # identity + unit discovery
msfconsole -q -x "use auxiliary/scanner/scada/modbusdetect; run"
tshark -r capture.pcap -Y 'modbus.func_code == 16'           # who writes what
python3 -c "..."                                             # pymodbus reads (Phase 2)
```

## Tool mapping (prose)

nmap with the modbus-discover NSE for banner/identity sweeps; Metasploit's SCADA scanner
modules (modbusdetect, modbus_findunitid) for unit-ID walks; pymodbus for scripted client
reads; scapy's modbus contrib layer for packet crafting; the oida multi-protocol ICS scanner
for typed enumeration and gated write testing; smod and modbuster as dedicated Modbus attack
frameworks; Wireshark/tshark for the passive layer. Tools vault owns per-tool detail.
