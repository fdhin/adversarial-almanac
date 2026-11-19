---
technology: "SolarWinds Orion & Access Rights Manager"
kind: overview
audience: offensive-security
status: upstream-only
family: monitoring
layer: IT management / monitoring software
homepage: https://www.solarwinds.com
provenance: "upstream-only (HackTricks + primary sources); zero corpus evidence by design — owner-approved bucket-2 new-unit wave 2026-11-13; CVE-2026-28326 gate verified against Bishop Fox research + BishopFox/CVE-2026-28326-check README + SolarWinds ARM 2026.2.1 release notes; SUNBURST version history per SolarWinds security-advisory FAQ / CISA ED 21-01 + rewritten from HackTricks (src/network-services-pentesting/55555-pentesting-solarwinds-arm.md), CC BY-NC 4.0, accessed 2026-11-13"
---

# SolarWinds (Orion platform & Access Rights Manager)

> Monitoring and management software that runs privileged, credentialed, and network-adjacent everywhere — the product family whose own build pipeline was the 2020 supply-chain event, and whose Access Rights Manager still ships a gRPC port on 55555 that deserializes .NET Remoting as SYSTEM when its auth fallback is abused.

## What it is

SolarWinds is the vendor behind the **Orion Platform** — the network/infrastructure monitoring suite (NPM, SAM, NCM...) ubiquitous in mid-market enterprises, MSPs, and government networks — and a stack of adjacent management products including **Access Rights Manager (ARM)**, which inventories and analyzes NTFS/share/Active Directory permissions. Both products sit exactly where an operator wants to land: on servers that hold credentials for every network device and domain they watch, running agents and jobs with high privilege, reachable on management VLANs.

The **2020 supply-chain event** (SUNBURST) is threat-model context, not an engagement technique: attackers compromised SolarWinds' build system and distributed trojanized Orion Platform updates through the vendor's own trusted channel — affected builds span the 2019.4 HF5 through 2020.2.1 range, with the first clean releases per SolarWinds' advisory FAQ being 2019.4.2 and 2020.2.4 (CISA emergency directive ED 21-01 drove the response). What it permanently teaches about this product class: the attack surface of monitoring software includes its **update and build channel**, because the product's privilege (domain-joined, credentialed to everything, present on every segment) transfers to whoever controls its code. For a pentester the practical corollaries are defensive and analytic: these platforms should be treated as tier-0-adjacent, their service accounts and update paths audited, and any SolarWinds-shaped surface enumerated with that weight in mind. The same reasoning is why the vault carries a [[monitoring]] area at all.

**ARM's** attack surface is concretely enumerable: its components talk to each other over a TLS **gRPC listener on TCP 55555** (HTTP/2 with ALPN `h2` — a probe that skips ALPN may eat a reset and misreport the service dead). Under the gRPC framing, ARM transports legacy **.NET Remoting** messages and deserializes them with `BinaryFormatter` — the single most dangerous deserializer in the .NET ecosystem. The service runs as **LocalSystem** by default, so any code execution in that process is host SYSTEM plus whatever credentials and delegated access ARM holds.

## The anatomy that matters on an engagement

    ARM deployment: server + collectors + clients
      TCP 55555  TLS gRPC (ALPN h2) between components
        /SolarWinds_ARM_GRPC_ConnectionAuthrization.../GetCertificate   ← misspelling is real
        /SolarWinds_ARM_Remoting.Remoting/Send                          ← BinaryFormatter sink
      intended auth: client certificate — but handshake completes WITHOUT one
      CVE-2026-28326 fallback: HMAC token from a product-shipped shared key,
        MAC over caller-controlled values only (no nonce/challenge/channel/body binding)
        → forge token offline → server adopts a registered client cert thumbprint
          from its own store (present on default installs) → caller == that client
        → Send → BinaryFormatter → denylist-evasion gadget chain → LocalSystem

Structural properties that drive findings: (1) requested-but-not-required mTLS — the handshake lets you in and the application layer was supposed to stop you; (2) a server that upgrades a weak fallback credential into a strong identity by lending out its own certificate; (3) denylist-based gadget filtering on a deserializer whose type space is unbounded.

## Where you'll meet it

- **Enterprise management VLANs and MSP tool stacks** — Orion polling servers, ARM boxes beside the DCs they audit; management-plane reach usually equals finding scope.
- **Firewall-rule drift** — ARM wants 55555 between its own components, but a broad internal allow rule turns an adjacent-only issue into a network-reachable service (the listener can bind every interface, IPv6 included).
- **Post-compromise IR work** — the patch-state check and forensic traces below matter as much to blue teams verifying exposure as to attackers scanning for vulnerable builds.

## Reading the framework's mind

1. CVE-2026-28326 (fixed in ARM 2026.2.1; builds 2026.2.0.42 and earlier affected, 2026.2.1.7+ patched) is unauthenticated pre-RCE-reachable when 55555 is network-exposed: forge the fallback HMAC, get adopted as a registered client, deliver a .NET gadget to the `Remoting/Send` sink.
2. The gRPC verdicts are behavioral fingerprints: pre-patch, `Remoting/Send` rejects with gRPC status 14 (UNAVAILABLE) while `GetCertificate` answers 0 (OK); post-patch, anonymous `Send` gets 16 (UNAUTHENTICATED) with a "register your application" message.
3. The fix is structural: shared-key fallback removed, localhost-only child processes get a per-boot GUID token instead — so patched boxes cannot be probed with the old key at all.
4. Rejected anonymous attempts leave `Unable to validate remote request token` in ARM debug logs; successful forgeries can be quieter because auth returns early.
5. On builds with the GrantMA service, `GET /api/armconfig/network/Connections` is the evidence ledger: a `NetworkConnectionEntry` with `IsAuthenticated: false` and a populated `ClientCertThumbPrint` matches the bypass — memory-only, three-day expiry, cleared on restart, so collect before upgrading.

## Where to go next

The companion methodology: [[solarwinds-bug-bounty]] — safe patch-state detection on 55555, the exploitation chain in order, and the forensic/hardening checklist. Sibling context: [[monitoring]] for the product class; [[windows-secrets]] for what LocalSystem on a management box yields.
