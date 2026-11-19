---
technology: "WS-Discovery (WSD)"
kind: bug-bounty-methodology
audience: ai-agent
family: network-services
layer: "SOAP-over-UDP multicast discovery (UDP 3702; IPv4 multicast 239.255.255.250, IPv6 ff02::c; OASIS WS-Discovery)"
status: "upstream-only — bucket-2 new-unit, zero corpus evidence by design"
ht-source:
  - "rewritten from HackTricks (src/network-services-pentesting/3702-udp-pentesting-ws-discovery.md), CC BY-NC 4.0, accessed 2026-11-13"
provenance: "upstream-only: rewritten from HackTricks per the third-party-reference rule; reflection/amplification facts verified against the Axis Communications security advisory 'DDoS attacks using WS Discovery' (Sept 2019) and CISA alert TA14-017A (WS-Discovery bandwidth amplification factor listed as 10 to 500; Akamai-reported 2019 in-the-wild WSD attacks); NSE script names verified against nmap.org nsedoc (wsdd-discover, broadcast-wsdd-discover). Zero corpus evidence by design (new-unit wave 3, 2026-11-13)."
---

# WS-Discovery (3702/udp) — device discovery and reflection

Target model: the Web Services Dynamic Discovery protocol — SOAP over
UDP multicast — by which cameras, printers, and Windows services
announce and find each other on a LAN. UDP 3702, multicast group
`239.255.255.250` (IPv4) / `ff02::c` (IPv6). You meet it in ONVIF camera
fleets, network printers, Windows WSD/WCF services, and embedded/IoT
devices. It is the SOAP cousin of SSDP in [[upnp]]: a chatty,
pre-auth, directory-service-by-design protocol that leaks inventory and
management endpoints to anyone who can send a Probe — and, when
wrongly exposed to the internet, a reflection/amplification DDoS
reflector.

The protocol's four message types are all you need: **Hello** on join,
**Bye** on leave, **ProbeMatch** answering a discovery **Probe**,
**ResolveMatch** answering a **Resolve** (name→address). The offensive
value is in what a ProbeMatch carries:

- **Types** — device/service class (`dn:NetworkVideoTransmitter` for
  cameras, `wprt:PrintDeviceType` for printers, `pub:Computer` for
  Windows hosts)
- **Scopes** — metadata: model/hardware family, MAC addresses, ONVIF
  profiles, roles, location-like and asset labels
- **XAddrs** — the follow-up management endpoint, typically the
  HTTP(S) SOAP URL you actually attack next (e.g. an ONVIF
  `http://<device>/onvif/device_service`)

## Phase 1 — Discover devices

```bash
nmap -sU -p 3702 --script wsdd-discover 198.51.100.10     # one known host
sudo nmap --script broadcast-wsdd-discover                 # whole L2 segment via multicast
```

`wsdd-discover` (portrule, safe/default categories) also surfaces
published WCF web services (.NET 4.0+) on Windows hosts — often the
only visible trace of an internal .NET service. Output shape:

```text
3702/udp open|filtered unknown
| wsdd-discover:
|   Devices
|     Message id: 39a2b7f2-fdbd-690c-c7c9-<uuid>
|     Address: http://198.51.100.116:50000
|_    Type: Device wprt:PrintDeviceType
```

The `XAddrs` line is the deliverable: it tells you where to pivot —
ONVIF device service (then RTSP/media profiles and camera auth),
printer SOAP/IPP admin endpoints ([[jetdirect]] owns the printer
surface), or the Windows WCF HTTP service.

Multicast does not cross routed segments: a device two VLANs away is
invisible to `broadcast-wsdd-discover`. Once you can guess the subnet,
send the **same Probe unicast** to UDP 3702 per host — devices answer
unicast Probes the same way, which makes routed-environment sweeps
work.

Manual Probe when you want the raw XML (trim the Types element to the
class you're hunting; cameras answer `dn:NetworkVideoTransmitter`,
printers expose `tds:Device`/`wprt:PrintDeviceType`):

