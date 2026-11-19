---
technology: "ICS / OT protocols"
kind: overview
audience: offensive-security
family: network-services
role: area-router
layer: industrial control / OT application protocols (Modbus 502, DNP3 20000, IEC 60870-5-104 2404, EtherNet/IP 44818, OPC UA 4840, BACnet 47808/udp, IEC 61850 MMS 102, PROFINET L2/34964)
standards: "Modbus org specs, IEEE 1815 (DNP3), IEC 60870-5-104, IEC 61850 / ISO 9506 (MMS), ODVA CIP, IEC 62541 (OPC UA), ANSI/ASHRAE 135 (BACnet), IEC 61158/61784 (PROFINET)"
status: upstream-only
source: "rewritten from HackTricks (src/network-services-pentesting/{pentesting-modbus,20000-pentesting-dnp3,2404-pentesting-iec-60870-5-104,44818-ethernetip,47808-udp-bacnet,4840-pentesting-opc-ua,pentesting-profinet,102-pentesting-iec-61850-mms}.md and src/industrial-control-systems/README.md), CC BY-NC 4.0, accessed 2026-11-13"
provenance: "upstream-only (HackTricks + primary sources); zero corpus evidence by design. CVEs re-verified against primary advisories (OPC Foundation bulletins CVE-2024-42512/-42513, GHSA-h958-fxgg-g7w3, VDE-2025-022, Softing PSIRT CVE-2025-7390, Ubuntu CVE-2024-53429); nmap NSE script existence checked against nmap.org/nsedoc. Bucket-2 new-unit wave 1, 2026-11-13."
---

# ICS protocols (area router)

> The only port family where a wrong packet stops a production line or trips a grid breaker: every mainstream ICS protocol authenticates nobody, and the first job is deciding WHICH protocol is on the wire — then staying passive until the write window opens.

## What it is

