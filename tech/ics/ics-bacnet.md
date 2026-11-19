---
technology: "BACnet (BACnet/IP)"
kind: bug-bounty-methodology
audience: ai-agent
family: network-services
role: implementation-variant
layer: building automation protocol over UDP 47808 (0xBAC0); MS/TP serial, BACnet/SC (TLS 1.3 over wss) variants
standards: "ANSI/ASHRAE 135, ISO 16484-5, Annex AB (BACnet/SC)"
status: upstream-only
source: "rewritten from HackTricks (src/network-services-pentesting/47808-udp-bacnet.md), CC BY-NC 4.0, accessed 2026-11-13"
provenance: "upstream-only (HackTricks + primary sources); zero corpus evidence by design. nmap bacnet-info NSE and Metasploit bacnet_l3 module checked against nmap.org/nsedoc and Rapid7 docs; property/service IDs pinned to the ASHRAE 135 service set. Bucket-2 new-unit wave 1, 2026-11-13."
---

# BACnet — implementation methodology

Companion router: [[ics|ICS area router]]. BACnet is the dominant building-automation
protocol: HVAC, lighting, metering, access control, fire/life-safety panels, and the
workstations supervising them. BACnet/IP rides **UDP 47808 (0xBAC0)** with no authentication
and no encryption; anyone who can reach the port discovers every device, reads every object,
and drives setpoints and actuators where writes are accepted. The physical blast radius is a
building, not a factory — which is exactly why doors and life-safety points rank the findings.

## Triage table

| Surface | Gate | Pre-auth? | Payoff |
|---|---|---|---|
| Who-Is discovery | none — every device must answer | yes | Full device inventory, one broadcast — Info |
| Device identity (bacnet-info) | none | yes | Vendor/model/firmware/location — Info/CVE index |
| object-list + present-value walk | none | yes | Whole plant model + live values — High |
| WriteProperty (service 15) | **authorization only** | yes | Setpoints/valves/fans/locks — Critical, gated |
| DCC / ReinitializeDevice passwords | optional, usually unset | yes (capture) | Outage + reboot primitives; cleartext on wire — High |
| BBMD / foreign-device registration | none | yes | Cross-subnet broadcast pivot — Med |
| BACnet/SC (wss, TLS 1.3) | deployed | no | Rare; dual-stack devices still answer UDP 47808 |

## Phase 0 — Fingerprint (UDP truth rules)

```bash
nmap -sU -p 47808 --open -sV 198.51.100.10
nmap -sU -p 47808 --script bacnet-info 198.51.100.10
nmap -sU -p 47808 --script bacnet-info --script-args full=yes -sV 198.51.100.10
```

BACnet/IP is connectionless: a bare send always "succeeds", so **only a real reply proves a
peer** — an I-Am, a ReadProperty response, or a network-layer answer. Even an error response
confirms BACnet is present; a device cannot hide by rejecting the query. `bacnet-info` reads
vendor, model, firmware revision, and description off the Device object with no
authentication. Metasploit's `auxiliary/scanner/scada/bacnet_l3` broadcasts Who-Is across a
subnet and polls each discovery for the same fields.

## Phase 1 — Passive recon

Devices broadcast I-Am at startup and after network changes, and management traffic carries
secrets in the clear — a capture usually beats active probing:

```bash
bacapp                                   # all application-layer PDUs
bacapp.unconfirmed_service == 8          # Who-Is / == 0 I-Am (inventory)
bacapp.confirmed_service == 12           # ReadProperty
bacapp.confirmed_service == 15           # WriteProperty (setpoint/actuator changes)
bacapp.confirmed_service == 17           # DeviceCommunicationControl (cleartext password)
bacapp.confirmed_service == 20           # ReinitializeDevice  (cleartext password)
bvlc.function == 5                       # RegisterForeignDevice (BBMD table changes)
```

The prize: DCC and ReinitializeDevice send their management password as an unprotected
CharacterString, so one captured management action yields the password directly — nothing to
crack. oida's pcap mode (`oida pcap capture.pcap --protocols bacnet`) rebuilds per-session
views and recovers those passwords automatically.

## Phase 2 — Read-only enumeration

The object model is what turns a dump into impact. Each node has one **Device object**
(identified by a network-unique 22-bit instance, 0–4194302 — the address you target,
independent of IP) holding an **object-list** (property 76) of typed objects
(`analog-input`=0, `analog-output`=1, `analog-value`=2, `binary-output`=4, `schedule`=17,
`file`=10, `life-safety-point`=21), each with **properties** — `present-value` (85) is the
live value; `priority-array` (87) and `out-of-service` (81) decide impact.