```python
import socket, uuid
probe = f'''<?xml version="1.0" encoding="UTF-8"?>
<e:Envelope xmlns:e="http://www.w3.org/2003/05/soap-envelope"
 xmlns:w="http://schemas.xmlsoap.org/ws/2004/08/addressing"
 xmlns:d="http://schemas.xmlsoap.org/ws/2005/04/discovery"
 xmlns:dn="http://www.onvif.org/ver10/network/wsdl">
<e:Header><w:MessageID>uuid:{uuid.uuid4()}</w:MessageID>
<w:To>urn:schemas-xmlsoap-org:ws:2005:04:discovery</w:To>
<w:Action>http://schemas.xmlsoap.org/ws/2005/04/discovery/Probe</w:Action>
</e:Header><e:Body><d:Probe>
<d:Types>dn:NetworkVideoTransmitter</d:Types>
</d:Probe></e:Body></e:Envelope>'''.encode()
s = socket.socket(socket.AF_INET, socket.SOCK_DGRAM)
s.setsockopt(socket.IPPROTO_IP, socket.IP_MULTICAST_TTL, 1)
s.bind(("0.0.0.0", 0)); s.settimeout(3)
s.sendto(probe, ("239.255.255.250", 3702))
while True:
    try:
        data, addr = s.recvfrom(65535)
        print(addr[0], data.decode(errors="ignore"))
    except TimeoutError:
        break
```

Passive alternative on a segment you can sniff:

```bash
sudo tcpdump -ni eth0 udp port 3702
sudo tshark -i eth0 -f "udp port 3702" -Y 'xml.tag == "d:ProbeMatch" || xml.tag == "wsdd:ProbeMatch"'
```

## Phase 2 — Read the leak

Even where nothing is directly exploitable, `Scopes` is free
inventory: vendor/model families, MAC addresses, product roles, asset
naming and location labels — input for password-spray prioritization,
default-credential targeting per model, and firmware-version hunting.
A WS-Discovery census of a camera-heavy estate is usually the fastest
way to split ONVIF cameras from printers from Windows hosts before
touching any of them.

## Phase 3 — Rogue responder / impersonation

In a flat network, nothing authenticates a ProbeMatch. A rogue host can
answer multicast Probes faster than the real devices and advertise
attacker-controlled `XAddrs` — effective against management platforms
that auto-discover devices and then connect to whatever endpoint they
were handed. Test the three trust questions: does the client take the
**first responder**, does it validate the endpoint's identity, does it
**reuse credentials automatically** against the advertised service?
Each yes upgrades this from prank to credential capture or onboarding
redirect.

## Phase 4 — Reflection / amplification (internet-facing 3702)

An internet-exposed UDP 3702 is a spoofed-source reflector: the
attacker sends Probes with the victim's source address; devices reply
with ProbeMatches to the victim. Verified numbers: CISA alert TA14-017A
lists WS-Discovery with a bandwidth amplification factor of **10 to
500** (Akamai reported in-the-wild WSD reflection attacks in September
2019, tens of Gbps); the Axis advisory (Sept 2019) notes their ONVIF
devices answer spoofed WS-Discovery requests with **WS-Discovery
enabled by default regardless of whether the ONVIF API is used**.

From a pentest perspective, internet-reachable 3702 is less "DDoS
finding" and more a segmentation finding: the device is badly exposed,
sits in a fleet (cameras/IoT) with weak hardening, and the same
exposure almost always includes the management interfaces the XAddrs
would list. Report: block/never expose UDP 3702 at the edge; on-device
mitigations are IP filtering or disabling WSD where the vendor
supports it.

## Tool appendix (prose)

nmap NSE wsdd-discover and broadcast-wsdd-discover; plain Python/Scapy
for unicast Probe sweeps; tcpdump/tshark filters above for passive
collection. Follow-on surfaces: ONVIF/RTSP tooling for cameras (no
dedicated vault unit yet — generic SOAP/HTTP tooling applies), printer
enumeration under [[jetdirect]], and the SSDP/UPnP methodology in
[[upnp]] for the multicast-discovery attack class as a whole.
