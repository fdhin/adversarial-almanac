---
technology: "UPnP / SSDP"
kind: bug-bounty-methodology
audience: ai-agent
family: network-services
provenance: "evidence-grounded at zero chains (command-context gate 2026-11-12: no evidence file attacks UPnP; 53 files hit only the Windows HTTPAPI '(SSDP/UPnP)' banner, 1 username 'miranda' collision) — so this methodology is upstream-built by design: nmap upnp-info NSE docs, CISA TA14-017A (SSDP BAF 30.8, SEARCH), NVD CVE-2020-12695 CallStranger, UPnP IGD action semantics from the Device Architecture. Thin unit, first-class per standing rule. Companion overview: [[upnp|UPnP / SSDP]]."
---

# UPnP / SSDP bug bounty methodology

Target model: SSDP on UDP/1900 (unicast + multicast 239.255.255.250), device description XML over
HTTP, SOAP control actions — no authentication in any layer. LAN-adjacent position assumed for the
control phases; only the amplification check applies to internet-facing hosts. Authorization
assumed; prefer reading over writing (port-map creation is a state-changing action — get explicit
sign-off before AddPortMapping).

## Triage table

| Surface | Gate | Pre-auth? | Payoff |
|---|---|---|---|
| SSDP device discovery | any UPnP stack on segment | yes | Device inventory + LOCATION urls — Info |
| Description XML | any device that answers | yes | Model, firmware, service/action map — Info/Low |
| IGD mapping enumeration (GetGenericPortMappingEntry) | routers/NAT boxes with IGD service | yes | Existing port-forward table (lateral recon) — Low/Medium |
| AddPortMapping abuse | IGD with writable mapping set | yes | External exposure of an internal service — High |
| Eventing SUBSCRIBE cross-segment (CallStranger) | pre-2020-04-17 spec stacks (CVE-2020-12695) | yes | Exfiltration channel / reflected traffic — Medium/High |
| SSDP amplification (internet-facing 1900/udp) | spoofable unicast M-SEARCH | yes (abuse) | ~30.8x reflector (CISA TA14-017A) — Medium infra finding |

## Phase 0 — Confirm and fingerprint

```bash
nmap -sU -p 1900 -sV 198.51.100.1
# 1900/udp open  ssdp
nmap -sU -p 1900 --script upnp-info 198.51.100.1
# | upnp-info:  System/1.0 UPnP/1.0 IGD/1.0
# |_ Location: http://198.51.100.1:80/UPnP/IGD.xml
```

The upnp-info script sends a discovery packet and reports the server string plus the description
XML location. Note its `upnp-info.override` argument: by default the script ignores a
target-supplied LOCATION pointing elsewhere (set it `false` only knowingly — the target can aim
your scanner at arbitrary hosts).

Calibration: on Windows targets ignore the `Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)` banner on
5985/47001 — that is the WinRM HTTP stack, not SSDP; this exact banner inflates naive counts.

## Phase 1 — SSDP sweep (discovery)

```bash
# multicast sweep — every UPnP device on the segment answers
nmap --script broadcast-ssdp-discover 2>/dev/null | head -40
# raw unicast M-SEARCH against one host:
printf 'M-SEARCH * HTTP/1.1\r\nHOST: 239.255.255.250:1900\r\nMAN: "ssdp:discover"\r\nMX: 2\r\nST: ssdp:all\r\n\r\n' \
  | timeout 5 nc -u -w 3 198.51.100.1 1900
# targeted: ST: urn:schemas-upnp-org:device:InternetGatewayDevice:1
```

Expected: `HTTP/1.1 200 OK` with `ST`/`USN` (device identity) and `LOCATION` (description URL).
Collect every LOCATION for Phase 2. Also send NOTIFY-free probes with device-type `ST` values
(MediaRenderer, WFA-device, Basic) — stacks answer only for types they implement, which
fingerprints the device class.

## Phase 2 — Description XML mining

```bash
curl -s http://198.51.100.1:80/UPnP/IGD.xml -o igd.xml
# mine: friendlyName, manufacturer, modelNumber (firmware era -> CallStranger applicability),
# every <serviceType> URN (urn:schemas-upnp-org:service:WANIPConnection:1 = IGD gold)
# and each service's <controlURL> + <SCPDURL> (action list).
```

The SCPD (service control protocol definition) fetched per service enumerates every action and its
arguments — read it before guessing envelopes. `GetExternalIPAddress` confirms the WAN side and
that the IGD dialect works.