```bash
oida bacnet 198.51.100.0/24               # Who-Is sweep of a subnet
oida bacnet 198.51.100.10 -e --values     # walk every object, read every present-value
oida bacnet 198.51.100.10 -r AV:1:pv      # ReadProperty analogValue:1 present-value
oida bacnet 198.51.100.10 --assess        # read-only posture check (see below)
oida bacnet 198.51.100.10 --networks      # remote BACnet networks behind routers/BBMDs
oida bacnet 198.51.100.10 --dump -o backup# export full device state
```

Install extra `oida-ics[bacnet]`. Object specs use `TYPE:INSTANCE:PROPERTY` (short and long
forms both accepted). `--assess` runs the full read-only pass — unauthenticated access,
writable-object discovery, priority-array/out-of-service inspection, schedule/trendlog review,
BACnet/SC posture — with no `--confirm` needed because it never writes. Manual work via the
reference C stack bacnet-stack: `bacwi` (Who-Is broadcast; a range argument keeps it quiet on
big nets), `bacrp` for per-property reads (including `all`), `bacepics` for a full device
walk; BAC0 is the Python scripting library over the same service set (`bacnet.whois()`,
`bacnet.readMultiple(f"{ip} device {id} all")`).

## Phase 3 — Write surface and impact (gated)

Every mutating action in oida is `--confirm`-gated:

```bash
oida bacnet 198.51.100.10 -w AV:1:pv:72.5 --confirm        # WriteProperty
oida bacnet 198.51.100.10 --brute-force --confirm          # DCC + Reinit passwords
oida bacnet 198.51.100.10 --call write AV:1:pv:72.5 --confirm  # arbitrary service catalog
```

Map impact through the object semantics, not the protocol:

- **WriteProperty on a commandable object is a control action** — setpoints, valve/damper
  positions, fans, pumps, relays. No access-control model guards the service.
- **Priority-array hijack**: commandable values resolve through 16 priority levels (1 highest,
  1–2 life-safety). Writing at priority 8 (manual operator) beats the scheduler and automation
  without touching the levels that raise alarms; some controllers retain the array across
  warmstart.
- **Out-of-service spoofing**: setting `out-of-service` (81) TRUE on an input decouples
  present-value from physical I/O — a temperature that reads normal while the room overheats.
- **Schedule/Calendar writes** change occupied hours: HVAC and lighting forced on, or an access
  window moved.
- **DCC silences a controller** (and its alarms) for a duration; ReinitializeDevice reboots it.
  Default passwords are usually empty; both are clean outage primitives.
- **BBMD abuse**: register as a foreign device (BVLC 0x05, unauthenticated) or rewrite the
  broadcast table to pivot discovery and control across subnets you never had a path to.
- **UDP replay**: no session, no anti-replay — captured value-change packets re-force a state.

## Phase 4 — BACnet/SC and closure

Where Secure Connect is deployed, the same application layer rides TLS 1.3 + X.509 over
`wss://` (oida: `--sc wss://198.51.100.5:47800 --ca ca.pem --cert c.pem --key k.key`, with
`--hub-uri` for hub deployments). Cert verification in tooling is permissive by default —
encrypted but not verified unless you load a trust store. The decisive check is simpler: most
SC-capable controllers keep legacy BACnet/IP enabled in parallel, so **re-scan UDP 47808 on
every SC device** — the unauthenticated path usually still answers. Closure workflow: confirm
BACnet/IP → identity → object walk (`-e --values`) → posture (`--assess`) → networks/BBMD map
→ only then gated write validation on a chosen point, restored and logged.

Shodan closure for external recon: `port:47808`, `"Instance ID" "Vendor Name"`.

## Command quick reference

```bash
nmap -sU -p 47808 --script bacnet-info 198.51.100.10   # zero-auth identity
oida bacnet 198.51.100.0/24                            # Who-Is inventory
oida bacnet 198.51.100.10 --assess                     # read-only posture
tshark -r capture.pcap -Y 'bacapp.confirmed_service == 15'   # who writes what
```

## Tool mapping (prose)

nmap with the bacnet-info NSE for identity; Metasploit's bacnet_l3 scanner for subnet-wide
discovery; the oida ICS scanner (bacnet module over a bacpypes3 transport) for enumeration,
assessment, and confirm-gated writes; bacnet-stack's CLI utilities for hand-run service
requests; BAC0 for Python scripting; Wireshark/tshark for the passive layer including
cleartext DCC/Reinit password recovery. Tools vault owns per-tool detail.
