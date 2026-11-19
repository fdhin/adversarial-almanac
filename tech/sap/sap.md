---
technology: "SAP"
kind: overview
audience: offensive-security
family: network-services
layer: "ERP application estate: ABAP/Java instances with per-instance port families (dispatcher 32NN, gateway 33NN, ICM HTTP 5NN00, sapstartsrv 5NN13/14), Host Agent SOAP (1128/1129), SAProuter gateway (3299), RFC trust webs"
language: ABAP / Java
vendor: SAP SE (proprietary; NetWeaver is the application server stack)
homepage: "https://www.sap.com"
evidence: "zero corpus evidence by design — upstream-only new unit (bucket-2, owner-approved)"
status: upstream-only
source: "rewritten from HackTricks (src/network-services-pentesting/pentesting-sap.md; src/network-services-pentesting/3299-pentesting-saprouter.md as the SAProuter section), CC BY-NC 4.0, accessed 2026-11-13"
provenance: "upstream-only: zero corpus evidence by design (new-unit bucket-2; built from the upstream reference plus primary sources, not the writeup corpus). Upstream-verified 2026-11-13: default-credential list cross-checked against the shipcod3/mySapAdventures methodology repo (SAP*:06071992, DDIC:19920706, EARLYWATCH:SUPPORT:066, TMSADM:PASSWORD/$1Pawd2&, SAPCPIC:ADMIN, IDEADM:admin, SOLMAN/SAPSUPPORT init1234, developer-edition passwords); CVE-2022-27668 verified against the SEC Consult advisory (SAP Note 3158375; NI_ROUTE tunnel to 0.0.0.0:3299 → ROUTER_ADM admin commands; affected SAP_ROUTER 7.22/7.53, KERNEL 7.49/7.77/7.81/7.85–7.88, KRNL64NUC/UC 7.49; workaround = no wildcard targets in saprouttab P/S entries); Metasploit module names verified against the rapid7/metasploit-framework tree (auxiliary/scanner/sap/*, exploit/multi/sap/*). Rewritten from HackTricks (src/network-services-pentesting/pentesting-sap.md; src/network-services-pentesting/3299-pentesting-saprouter.md as the SAProuter section), CC BY-NC 4.0, accessed 2026-11-13."
---

# SAP

> An ERP estate where every instance wears its port number on its sleeve (dispatcher 32NN, gateway 33NN, HTTP 5NN00, router 3299) and where `SAP*` — the built-in superuser — shipped for years with the same default password, `06071992`. Find one unpatched perimeter component and the business's whole back office is behind it.

## What it is

SAP is both the German enterprise-software company and its ERP system: integrated modules covering finance, HR, supply chain — the application layer that runs large enterprises. An engagement-facing mental model has three tiers per **SID** (system identifier): database, application server (AS ABAP and/or AS Java under NetWeaver), and the SAP GUI thick client. Landscapes clone into development / test / QA / production, and instances are numbered (`00`, `01`, …) — that **instance number NN is the Rosetta stone for the port map**: dispatcher `32NN`, gateway `33NN`, ICM HTTP `5NN00` (instance 00 → 50000), sapstartsrv/sapcontrol `5NN13`/`5NN14`, with the SAP Host Agent on 1128/1129 (HTTP/HTTPS SOAP) and SAProuter on 3299. Compromising the database layer is maximum impact, but every layer has its own default-credential and legacy-exploit story.

Inside each instance, data and users are partitioned into **clients** (three-digit mandants — 000, 001, 066 …), and each client carries a `SAP*` user, the application-level superuser. On older installations `SAP*` shipped with default password `06071992` (its siblings: `DDIC:19920706`, `EARLYWATCH:SUPPORT:066`, `TMSADM:PASSWORD`, `SAPCPIC:ADMIN`) — the credential set that opens unmanaged production systems to this day. At the OS layer, each instance is administered as `<SID>adm` — a shell account worth password-guessing where scope allows (watch for lockout controls).

The attacker's mental model: **enumerate by instance number, authenticate with legacy defaults, pivot through RFC trust and SAProuter**. SAP estates grow by accretion — SolMan monitoring boxes, PI/PO integrations, transport systems — and the trust links between them (RFC destinations in transaction SM59) mean one weak system often inherits the authority of its better-protected neighbours.

## The anatomy that matters on an engagement

    perimeter: SAProuter :3299 (saprouttab ACL) ──pivot──► internal instances
        ▼
    port families per instance NN: 32NN dispatcher · 33NN gateway · 5NN13/14 sapcontrol · 5NN00 ICM HTTP
    Host Agent :1128/:1129 SOAP — /SAPHostControl/?wsdl, GetComputerSystem
        ▼
    auth: SAP GUI (sapgui HOST NN) · web :50000 (/irj/portal, /sap/public/info, /ctc ConfigServlet)
    legacy defaults: SAP*:06071992 · DDIC:19920706 · EARLYWATCH:SUPPORT:066 · TMSADM · SAPCPIC
        ▼
    post-auth: tcodes (SU01, SM59 …) · RFC_READ_TABLE / SXPG_* command exec · SM59 trusted-RFC pivot
        ▼
    parameter audit (RSPFPAR): auth/rfc_authority_check, gw/reg_no_conn_info, login/min_password_lng …

Structural properties that drive findings:

