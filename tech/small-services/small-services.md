---
technology: Small services (whois, ident, echo)
kind: overview
audience: offensive-security
status: upstream-only
family: legacy-network
layer: inetd-era TCP/UDP micro-services
provenance: "upstream-only; zero corpus evidence by design (owner-approved new-unit wave 2026-11-13) — ident semantics verified against RFC 1413 (query/response format, error types, explicit not-authentication warning) and nmap nsedoc for auth-owners; whois script semantics against nmap nsedoc for whois-ip (whodb values incl. nocache) with RFC 3912 as the protocol constant; echo/chargen abuse verified against CERT advisory CA-1996-01 (packet storm, disable/filter guidance) + rewritten from HackTricks (src/network-services-pentesting/43-pentesting-whois.md, 113-pentesting-ident.md, 7-tcp-udp-pentesting-echo.md), CC BY-NC 4.0, accessed 2026-11-13"
---

# Small services (whois, ident, echo)

> Three inetd-era daemons, one page: whois still front-ends registries people trust blindly, ident still answers "who owns this connection" for protocols that believe it, and echo still reflects packets for free.

## What it is

The classic UNIX host ran a handful of trivially small services out of `inetd`: **echo** (port 7, TCP and UDP) returned whatever it received, **ident** (port 113) answered the question "which local user owns this TCP connection", and **whois** (port 43) served registry lookups over a CRLF-terminated text exchange with connection-close marking the end of the response. None of them authenticate, none of them encrypt, and none of them were designed against an adversary — which is precisely why each one is still worth a probe when it shows up on a modern engagement.

They are one unit because the engagement logic is shared: individually trivial, jointly a reminder that "legacy small service" is a trust decision somebody made decades ago that the network never revisited. Ident is the trust oracle — the r-services world consulted it as a secondary signal (see [[r-services]], and the sibling user-disclosure service [[finger]]); whois is recon plumbing plus an injection surface wherever a custom gateway fronts a database; echo is a reflection and packet-storm primitive in the same family as the amplification sources documented under [[memcached]].

Dispatch table — the engagement's first question is which of the trio is open, and whether it is a stock daemon or a custom gateway wearing the port number:

| Port | Service | Why it still matters |
| --- | --- | --- |
| 43/tcp | whois | Recon and referral chasing; custom gateways proxy queries into databases (injection); rogue/stale whois servers poison tools that still trust the protocol |
| 113/tcp | ident | User and service-account enumeration; historically consulted as a trust signal by r-services and mail daemons despite being explicitly not authentication |
| 7/tcp+udp | echo | UDP reflection/amplification source; pairs with chargen into the CERT-documented packet-storm denial of service |

## The anatomy that matters on an engagement

    whois (43/tcp):  query CRLF → text answer → connection close ends the record
      stock: registry data; custom: a parser + backend database (SQL/LDAP/HTTP)
      referrals: "Whois Server:" points at the next server to ask
    ident (113/tcp): "<port-on-server>, <port-on-client>" CRLF
      → "pair : USERID : <os> : <user>"  or  "pair : ERROR : <why>"
      queryable only about connections the target host can see
    echo (7): TCP echoes the stream; UDP echoes each datagram
      chargen ↔ echo over UDP = self-sustaining packet storm

Three structural properties drive findings: (1) each service answers before any authentication could exist — the only question is what the answer leaks; (2) the interesting bug surface is never the stock daemon, it is the custom gateway or the consumer that trusts the response; (3) the trio lives longest on legacy UNIX, embedded firmware, and OT/ICS-adjacent networks where nothing ever gets decommissioned.

## Where you'll meet it

- **Legacy UNIX and appliance fleets** — identd and echo linger on old hosts, print servers, and device firmware long after the services stopped being useful.
- **Registrars, RIRs, and internal asset registries** — whois servers fronting IP/domain/ASN data, sometimes with homegrown backends.
- **OT and ICS-adjacent segments** — small services survive on engineering workstations and historians that predate the security team.
- **Anything with an old admin script** — automation that still queries ident or whois and makes decisions from the answer is the real attack surface, not the daemon.

## Reading the framework's mind

1. A whois port that answers domain, IP/CIDR, and ASN queries is usually a gateway with per-type backends — each backend is a separate injection target (see [[small-services-bug-bounty]] whois section).
2. Ident queries only make sense about live connections: open a connection to the target's service from a known source port, then ask ident about that port pair — the answer names the account behind the service.
3. RFC 1413 says in its own security considerations that ident is not authentication and may be forged or misleading — quote that at anyone using it as a trust decision, then enumerate with it anyway.
4. nmap's `auth-owners` NSE turns one ident responder into an owner map of every open TCP port on the host — the single highest-yield query in the trio.
5. An open UDP echo joins the reflection family; an open UDP echo *plus* a chargen anywhere is the CERT-documented packet storm — report both, recommend disabling the diagnostic UDP services outright.

## Where to go next

The companion methodology: [[small-services-bug-bounty]] — per-service sections: whois enumeration, gateway injection, and rogue-server abuse; ident user enumeration and the trust-oracle pattern; echo reflection and amplification.
