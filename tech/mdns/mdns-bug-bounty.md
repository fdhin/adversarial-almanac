---
technology: "mDNS"
kind: bug-bounty-methodology
audience: ai-agent
family: network-services
provenance: "corpus-grounded (5 writeups: Responder MDNS poisoner in capture workflows (4 files); mDNS broadcast auto-discovery as delivery for CUPS exploitation (1 file)) + upstream-verified (RFC 6762 — multicast group/port, legacy unicast response rule §6.7, trust-the-link security model). Thin corpus; upstream supplies protocol mechanics. Companion overview: [[mdns|mDNS]]. Enrichment pass 1, 2026-11-12. Phase 5 additions: rewritten from HackTricks (hacktricks/src/network-services-pentesting/5353-udp-multicast-dns-mdns.md), CC BY-NC 4.0, accessed 2026-11-13 — nmap DNS-SD script names verified against the nmap repo (which also disproved this page's earlier 'broadcast-mdns-discover' — corrected 2026-11-13), Pholus flags verified against its README, zerogod commands against bettercap's module docs, Avahi CVEs against Ubuntu USN-6487-1, Cisco CVE-2024-20303 against GHSA-jfv4-r4r3-xx7w, Apple CVEs against support.apple.com advisories 121250/122716. HT's CVE-2023-1981 not carried (not verified within budget); HT's 'zerogod.show-full' not carried (absent from current module docs)."
---

# mDNS bug bounty methodology

Target model: 5353/udp, multicast 224.0.0.251/ff02::fb, `.local` namespace, DNS-SD
service types. Requires a segment position (same L2) for full play; internet-exposed
5353/udp is a separate reflection check.

## Triage table

| Surface | Gate | Pre-auth? | Payoff |
|---|---|---|---|
| Service/host enumeration | any segment position, avahi tools or nmap scripts | yes | Host + service inventory — recon |
| mDNS poisoning (Responder MDNS) | segment position + clients doing .local lookups | yes | NTLM hash capture (with LLMNR/NBT-NS) — High |
| Spoofed service advertisement | clients auto-discovering services (print/IPP) | yes | Client connections to attacker services — High |
| Reflection/amplification | internet-exposed 5353/udp answering legacy unicast | yes (spoofed source) | DDoS reflection — report, don't run |
| Rogue responder registration | announcing names no one contests | yes | Persistent name hijack on segment — Medium |

## Phase 0 — Enumerate the segment

```bash
# from a foothold on the same segment:
avahi-browse -ar               # all advertised services, resolved (hosts, IPs, ports, TXT)
avahi-resolve -n printer.local # targeted name resolution
nmap -sn 198.51.100.0/24 --script broadcast-dns-service-discovery   # nmap's DNS-SD segment sweep
sudo tcpdump -i eth0 udp port 5353 -w mdns.pcap   # passive: catalog from chatter alone
```

DNS-SD types worth grepping from avahi-browse output: `_ipp._tcp`/`_ipps._tcp`
(printers — CUPS chain entry), `_smb._tcp`/`_adisk._tcp` (file shares, TimeMachine),
`_http._tcp` (admin panels), `_workstation._tcp` (hostnames + OS hints). The TXT records
carry model strings and path hints — pre-auth inventory with zero packets sent by you
(passive tap).

## Phase 1 — Poison with Responder (observed pattern)

```bash
sudo responder -I eth0          # poisoners: LLMNR [ON], NBT-NS [ON], MDNS [ON]
# all four observed capture workflows show this exact banner; MDNS poisoning
# answers .local queries for contended names; SMB file actions trigger lookups
```

Workflow: start Responder → induce name lookups (open file share, print dialog, browse
network in Explorer) → captured NTLMv2 hashes land in Responder's logs → crack/relay
(relay paths continue in [[smb]]). mDNS adds value exactly where LLMNR/NBT-NS are
disabled by policy — Unix-heavy and macOS segments still resolve `.local`.

## Phase 2 — Spoofed service advertisement

Answer browse queries instead of name queries: advertise `_ipp._tcp.local` or
`_http._tcp.local` pointing at your listener and clients' auto-discovery connects to you
(observed context: broadcast mDNS discovery is how victims found the malicious
printer). Non-destructive verify: advertise a service with a canary name, watch the
client connect, capture the handshake, stop. This is also the rogue-printer phishing
primitive — document the acceptance, don't exfiltrate print jobs.

## Phase 3 — Reflection check (internet-facing 5353/udp)