## Phase 3 — IGD control (the payoff)

**Enumerate existing forwards** (read-only, always safe):

```bash
# per-index walk; stop on error/no more entries
curl -s -X POST http://198.51.100.1:80/ctrl/url \
  -H 'SOAPACTION: "urn:schemas-upnp-org:service:WANIPConnection:1#GetGenericPortMappingEntry"' \
  --data '<?xml version="1.0"?><SOAP-ENV:Envelope xmlns:SOAP-ENV="http://schemas.xmlsoap.org/soap/envelope/" SOAP-ENV:encodingStyle="http://schemas.xmlsoap.org/soap/encoding/"><SOAP-ENV:Body><m:GetGenericPortMappingEntry xmlns:m="urn:schemas-upnp-org:service:WANIPConnection:1"><NewPortMappingIndex>0</NewPortMappingIndex></m:GetGenericPortMappingEntry></SOAP-ENV:Body></SOAP-ENV:Envelope>'
```

Each index returns one mapping: external port, protocol, internal `NewInternalClient` +
`NewInternalPort` — a map of which internal hosts already face outward (lateral recon; the
consistent pivot into other units: aim nmap at the internal clients discovered).

**AddPortMapping** (state-changing — authorized engagements only): same envelope with
`NewExternalPort`, `NewInternalClient` (e.g. an internal admin panel), `NewInternalPort`,
`NewProtocol` TCP, `NewPortMappingDescription` benign-looking. Success = internal service now
reachable on the WAN address: the High finding. Document and **delete with DeletePortMapping**
when finished.

Tools in prose: **miranda** automates Phases 2-3 interactively (set a target host, `load` its XML,
`find action AddPortMapping` by walking the discovered services, then `send` the action with
arguments) — faster than hand-built envelopes and it remembers per-device quirks; **upnpc**
(miniupnpc) does the same from one command line, e.g. listing mappings (`upnpc -l`) and adding
one (`upnpc -a 198.51.100.10 8080 8080 tcp`) against auto-discovered IGDs.

## Phase 4 — Internet-facing hosts: amplification check

If 1900/udp answers from the internet (unicast M-SEARCH), the host is a reflector: CISA TA14-017A
measures SSDP SEARCH at ~30.8x bandwidth amplification. Evidence the factor with your own
payload-size measurement (bytes out vs bytes in), report as abuse-enabling exposure; remediation
is blocking 1900/udp ingress. Do not attempt IGD control from outside — it is normally unreachable
by design; the finding is the reflection.

## Phase 5 — Eventing / CallStranger (CVE-2020-12695)

On stacks implementing the pre-2020-04-17 spec, SUBSCRIBE with a `CALLBACK` URL pointing at
another network segment (or an attacker host) is accepted; the device then delivers event
notifications to that URL — an exfiltration channel from segmented networks and a reflected-traffic
source. Detection of the bug class per the NVD record: subscription delivery URL on a different
segment accepted. Verify passively (spec date, vendor advisory) rather than generating traffic to
third-party hosts; PoC tooling exists in the CallStranger checker referenced from the CVE.

## Reporting notes

- Discovery/description phases: Info — bundle as the recon enabler for the real finding.
- Mapping-table read: Low/Medium (internal topology + existing exposures).
- Successful AddPortMapping demonstration: High — quote request, response, and proof of external
  reachability; always include DeletePortMapping cleanup evidence.
- Amplification: Medium infrastructure finding with measured amplification factor.
- Remediation framing: disable UPnP on routers or restrict SSDP to trusted segments; never expose
  1900/udp to the internet; patch stacks past the 2020-04-17 spec (CallStranger).

## Appendix — Tool map

- **nmap** (`-sU -p 1900`, `upnp-info`, `broadcast-ssdp-discover`) — Phases 0-1.
- **curl / netcat** — raw SSDP probes and SOAP envelopes — Phases 1-4.
- **miranda** — interactive UPnP client (XML load, action discovery, send) — Phase 3.
- **upnpc (miniupnpc)** — one-shot IGD mapping list/add — Phase 3.
- **Wireshark** — SSDP multicast capture on-segment — Phase 1 recon.
- Tools vault companions by name: nmap, netcat, curl, wireshark.

CVE index: CVE-2020-12695 (CallStranger — spec-level cross-segment SUBSCRIBE callback; fixed by
the 2020-04-17 UPnP Device Architecture). SSDP amplification is tracked as an abuse class under
CISA TA14-017A rather than a single CVE.
