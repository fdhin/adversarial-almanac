---
technology: "UPnP / SSDP"
kind: overview
audience: offensive-security
family: network-services
layer: service discovery + device control (UDP/1900 SSDP, HTTP description, SOAP control)
standards: "UPnP Device Architecture (Open Connectivity Foundation; CallStranger fix 2020-04-17)"
status: thin
provenance: "structure-pass count of 41 was inflated — command-context re-gate (2026-11-12) found 0 files attacking UPnP: 53 files matched only the Windows 'Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)' service banner on WinRM/http ports (not a UPnP surface) and one username 'miranda' collision. Kept as a thin first-class unit per standing rule; all facts upstream-verified (NVD CVE-2020-12695 CallStranger; nmap upnp-info NSE docs; CISA TA14-017A amplification table — SSDP BAF 30.8). Enrichment pass 1, wave 2."
---

# UPnP / SSDP

> A zero-configuration control plane that lets any LAN peer ask the router to open the firewall — discovery, description, and command with no authentication anywhere in the chain.

## What it is

UPnP (Universal Plug and Play, now under the Open Connectivity Foundation) is a protocol suite for zero-config device networking: a device announces itself, describes its capabilities in XML, and accepts commands via SOAP. The pieces an operator cares about are three. **SSDP** (Simple Service Discovery Protocol) runs over UDP on port 1900, both unicast and multicast at 239.255.255.250 — HTTP-shaped `M-SEARCH` and `NOTIFY` messages with no auth. A device that hears an `M-SEARCH` for its device type answers with a `LOCATION` header pointing at its **device description XML**, which enumerates embedded devices and services by URN (e.g. `urn:schemas-upnp-org:service:WANIPConnection:1`). Each service then exposes **SOAP control actions** at a URL in that XML.

The design assumption — a trusted home LAN in 1999 — is the entire vulnerability class. There is no authentication in any layer: not in discovery, not in the description fetch, not in the SOAP call. The canonical target is the **IGD (Internet Gateway Device)** service on routers, whose `AddPortMapping` action forwards an external WAN port to an internal host — meaning a pre-auth LAN position converts into external exposure of an internal service in one SOAP request. The spec also has a real CVE history: **CVE-2020-12695 (CallStranger)** — the spec before 2020-04-17 did not forbid event-subscription callback URLs on a different network segment, giving exfiltration and amplified DDoS traffic from vulnerable stacks.

The operator takeaway: **1900/udp answering on a segment is a pre-auth map-and-pivot surface** — enumerate devices, read their XML, and on routers enumerate and create port mappings. On internet-facing hosts, an answering SSDP is an amplification reflector (CISA TA14-017A measures SSDP SEARCH at ~30.8x bandwidth amplification), a reportable finding in its own right. One calibration: the nmap service banner `Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)` seen on Windows WinRM/http ports is an HTTPAPI implementation string, not an exposed UPnP surface — do not chase it (it inflated this unit's original count).

## The anatomy that matters on an engagement

    M-SEARCH * HTTP/1.1  → 239.255.255.250:1900 (or unicast 1900/udp)
      ← HTTP/1.1 200 OK, ST: <device urn>, LOCATION: http://198.51.100.1:PORT/UPnP/IGD.xml
    GET LOCATION → device description XML → <service> URNs + controlURLs + SCPD
    SOAP POST to controlURL: AddPortMapping / GetGenericPortMappingEntry / GetExternalIPAddress
      → firewall rule created / full mapping table read / WAN address learned
    (eventing: SUBSCRIBE with attacker CALLBACK url — the CallStranger class)

Three structural properties drive most findings:

- **No auth token exists anywhere in the chain** — SSDP answers anyone who multicasts or unicasts at 1900/udp; the description XML is a plain GET; SOAP actions carry at most a device-identifying header. Position on the LAN segment is the only credential.
- **The description XML is a free attack-surface map** — device model, vendor, firmware version, embedded devices, every service and action name, before touching anything else. LOCATION values can also be poisoned to point clients at attacker URLs.
- **The IGD action set is a firewall-control API** — `GetGenericPortMappingEntry` walks existing forwards (lateral movement recon), `AddPortMapping` creates them (external exposure of an internal service), `DeletePortMapping` reverts another user's setup (chaos/DoS).

## Where you'll meet it

- **SOHO/branch routers and ISP CPE** — the classic IGD target on internal-engagement segments: one SOAP call exposes an internal admin panel to the internet, bypassing the whole perimeter narrative.
- **NAS boxes, printers, media renderers, DVRs, IoT hubs** — every consumer device line that ships UPnP; firmware eras before mid-2020 carry the CallStranger spec bug.
- **Internet-facing 1900/udp** — misconfigured routers/boxes exposing SSDP to the world; that is an amplification finding (BAF ~30x), not a control channel.
- **Adjacent, not target: Windows hosts** — the `HTTPAPI ... (SSDP/UPnP)` nmap banner on 5985/47001 is the WinRM HTTP stack's service string; SSDP is not listening there.

## Reading the protocol's mind

1. **Discovery answers are unauthenticated and chatty**: a well-formed `M-SEARCH ssdp:all` (or targeted `ST: urn:schemas-upnp-org:device:InternetGatewayDevice:1`) makes every UPnP device on the segment identify itself and hand over its description URL.
2. **The description XML leaks build details pre-exploit** — modelNumber/friendlyName and the full action list (the service control protocol definition) tell you firmware era and exact IGD dialect before you send a single SOAP call.
3. **IGD actions are CRUD on the firewall**: `GetExternalIPAddress` (confirm WAN side), `GetGenericPortMappingEntry` with index 0,1,2… (walk every existing forward), `AddPortMapping` (create your own). See the [[upnp-bug-bounty|methodology]] for the exact envelopes.
4. **Eventing callbacks cross segments on old stacks** — CVE-2020-12695: pre-2020-04-17 spec allowed a SUBSCRIBE delivery URL on another network segment; the device then HTTP-POSTs event traffic wherever you said (exfiltration channel / reflected traffic).
5. **UDP 1900 on the internet is a reflector, not a door** — spoofed SEARCH gets ~30.8x the payload back to a victim (CISA TA14-017A); report as infrastructure abuse, and expect no control-plane reachability from outside.

## Where to go next

[[upnp-bug-bounty|UPnP bug bounty methodology]] — SSDP sweep, description-XML mining, IGD AddPortMapping abuse with miranda and upnpc, and the amplification check.
