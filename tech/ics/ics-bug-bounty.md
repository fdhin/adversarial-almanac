---
technology: "ICS engagement method"
kind: bug-bounty-methodology
audience: ai-agent
family: network-services
role: area-methodology
layer: protocol-agnostic OT/ICS assessment runbook (applies to Modbus, DNP3, IEC-104, IEC 61850 MMS, EtherNet/IP, BACnet, PROFINET, OPC UA)
standards: "NIST SP 800-82 Rev. 3 (OT security) as the doctrine reference"
status: upstream-only
source: "rewritten from HackTricks (src/industrial-control-systems/README.md and the eight ICS protocol pages under src/network-services-pentesting/), CC BY-NC 4.0, accessed 2026-11-13"
provenance: "upstream-only (HackTricks + primary sources); zero corpus evidence by design. Justification for a protocol-common methodology file: every protocol in the area shares the same engagement method — passive-first capture, unauthenticated process-model enumeration, write-surface mapping without writes, and authorization-gated write validation — the same bar an anonymous-access method clears for any FTP daemon; upstream maintains the doctrine as a protocol-independent page (assessment priorities), which is the model for this file. Bucket-2 new-unit wave 1, 2026-11-13."
---

# ICS engagement runbook (protocol-common)

Agent-facing method for any ICS/OT target, independent of protocol. The router
([[ics|ICS area router]]) decides WHICH protocol you are facing and links the per-protocol
pages; this file is the safety contract and the phased method that govern all of them. The
per-protocol pages repeat the gates inline — when they conflict with this file, the stricter
rule wins.

## The safety contract (non-negotiable, before phase 0)

1. **Understand the controlled process before touching it**: what the plant does, what a
   wrong value does physically, which devices are safety-rated. Same device types serve
   different functions per site — no assumptions from previous engagements.
2. **Scope in writing**: approved test plan naming targets, windows, recovery procedures, and
   STOP conditions; process owner reachable during testing. Anything not in the plan does not
   get probed, however harmless it looks.
3. **Passive-first doctrine**: capture and engineering documentation before any active frame.
   Plaintext ICS protocols usually surrender inventory, point maps, addresses, and even
   credentials (BACnet DCC passwords, MMS ACSE values, OPC UA ActivateSession passwords) to a
   SPAN/tap alone.
4. **Never send write function codes to field devices outside the approved window.** Not for
   "one quick test", not same-value, not on a device you believe is lab gear unless the plan
   says so. A write here is a physical actuation (valve, breaker, heater, pump).
5. **Fragility is the default**: OT stacks crash on unexpected input, and availability is a
   safety property. Rate-limit discovery; no fuzzing outside a lab or an explicit resilience
   authorization.
6. **Restore and log**: before/after values for any accepted write; roll back; note broadcast
   addressing (DNP3 `0xFFFF`, IEC-104 `CASDU=0xFFFF`, BACnet broadcasts) so one command is
   never N effects by accident.

## Phase 0 — Identify the protocol (dispatch)

Map each open port through the [[ics|router dispatch table]]: 502 Modbus, 20000 DNP3,
2404 IEC-104, 44818 EtherNet/IP, 4840 OPC UA (+vendor ports), 47808/udp BACnet, 102 MMS-or-
S7comm (check payload: BER = MMS), plus the portless Layer-2 surfaces (PROFINET `0x8892`,
GOOSE `0x88B8`, SV `0x88BA` — reachable only with segment access, never over routed scan).
Confirmation rules: IEC-104 needs `STARTDT` before it speaks; BACnet is UDP — only a parsed
reply counts; a wrong-protocol probe is itself an impact event on fragile stacks, so send the
protocol-correct handshake, not a generic one.

## Phase 1 — Passive recon (preferred entry)

- Capture at the OT boundary or on the control segment (SPAN/tap); do not inject.
- One-pass decode: `oida pcap capture.pcap --protocols ics` covers every listener at once;
  per-protocol tshark filters are in each variant page.
- Extract, per protocol: device identities (vendor/firmware/serial), address spaces (unit IDs,
  link addresses, CASDU/IOA, object lists, tag tables), the master/HMI polling patterns,
  write/control activity, and cleartext credentials where present.
- Pull existing engineering documentation (network diagrams, register maps, SCL files) before
  generating your own.

## Phase 2 — Read-only interrogation

Unauthenticated reads are the recon product; none of them write. Order of operations that
generalizes across the family:

1. **Identity**: device identification / ListIdentity / GetEndpoints / Identify service —
   version and vendor for advisory matching.
2. **Model**: the process point map — Modbus register walk, DNP3 Class-0 integrity poll,
   IEC-104 general interrogation, BACnet object-list walk, CIP object/tag enumeration,
   OPC UA Browse + namespace table, MMS GetNameList.
3. **File surface where it exists** (DNP3 Group 70, BACnet File objects, CIP File Object,
   MMS file services incl. SCL configs): config and logs, frequently ungated.
4. **Security posture**: which security profile is claimed, whether the plaintext sibling
   still answers, SAv5/62351/CIP Security/BACnet-SC actually enforced.

Rate-limit everything; a discovery sweep that floods a fragile segment is a finding against
you, not the target.

## Phase 3 — Write-surface mapping WITHOUT writing

Prefer primitives that prove writability without changing state:

- Access metadata: OPC UA `UserAccessLevel` bits (`--scan-writable`), BACnet commandable
  objects + priority arrays, IEC 61850 `ctlModel`, Logix External Access.
- Control-model identification: Direct Operate vs Select-Before-Operate (DNP3, IEC-104,
  IEC 61850) — SBO is an operator interlock; note it, do not treat it as resistance.
- Same-value write-back (`--test-write` style) is the least-destructive ACTIVE validation:
  still a live write, still gated by the safety contract, executed only in the approved
  window.

## Phase 4 — Gated write validation (authorized window only)

With the process owner on the loop: least-destructive first (acceptance probes like IEC-104
`--test-commands`), then a single chosen point, logged before/after, restored immediately.
Never: clock/time-sync operations (evidence and logic corruption), restart/stop/disable
primitives, DCP Set operations, fuzzing — these are outage and equipment-risk actions beyond
even an authorized write window unless individually specified in the plan.

## Never-do list (outside explicit per-action authorization)

Write function codes to field devices · broadcast-addressed sends · clock writes ·
restart/stop/factory-reset/disable-reporting primitives · DCP station-name/IP changes ·
forward-open or subscription flooding · fuzzing production · unthrottled subnet-wide sweeps ·
any action against safety-instrumented systems (fire/gas, protection relays, e-stop chains)
beyond passive capture.

## Reporting frame

Every finding gets both halves: the cybersecurity impact AND the process consequence
("writable analog output on IOA 200 controls the transformer tap changer"). Report the
protocol trust model (no auth/integrity by design), the compensating controls absent
(segmentation, security profiles, registered-master restrictions), and the exposure path that
got you there — the durable fixes are network isolation and the standards' security profiles,
not patches to a 1979 protocol.

## Per-protocol pointers

[[ics-modbus|Modbus]] · [[ics-dnp3|DNP3]] · [[ics-iec-60870-5-104|IEC 60870-5-104]] ·
[[ics-bacnet|BACnet]] · [[ics-ethernetip|EtherNet/IP]] · [[ics-opc-ua|OPC UA]] · PROFINET and
IEC 61850 MMS live as sections of the [[ics|router]].
