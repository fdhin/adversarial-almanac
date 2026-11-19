---
technology: "mDNS"
kind: overview
audience: offensive-security
family: network-services
layer: link-local name resolution and service discovery (5353/udp, multicast 224.0.0.251 / ff02::fb)
standards: "RFC 6762 (Multicast DNS), RFC 6763 (DNS-Based Service Discovery); Avahi on Linux, Bonjour on Apple"
status: thin
provenance: "corpus-grounded (5 writeups under command-context gate — 4 files: Responder runs with the MDNS poisoner ON during LLMNR/NBT-NS capture workflows; 1 file: mDNS broadcast auto-discovery as the CUPS attack's delivery mechanism. Gate note 2026-11-12: avahi entries in /etc/passwd listings (3 further files) are presence noise, not attack evidence) + upstream-verified (RFC 6762 — .local namespace, legacy unicast responses to source ports ≠5353, security considerations on trusting multicast answers). Thin unit treated first-class; upstream supplies protocol behavior. Enrichment pass 1, 2026-11-12."
---

# mDNS

> Name resolution with no server and no auth: every host on the segment answers `.local` queries to a multicast group — so a single spoofing position on the LAN collects every name lookup the workstations make.

## What it is

Multicast DNS (RFC 6762) is DNS without an infrastructure server: a host with a name or service to advertise joins multicast group 224.0.0.251 (ff02::fb for IPv6) on UDP port **5353**, and both queries and answers go to the whole link-local group. The `.local` suffix is the reserved namespace; DNS-SD (RFC 6763) layers service browsing on top — `_http._tcp.local`, `_ipp._tcp.local`, `_smb._tcp.local` — which is why printers, file shares, and screenshares announce themselves with zero configuration. Avahi is the Linux implementation (daemon avahi-daemon, tools avahi-browse and avahi-resolve), Bonjour the Apple one, and Windows resolves `.local` through its own stack.

The operator takeaway: mDNS is a **segment-local trust oracle**. Any position that can emit packets to the multicast group can answer queries it did not receive (or forge replies to ones it did), and clients accept answers per the RFC's model — "the security considerations... assume the local link is trusted" is the whole vulnerability in one clause. It also *discloses*: a listening attacker enumerates hosts and services from the chatter alone, no query needed.

## The anatomy that matters on an engagement

    querier → 224.0.0.251:5353 → all hosts on segment
        responders answer for their own names/services (_ipp._tcp.local, A/AAAA/SRV/TXT)
    attacker on segment → passive: catalog hosts/services from announcements
                         → active: answer any query (Responder's MDNS poisoner) → capture NTLM/etc.
    legacy unicast: query from source port ≠5353 → responder MUST reply unicast (RFC 6762 §6.7)
                    → reflection vector when spoofable

Three structural properties drive most findings:

- **No authentication on answers.** The protocol trusts whoever talks on the group — poisoning mDNS (Responder's MDNS engine, 4 observed engagements) redirects `.local` names to the attacker, feeding the same hash-capture machinery as LLMNR/NBT-NS poisoning.
- **Announcements are an inventory.** Service enumeration is passive by design — the segment broadcasts its printers, file servers, and workstations with hostnames and metadata.
- **Legacy-unicast replies create a reflection path.** RFC 6762 §6.7: queries arriving from a source port other than 5353 get a unicast reply to the (spoofable) source address — the documented basis for mDNS reflection/amplification DDoS observations on internet-exposed 5353/udp.

## Where you'll meet it

- **Every office LAN as a capture channel** (4 observed engagements): Responder's poisoner stack runs MDNS alongside LLMNR and NBT-NS — poisoned `.local` lookups during legit workflows (file shares, print dialogs) yield NTLMv2 hashes.
- **Printer/CUPS discovery paths** (1 observed engagement): broadcast mDNS auto-discovery is how clients find IPP printers — an attacker answering the browse query becomes the "printer", feeding the CUPS attack chain (cross-ref [[cups]]).
- **Home/OT/IoT segments**: appliances, TVs, cameras chatter services over mDNS with no infrastructure DNS — enumeration-only engagements get full inventories from one tap.
- **Internet-exposed 5353/udp** (misconfigured): reflection source; also unauthenticated enumeration of internal hostnames from outside.

## Reading the protocol's mind

1. **`5353/udp open` on nmap is usually filtered-by-design from off-segment** — mDNS enumeration wants a segment position (same L2) or a responder that ignores scope.
2. **avahi-browse -ar** on a foothold host prints the segment's advertised services in real time — free inventory before any active move.
3. **Responder's MDNS poisoner pairs with LLMNR/NBT-NS by default** — enable all three, then trigger name lookups (SMB browse, print, typo'd shares).
4. **Answers are only as trustworthy as the link** — RFC 6762 says so explicitly; any answer you can send is accepted for names nobody contests.
5. **`.local` never leaves the segment by design** — but hybrid resolvers sometimes forward it; a `.local` query hitting unicast DNS 53 is itself a misconfig signal worth reporting.

## Where to go next

[[mdns-bug-bounty|mDNS bug bounty methodology]] — enumeration workflows, poisoning with Responder, reflection checks, and nmap/avahi tooling.