Industrial control systems (ICS) — the OT side of the house: PLCs, RTUs, IEDs, controllers, and the SCADA/HMI layer that drives them — speak a family of application protocols designed in the 1979–2005 era for availability and determinism, not adversaries. Modbus (1979, Modicon), DNP3 (IEEE 1815), IEC 60870-5-104, IEC 61850 MMS, EtherNet/IP (ODVA's CIP), BACnet (ASHRAE 135), PROFINET, and OPC UA (IEC 62541) between them run factories, grids, water systems, pipelines, and buildings. With one deliberate exception — OPC UA, which CAN authenticate, sign, and encrypt but is routinely deployed with all of it switched off — the legacy protocols ship with **no authentication, no encryption, and no integrity protection on the wire**. Security profiles exist for nearly all of them (Modbus/TCP Security on 802, IEC 62351 for the IEC family, DNP3 TLS/SAv5, CIP Security, BACnet/SC, PROFINET Security Class 3); fielded adoption is minimal, and dual-stack devices usually keep the plaintext path enabled alongside.

The operator takeaway is not "these protocols are broken" — it is that the trust model is *whoever reaches the port*. On a flat or badly segmented network, an IT-side foothold is one hop from a process-control write primitive. The entire assessment doctrine of this area flows from that: inventory passively first, read the process model without credentials, and treat any write function code as a physical action requiring authorization — because it is one.

This matters doubly because these are safety-critical targets. A register write is not a config change; it is a valve, a breaker, a heater, a compressor. Compromise or accidental disruption can stop production, damage equipment, or cause physical harm — the reason every implementation page in this unit gates writes behind explicit confirmation steps.

## The dispatch table — WHICH protocol is this?

The port names the protocol family; the response shape confirms the implementation. Confirm before you enumerate — several of these stacks crash on unexpected input, and a wrong-protocol probe is itself an impact event.

| Port | Protocol | Variant page | The tell |
|---|---|---|---|
| **502/tcp** | Modbus TCP | [[ics-modbus\|Modbus]] | `modbus-discover` NSE answers with device identification; MBAP header, unit-id addressing |
| **20000/tcp+udp** | DNP3 | [[ics-dnp3\|DNP3]] | FT3 frames start `0x0564`; no NSE script exists — needs a real DNP3 client |
| **2404/tcp** | IEC 60870-5-104 | [[ics-iec-60870-5-104\|IEC-104]] | TCP connects, but nothing flows until a `STARTDT` U-frame; `iec-identify` NSE |
| **44818/tcp (+udp)** | EtherNet/IP (CIP) | [[ics-ethernetip\|EtherNet/IP]] | 24-byte `ListIdentity` probe answers with vendor/product/serial; `enip-info` NSE |
| **4840/tcp** (+4843, vendor ports) | OPC UA | [[ics-opc-ua\|OPC UA]] | `GetEndpoints` answers pre-auth with the whole security posture; no NSE script |
| **47808/udp** | BACnet/IP | [[ics-bacnet\|BACnet]] | UDP — only a real reply (I-Am / ReadProperty) proves life; `bacnet-info` NSE |
| **102/tcp** | IEC 61850 MMS **or Siemens S7comm** | section below | Shared TPKT/COTP transport; ASN.1 BER payload = MMS, S7 protocol id 0x32 = S7comm |
| **no port at all** | PROFINET DCP/RT, GOOSE, SV | section below | Layer-2 EtherTypes `0x8892`, `0x88B8`, `0x88BA` — invisible to a routed scan |

Adjacent ports that refine the picture: **802/tcp** Modbus/TCP Security (TLS), **19998/tcp** IEC 62351-3 TLS for IEC-104, **3782/tcp** MMS-over-TLS (the libiec61850 convention), **4843/tcp** OPC UA TLS binding, **2222/udp** EtherNet/IP implicit I/O, **34962–34964/udp** PROFINET RT/CM (the only routable PROFINET surface), and a spread of OPC UA vendor ports (49320 KepServerEX, 53530 Prosys, 62541 open62541). A listener on a security port usually still has its plaintext sibling enabled — always re-check the legacy port.

## The engagement reality (read before touching anything)

This doctrine is protocol-common; every variant page assumes it.

- **These are safety-critical targets.** Understand the controlled process, its safe operating limits, and the blast radius of a write BEFORE any active test. A technically valid probe that faults a fragile stack is an outage you caused.
- **Passive-first.** Every plaintext protocol here hands over its inventory, address space, and control activity from a SPAN port or tap capture alone — unit IDs, point maps, link addresses, cleartext management passwords, even operator credentials (OPC UA `ActivateSession` on a `None` channel, BACnet DCC/ReinitializeDevice passwords, MMS ACSE association values). Capture first; you often finish recon without sending a single frame.
- **Never send write function codes to field devices outside an approved test window.** Modbus FC 5/6/15/16, DNP3 Operate/Direct-Operate, IEC-104 `C_SC`/`C_DC`, BACnet WriteProperty, CIP Set_Attribute/Write Tag, OPC UA Write/Call, MMS writes under `CO`/`SP`, PROFINET CM Write / DCP Set — each is a physical actuation. Validate writes only with explicit operational authorization, a maintenance window, recovery steps, and stop conditions agreed with the process owner.
- **Interlocks are operator safety, not security.** Select-Before-Operate (DNP3, IEC-104, IEC 61850) stops fat-fingered operators; an attacker simply sends Select then Operate. Priority arrays in BACnet resolve contention among legitimate writers; they do not check identity.
- **Broadcast addresses fan out.** DNP3 link address `0xFFFF`, IEC-104 `CASDU=0xFFFF`, and BACnet broadcasts multiply a single forged message across every outstation/device on the segment. Treat broadcast sends as N× impact.
- **Some primitives that look like reads are writes.** An IEC-104 clock read (`C_CS_NA_1`) overwrites the outstation clock as a side effect; a BACnet "same-value" WriteProperty is still a WriteProperty. Gating is about effect, not name.
- **Clock and time operations corrupt evidence and process logic.** Time-sync writes desynchronize event timestamps and historian timelines; treat them as destructive.
- **Restore and document.** Same-value write-backs, `--test-write` style probes, and every accepted write you do make must be logged with before/after values and rolled back.
- **Rate-limit and avoid scan machinery on fragile stacks.** Discovery sweeps should be slow; fuzzing belongs in a lab, or explicitly authorized resilience windows, never routine enumeration.

## The anatomy that matters on an engagement

    IT foothold → flat/under-segmented network → OT segment
        → open control port (dispatch table above)
        → [no auth] read: identity, config, full process point map
        → [no integrity] capture/replay, forged telemetry, on-path rewrite
        → [no authz] write function code = physical actuation (gated by engagement rules, not the protocol)
        → availability primitives as first-class FCs: stop app, restart, disable reporting, connection exhaustion

Three structural properties drive most findings:

- **The process model is free.** Modbus register walks, DNP3 Class-0 integrity polls, IEC-104 general interrogation, BACnet object-list walks, CIP object/tag enumeration, OPC UA Browse/Read, MMS GetNameList — all unauthenticated by default. The point map IS the recon product; malware families (Industroyer, INCONTROLLER/PIPEDREAM) built their kill chains on exactly this enumeration step.
- **CRCs and checksums are noise protection, not integrity.** Every serial legacy CRC (Modbus RTU, DNP3 FT3 `0x3D65`) is trivially recomputable by an on-path attacker. Nothing in the base protocols detects injection or replay.
- **The real boundary is segmentation, and it is usually missing or leaky.** Flat corporate/OT routing, exposed remote access, and (for PROFINET/GOOSE) VLANs that let `0x8892`/`0x88B8` frames cross the IT/OT line are where nearly every engagement actually enters.

## Where you'll meet it

- **Electric utilities** (IEC-104 in Europe/Asia, DNP3 in North America, IEC 61850 in substations) — the highest-stakes variant: control writes operate breakers. Industroyer/CrashOverride (2016) and Industroyer2 (2022) are the proof-of-concept payloads, with per-station point maps hardcoded after the recon phase described here.
- **Water/wastewater and oil & gas** (DNP3, Modbus) — long serial tails behind terminal servers and radio links; TCP-to-RTU gateways make unit/link-address enumeration the entry move.
- **Discrete manufacturing** (EtherNet/IP on Rockwell, PROFINET on Siemens) — the L2-only surfaces (DCP, RT, GOOSE) punish routed scanning; the routable surface is CM-RPC/34964 and the S7 channel on 102.
- **Building automation** (BACnet) — HVAC, lighting, meters, access control; UDP broadcast discovery makes whole-campus inventories a one-packet job, and BBMD relays extend reach across subnets.
- **MES/historian/cloud convergence** (OPC UA) — the one protocol with real security knobs; the finding is almost always the deployment that turned them off (`SecurityPolicy None` + `Anonymous`).
- **Internet exposure**: Shodan indexes all of these (`port:502`, `port:20000`, `port:47808`, `port:4840`, `port:44818`); every protocol page carries its query. Reachable-from-the-internet here means physically dangerous, not just pwned.

## PROFINET (section)

PROFINET (Siemens-led, IEC 61158/61784) is a protocol *family*, and the transport split is the whole game: **DCP discovery and RT cyclic I/O are Layer-2 frames (EtherType `0x8892`) with no IP and no port** — a routed TCP/UDP scan sees nothing. The routable surface is the acyclic context manager, **CM-RPC over UDP 34964** (IANA 34962/34963 RT, 34964 CM), plus the S7 engineering channel on TCP 102 shared with S7comm.

- **Inventory for one broadcast:** DCP Identify-All to the reserved multicast `01:0e:cf:00:00:00` — every device on the segment answers with MAC, station name, IP, vendor/device ID. No credential.
- **Firmware for free:** CM-RPC Read of the I&M0 record (`0xAFF0`) returns exact hardware/software revision and serial per device — the input for offline vuln matching. I&M1–3 (`0xAFF1`–`0xAFF3`) are writable asset labels.
- **DCP Set is the outage primitive:** assign a new station name or IP (or factory-reset) and the device drops out of its controller's Application Relation — a line-stopping fault, credential-free in Security Class 0/1.
- **Tooling:** nmap `multicast-profinet-discovery` (L2, safe/broadcast prerule) and `profinet-cm-lookup` (EPM lookup on 34964, discovery/intrusive) for scripted recon; the oida scanner's profinet module covers DCP + CM-RPC with write operations confirm-gated; scapy's `pnio_dcp` contrib layer for hand-built frames; TIA Portal/PRONETA as the legit IO-Supervisor role you impersonate.
- **Defensive check that doubles as attack confirmation:** `0x8892` frames visible from the corporate segment = VLAN bleed-through past the IT/OT boundary.

## IEC 61850 MMS (section)

MMS (ISO 9506) is the application layer of **IEC 61850**, the substation automation standard. Plaintext **TCP 102**, shared with Siemens S7comm — both ride TPKT/COTP, so the port banner (`102/tcp open iso-tsap`) does not tell you which; ASN.1 BER payloads mean MMS.

- **Model:** Server → Logical Device (MMS domain) → Logical Node (`XCBR1` breaker, `MMXU1` metering, `CSWI1` control) → Data Object → Data Attribute. Wire references use `$` separators (`XCBR1$ST$Pos$stVal`); tools print the dotted form.
- **Functional Constraints decide read vs write territory:** `ST`/`MX`/`CF`/`DC` are status/measurement/config reads; **`CO` is control and `SP`/`SE` are settings** — a write under those is a relay operation. Check `ctlModel` (`CF`) for direct vs Select-Before-Operate before assuming a single write actuates anything.
- **The config jackpot:** MMS file services (ObtainFile/FileDirectory) are routinely left open — the station `.scd`/`.icd` SCL file IS the whole substation's data model and addressing, letting you map every IED offline.
- **ACSE "auth" is a plaintext password:** the association request can carry an `Authentication-value` — a shared token recoverable from any capture, then replayable.
- **The L2 siblings:** GOOSE (`0x88B8`) protection messaging accepts any frame with a higher `stNum` — spoofed trips are trivial without IEC 62351-6 signatures; SV (`0x88BA`) streams can blind protection to real faults. Both need station-bus access, not a routed path.
- **Tooling:** nmap `iec61850-mms` (identity from LLN0/LPHD reads; intrusive), oida's mms module (identify, name-list walk, typed value reads, vendor fingerprinting across MiCOM/SEL/SIPROTEC/ABB/GE families, confirm-gated write testing), libiec61850's Python binding for manual association work. IEC 62351-3 TLS (3782 in libiec61850) is rarely fielded.

## Reading the protocols' mind

1. **The port is a hypothesis; the first structured response is the dispatch.** `iec-identify` answers on 2404 → [[ics-iec-60870-5-104]]; a 24-byte ListIdentity reply on 44818 → [[ics-ethernetip]]; BER on 102 → this page's MMS section.
2. **Everything answers discovery pre-auth** — device identity (vendor, product, firmware, serial) is the first free finding on every protocol; pin it against vendor advisory history before hunting CVEs.
3. **A bare connect proves nothing on IEC-104 and BACnet.** IEC-104 gates data behind `STARTDT`; BACnet is UDP — only a parsed reply is signal, a timeout is not a hit.
4. **Numeric point maps are the recon product** (unit IDs, IOAs, link addresses, object lists, tag tables); named models (OPC UA, Logix tags) hand you semantic names like `*ESTOP*` and `*INTERLOCK*` that rank targets by impact.
5. **If the security profile port is open, check the plaintext sibling anyway** — dual-stack devices keep both, and the unauthenticated path usually still answers.
6. **The most dangerous verbs are protocol-native**: stop-application, cold-restart, disable-unsolicited, DeviceCommunicationControl, CPU STOP, DCP Set — availability attacks that need no exploit at all.

## Where to go next

- [[ics-modbus|Modbus TCP]] — 502, unit-ID enumeration, function-code surface, gateway walks
- [[ics-dnp3|DNP3]] — 20000, FT3 link addressing, Class-0 polls, CROB control models
- [[ics-iec-60870-5-104|IEC 60870-5-104]] — 2404, ASDU type IDs, CASDU/IOA space, grid telecontrol
- [[ics-bacnet|BACnet]] — 47808/udp, object model, priority arrays, BBMD pivots
- [[ics-ethernetip|EtherNet/IP]] — 44818, CIP objects, Logix tag tables, Basecamp-class DoS
- [[ics-opc-ua|OPC UA]] — 4840, security-policy triage, address space, the verified CVE set
- [[ics-bug-bounty|ICS engagement runbook]] — the protocol-agnostic safety contract and phased method
