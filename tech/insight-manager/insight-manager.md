---
technology: "HP System Management Homepage (SMH) and HP Systems Insight Manager (SIM)"
kind: overview
audience: offensive-security
family: network-services
layer: "host-OS web management agent (TCP/2301 HTTP autostart, TCP/2381 HTTPS) — the in-band agent, not the out-of-band BMC"
language: "C/PHP (SMH web stack)"
standards: "HP/HPE SMH 2.x–7.x line; SIM 7.x management console"
evidence: "zero corpus evidence by design — upstream-only new unit (bucket-2, owner-approved)"
status: upstream-only
source: "rewritten from HackTricks (src/network-services-pentesting/pentesting-compaq-hp-insight-manager.md), CC BY-NC 4.0, accessed 2026-11-13"
provenance: "upstream-only: zero corpus evidence by design (new-unit bucket-2; built from the upstream reference plus primary sources, not the writeup corpus). Upstream-verified 2026-11-13: HPE SMH 7.2 Installation Guide (per reference: 2301 HTTP/autostart, 2381 HTTPS; a 2301 request may start the web service and redirect to 2381); HPE StoreEasy/SM documentation (per reference: SMH on Windows authenticates OS accounts; host admins receive SMH privileges — no universal product default). Traversal/advisory history verified against primary records: HP security bulletin SSRT061118 rev.1 (2006, via seclists bugtraq archive) — SMH 2.0.0–2.1.4 on Windows, directory traversal via the bundled Namazu search CGI 'lang' parameter (namazu.cgi), unauthorized file access, vendor fix = .namazurc edit; Nessus plugin 20988 corroborates; CERT/CC VU#735364 (published 2013-06-10) — authenticated command injection in ginkgosnmp.inc via /smhutil/snmpchp.php.en on 2381 (Apache MultiViews path-appendix reaches exec(); '/'/'<'/'>'/'|' filtered), REMOTE AUTHENTICATED impact (CVSS 9 AV:N/AC:L/Au:S/C:C/I:C/A:C), fixed in SMH 7.2.2 — no CVE ID listed on the VU record; NVD CVE-2012-2014 (SMH < 7.1.1, remote authenticated, unspecified impact) and CVE-2012-2016 (SMH < 7.1.1, local sensitive information) one-line records. Config-file loot list per reference's HPE documentation citation. Rewritten from HackTricks (src/network-services-pentesting/pentesting-compaq-hp-insight-manager.md), CC BY-NC 4.0, accessed 2026-11-13."
---

# HP System Management Homepage / Systems Insight Manager (SMH/SIM)

> The management web page that runs on the server's own operating system. Three HP products get confused under one banner here — knowing which one answered your scan decides whether you attack host credentials, a web agent's CGI history, or a BMC.

## What it is

**HP System Management Homepage (SMH)** — formerly Compaq Insight Manager agent in its browser-facing role — is a per-host web agent installed on ProLiant servers (Windows, Linux, HP-UX). It listens on **TCP/2301 (HTTP)** and **TCP/2381 (HTTPS)**; depending on startup mode a request to 2301 autostarts the web service and redirects to 2381. It surfaces health, firmware, and management agents for that one machine.

The adjudication that matters on an engagement, because the three get conflated constantly:

- **SMH** (2301/2381) is **in-band**: a web application inside the host OS, tied to the OS user database.
- **HP Systems Insight Manager (SIM)** is the central console that aggregates many SMH agents — a separate server product, not something you find by hitting 2381.
- **iLO** is HP's Baseboard Management Controller — out-of-band hardware with its own NIC and web interface, a completely different attack surface covered in [[ipmi]].

Seeing 2301/2381 means the host OS web agent; do not report it as "iLO exposed" and do not expect BMC behavior.

On authentication: **there is no universal product default password to try**. HPE's documentation for Windows deployments has SMH authenticate operating-system accounts, with host administrators receiving corresponding SMH privileges. The realistic findings are anonymous-access misconfigurations and weak or reused host credentials — which is also why SMH is a credential-validation oracle for the host itself: a login here is a host-account login. Historically the product shipped optional anonymous/guest modes on some configurations; test for them rather than assuming.

## The anatomy that matters on an engagement

    TCP/2301 ──autostart/redirect──► TCP/2381 (HTTPS)
        ▼
    SMH login page  →  OS account credentials (host user database)
        ▼
    per-agent pages: health, agents, SMH utilities (/smhutil/...)
        ▼
    historical weak spots: bundled CGI (search engine), /smhutil PHP utilities

Structural properties that drive findings:

1. **Auth maps to the host, not the product.** Attack surface = the host's account hygiene; credential reuse discovered elsewhere lands here.
2. **The web stack is ancient and bundled.** Decades of shipped third-party CGI and PHP utilities (the Namazu search engine, SNMP helpers under `/smhutil`) left a version-gated vulnerability trail — old installs keep it all.
3. **Port confusion creates false scope.** 2301/2381 ≠ iLO; mixing them wastes the credential attacks on the wrong target.

## Where you'll meet it

- **Legacy ProLiant estates** — VMware hosts, aging Windows file servers, HP-UX — where SMH was installed at build time and never revisited; anything still on a 7.1.x or earlier SMH carries the pre-7.2 advisory surface.
- **Managed-service networks** where SIM consoles fan out to hundreds of SMH agents; one weak host credential equals estate-wide visibility.
- **Bastion/restricted segments** where 2381 is one of the few reachable ports on a server VLAN.

## Reading the framework's mind

1. Probe 2301 first even when scanning 2381 — the autostart-and-redirect behavior is itself a fingerprint of a live SMH.
2. No product default password exists; try anonymous/guest access modes, then validated host credentials. Report exposure + weak host credentials, not "default creds".
3. Version-gate any advisory before firing it: the Namazu `lang` traversal applies to SMH 2.0.0–2.1.4 on Windows; the `/smhutil/snmpchp` command injection applies to pre-7.2.2 SMH and — critically — requires an authenticated session.
4. Post-shell, SMH's own files are the loot map: `path.properties`, `mx.log`, `CLIClientConfig.cfg`, `database.props`, `pg_hba.conf`, `jboss-service.xml`, `.namazurc` — names and locations vary by version and OS.
5. The HTTP surface is a mirror of host-agent state: agent names on the page tell you which management agents (and which HP-UX/Linux/Windows stack) you are dealing with. Workflow in the [[insight-manager-bug-bounty|methodology]].

## Where to go next

- [[insight-manager-bug-bounty|SMH/SIM bug bounty methodology]] — fingerprinting, the auth model, version-gated advisory history, config loot.
- Related units: [[ipmi]] (the actual BMC/iLO surface people confuse with this one), [[sccm]] and [[wsus]] for the rest of the Windows management estate.