1. **Port arithmetic** — once you learn `NN` (from any open `32NN`/`33NN`/`5NN13` port or the Host Agent), the whole instance's surface resolves; Metasploit's discovery modules automate exactly this.
2. **Default credentials as a genre** — the kernel-level `SAP*` account and the delivery users (`DDIC`, `EARLYWATCH`, `TMSADM`, `SAPCPIC`, SolMan's `init1234` set, developer-edition passwords) are decade-old, still tested on every unmanaged box. Production default creds rate P1 on Bugcrowd's taxonomy.
3. **RFC is the lateral movement fabric** — trusted destinations (SM59), `RFC_READ_TABLE` for data, `SXPG_CALL_SYSTEM`/`SXPG_COMMAND_EXECUTE` for OS commands, and callback abuse (`rfc/callback_security_method`) let a foothold in one system execute in another.
4. **SAProuter is the estate's real firewall** — route-permission-table ACLs decide what the outside world may reach; misconfigured wildcard routes turn it into an open pivot (see the section below).

## Where you'll meet it

- **Internet-facing SAProuters on 3299** — support remote-access gateways, frequently with permissive `saprouttab` entries; the pivot into the internal SAP estate.
- **NetWeaver portals and Java stacks on :50000** — `/irj/portal` logon pages, `/wsnavigator` test pages, ICM paths fuzzable with the SecLists SAP wordlists; legacy RCEs like ConfigServlet still land on unpatched stacks.
- **Host Agent on 1128/1129** — unauthenticated SOAP (`GetComputerSystem`) leaking host, OS, database, instances and interfaces before any login.
- **Internal ERP after the first foothold** — default creds, weak profile parameters, and RFC webs; nobody expects the ERP to be in scope, so coverage is thinnest exactly where impact is highest.

## Reading the framework's mind

1. Instance number NN unlocks the map: scan `3200-3299`, `5NN00`, `5NN13/14`, `1128/1129`, `3299` — or let `auxiliary/scanner/sap/sap_service_discovery` do it.
2. `/sap/public/info` answers unauthenticated with an `RFC_SYSTEM_INFO` XML: system ID, kernel release, database engine, OS, IP — build the attack graph before touching a login.
3. The default-credential canon is stable and small (see [[sap-bug-bounty|methodology]] table); try `SAP*:06071992` and `DDIC:19920706` in clients 000/001 first.
4. Profile parameters read as a misconfiguration checklist — `gw/reg_no_conn_info < 255`, `auth/rfc_authority_check < 2`, `login/min_password_lng < 8`, `icm/security_log = 2` are the classics (full table in the methodology).
5. A reachable SAProuter is a port scanner and proxy aimed at the internal network: enumerate through it (`sap_router_portscanner`), then tunnel (`Proxies sapni:...`).

## SAProuter — the perimeter gateway (section)

SAProuter is an application-level gateway for SAP's own protocols: TCP/3299 by default, routing decisions from the `saprouttab` permission table (`P` permit / `S` secure entries — source, target host, target service), deliberately complementing rather than replacing a firewall. Operators historically expose it to give SAP support remote access, which makes it the perimeter component an attacker meets first.

The pivot workflow (Metasploit, from Rapid7's research): `sap_service_discovery` to confirm → `sap_router_info_request` for router/config details → `sap_router_portscanner` (`INSTANCES 00-50`, `PORTS 32NN` or `MODE TCP`, `PORTS 80,32NN`) to map internals through the router → blind hostname guessing when info requests are refused → any module run through an NI proxy (`set Proxies sapni:198.51.100.10:3299`) so the framework reaches internal targets via the router.

The headline bug — **CVE-2022-27668** (SAP Note 3158375, SEC Consult): SAProuter refuses routes to its own loopback *by hostname* (127.0.0.1 filtered), but an `NI_ROUTE` request targeting the unspecified address **0.0.0.0** on port 3299 slips the filter. That builds a tunnel to the router's own loopback interface, granting local-host privileges — and `ROUTER_ADM` administrative packets follow: stop the router, change trace levels, kill connections, all unauthenticated and even when the router runs *without* the `-X` remote-administration flag. Preconditions: a `saprouttab` `P`/`S` entry permitting connections to port 3299 of arbitrary hosts (any wildcard-target entry). Affected: stand-alone SAProuter 7.22/7.53 and kernels 7.49, 7.77, 7.81, 7.85–7.88 (incl. KRNL64NUC/UC 7.49). Reproduce with pysap's `router_portfw.py` (build the 0.0.0.0 tunnel) and `router_admin.py` (`-s` stops the router); mitigate by patching and deleting wildcard targets from `saprouttab`.

Hardening that reads as recon signals: SNC-protected routing (`-K <snc-name>`, `KT`/`KP`/`KD`/`KS` entries between chained routers), remote administration disabled (no `-X`), interface-specific bind (`-H`), and `dev_rout` monitoring for `ROUTER_ADM` packets or `NI_ROUTE` requests to `0.0.0.0`. nmap can fingerprint versions with a custom probe matching the `SAProuter <ver>` banner string on 3299.

## Where to go next

- [[sap-bug-bounty|SAP bug bounty methodology]] — discovery modules, the credential table, GUI/web/RFC abuse, parameter audit, the SAProuter pivot.
- Adjacent enterprise surfaces: [[ldap]] for directory-backed landscapes, [[mssql]]/[[oracle-db]]-style database tiers behind the application layer.
