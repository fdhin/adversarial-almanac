---
technology: Check Point Firewall-1 (Quantum lineage)
kind: overview
audience: offensive-security
status: upstream-only
family: network-security
layer: perimeter firewall / VPN gateway / security management
homepage: https://www.checkpoint.com
provenance: "upstream-only; zero corpus evidence by design (owner-approved new-unit wave 2026-11-13) — verified against Check Point sk1000171 (CVE-2026-93616: affected Jumbo-hotfix takes, TCP/19009 guidance, IoC greps, LivePatch gap), sk182336 (CVE-2024-24919: fixed takes, gateway scope), sk181311 (CVE-2023-28130: Gaia Portal hostname injection, fixed takes), and Rapid7's CVE-2026-16232 technical analysis (18190 FWM/CPMI + 19009 CPM/DLE SOAP chain, SIC DN root cause, patch diff) + rewritten from HackTricks (src/network-services-pentesting/pentesting-264-check-point-firewall-1.md), CC BY-NC 4.0, accessed 2026-11-13"
---

# Check Point Firewall-1

> The stateful-inspection dynasty whose gateway still answers an unauthenticated topology query on 264/tcp with its own name and its management server's — and whose dual-generation management plane has become the mass-exploitation story of the moment.

## What it is

Check Point's Firewall-1 created stateful inspection in the 1990s; the product line runs today as Quantum Security Gateways on the Gaia OS, configured through SmartConsole against a Security Management Server (the old SmartCenter), with Multi-Domain Management scaling the same idea for large estates. The remote-access lineage is equally long: SecuRemote/SecureClient clients downloaded gateway **topology** from the `FW1_topo` service on **TCP 264**, a flow the current Endpoint Security VPN machinery still inherits; browser-launched SSL Network Extender sessions on 443 carried the clientless-VPN era before the Mobile Access blade and modern clients replaced them. That history matters because the same ports and name services still answer — 264 remains a pre-auth oracle for gateway and management names.

The management plane is where the damage happens, and it is two generations deep. The legacy **FWM/CPMI** service on **TCP 18190** speaks SIC (Check Point's certificate-based internal trust) with binary FwSet objects; the modern **CPM/DLE** service on **TCP 19009** exposes SOAP endpoints under `/cpmws/`, with sessions carried in `DLESESSIONID`/`CLIENTSESSIONID` headers. A SmartConsole login crosses *both* — which is exactly the seam the 2026 authentication-bypass and traversal chains exploited. Further control services (per the vendor's port matrix for control connections: CPD, ICA pull/push, policy-server login, ICA services) cluster on the management host, and the **"Accept Control Connections" implied rule** opens several of them regardless of what the visible rulebase says.

The operator takeaway: this is a perimeter device that leaks its own management topology pre-auth, whose control-plane exposure is governed by implied rules an operator may never have read, and whose management server — one network hop from the gateway — carries internet-scale RCE history (CVE-2026-93616, CVSS 9.8, exploited in the wild).

## The anatomy that matters on an engagement

    internet → gateway
      264/tcp  FW1_topo:   pre-auth topology → gateway + management hostnames, cert CN/O
      443/tcp  VPN portals (Mobile Access / legacy SSL Extender lineage)
      500/4500/udp IKE     → see [[ipsec-ike-bug-bounty]]
    foothold (VPN segment, jump host, mgmt VLAN) → management plane
      18190/tcp FWM/CPMI   binary FwSet over SIC certificates
      19009/tcp CPM/DLE    SOAP under /cpmws/ — DLESESSIONID sessions
      18191/18210/18211/18231/18264  CPD, ICA pull/push, policy server, ICA services
    admin creds → Management API (mgmt_cli / web_api) → full policy control
                  Gaia API (separate, OS-level, on gateway or management)

Three structural properties drive findings: (1) pre-auth naming leaks on the gateway point directly at the management host that matters; (2) implied rules — not the visible policy — decide control-plane reachability, so rescanning from every new foothold pays; (3) two protocol generations share trust and sessions, making identity-confusion and cross-protocol token reuse the recurring bug class (CVE-2026-16232 is the textbook).

## Where you'll meet it

- **Enterprise and government perimeters** — the gateway is the internet edge, and 264/443 are the internet-facing surfaces.
- **Internet-exposed remote-access VPN gateways** — the CVE-2024-24919 disclosure and exploitation wave lived exactly here.
- **OT/SCADA-adjacent deployments** — the vendor ships industrial-firewall variants, so expect Check Point management VLANs brushing process networks; the topology leak names the nodes before you ever touch them.
- **Multi-domain estates (MDS)** — the same services multiplied per domain, with the same takes and the same gaps.

## Reading the framework's mind

1. A printf-sized probe on 264 returns the gateway certificate `CN` and `O` — object name plus Internal CA/management naming context (see [[check-point-bug-bounty]] Phase 0).
2. The leaked management hostname is the pivot target for 18190/19009 — the CVE-2026-16232 chain and the CVE-2026-93616 traversal both live on that host.
3. Trusted Clients configuration is the real auth boundary for legacy management access; historically it did not restrict GUI clients by default — verify, don't assume.
4. Treat gateway and management as one chain: pre-auth gateway leaks (CVE-2024-24919) feed username discovery and credential attacks, whose prize is Gaia Portal command injection (CVE-2023-28130) or SmartConsole itself.
5. The IKE surface is its own battlefield — aggressive-mode and auth-flag issues — covered in [[ipsec-ike-bug-bounty]], which carries the Check Point IKEv1 case.

## Where to go next

The companion methodology: [[check-point-bug-bounty]] — the 264 topology oracle, control-plane port sweeps, the 2026 management-server chains with version gates and detection, and the authenticated Management API.