RFC 6762 §6.7: a query whose source port is not 5353 is a "legacy" querier, and the
responder MUST send a unicast reply to the packet's source address. A spoofable source +
a chatty responder = reflection. Test only against owned infrastructure: send a
legacy-style query (source port e.g. 53 or random) and observe the unicast reply's size
ratio; report exposed 5353/udp as the finding (amplification vector + internal name
disclosure to outsiders) without participating in actual reflection attacks.

## Phase 4 — Persistence and cleanup

A rogue responder can also *claim* names (probe three times per RFC 6762's tie-breaking,
then announce) — on segments where the real host is asleep/off, your announcement stands.
Cleanup discipline on engagements: stop advertising, confirm with a final avahi-browse
that your records aged out (TTL-driven), note any client caches that persisted.

## Phase 5 — Upstream tooling additions (2026-11-13)

**Enumeration from off-avahi hosts** — nmap carries two DNS-SD scripts: `dns-service-discovery`
runs against one target (`nmap -sU -p 5353 --script dns-service-discovery <target>`, the
QU-bit unicast-reply trick gets you an answer from a single host), while
`broadcast-dns-service-discovery` shouts at the segment and aggregates every responder's
service types and instances. On macOS, the native dns-sd client does the same interactively:
`dns-sd -B _services._dns-sd._udp` enumerates service types, `dns-sd -B _http._tcp` browses
one type, `dns-sd -L "Name" _ipp._tcp local.` resolves an instance to its SRV/TXT/A records.
For captures, tshark's mdns display filter beats raw tcpdump reading: `tshark -i <iface> -f
"udp port 5353" -Y mdns`, or `-Y 'dns.qry.name == "_services._dns-sd._udp.local"'` to watch
only service-list queries (a sudden surge in those is the impersonation indicator to
report).

**Pholus** (active mDNS/DNS-SD toolkit, python3 lineage — confirm flags against your
checkout): passive listen with a time budget (`pholus3.py <iface> -stimeout 60`), service
enumeration (`-sscan`), an explicit mDNS request (`--request`), and a reverse sweep that
resolves a whole subnet over mDNS (`-rdns_scanning 198.51.100.0/24`). Its `-afre` mode
auto-forges name-conflict responses during other hosts' probing — a name-squatting DoS that
blocks devices from registering: strictly a lab/maintenance-window technique.

**bettercap's zerogod module** is the industrial form of Phase 2's spoofed advertisement:
`zerogod.discovery on` catalogs services; `zerogod.show <ip>` dumps a host's advertisements;
`zerogod.impersonate <ip>` answers for a target host's services automatically; with
`zerogod.ipp.save_path` set, impersonating a printer writes incoming IPP jobs to disk
(evidence capture — hold to the Phase 2 discipline on print-job handling); `zerogod.save` /
`zerogod.advertise` replay captured service sets later.

**WebRTC note**: browsers replace local IPs in WebRTC ICE candidates with ephemeral mDNS
names; a `.local` candidate seen in signaling or a capture resolves only from the same link
and within that browser session's lifetime — treat it as a same-segment corroboration
signal, not a durable hostname.

**Implementation CVE notes (verified; DoS/local class — triage context, not remote
primitives):** Avahi reachable-assertion crashes CVE-2023-38469 through CVE-2023-38473
(Ubuntu USN-6487-1) kill avahi-daemon per-packet with recovery on restart — a nuisance-DoS
lever on discovery-dependent segments. Cisco IOS XE Wireless LAN Controller mDNS gateway
CVE-2024-20303 (GHSA-jfv4-r4r3-xx7w, high) lets an unauthenticated adjacent WLAN client
sustain CPU exhaustion that can tear down AP tunnels. Apple mDNSResponder CVE-2024-44183
(fixed iOS/iPadOS 18) and CVE-2025-31222 (fixed macOS 15.5) are local-impact issues on the
device, not remote mDNS attack surface.

## Command quick reference

```bash
avahi-browse -ar ; avahi-resolve -n NAME.local
nmap --script broadcast-dns-service-discovery -sn SUBNET
sudo responder -I IFACE
tcpdump -i IFACE udp port 5353
```

## Tool mapping (prose)

avahi-browse and avahi-resolve for enumeration from Linux footholds; nmap's
dns-service-discovery and broadcast-dns-service-discovery scripts for targeted and
segment-wide DNS-SD sweeps; Responder for poisoning (its
MDNS engine runs with the LLMNR/NBT-NS poisoners); tcpdump/Wireshark for passive
chatter cataloging. Tools vault owns per-tool pages.
