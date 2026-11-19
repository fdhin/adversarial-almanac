---
technology: "SNMP"
kind: overview
audience: offensive-security
family: network-services
layer: management protocol (161/udp agent, 162/udp traps; v1/v2c/v3)
standards: "RFC 1157 (v1), RFC 3416 (v2c operations), RFC 3414 (v3 USM); NET-SNMP-EXTEND-MIB, CISCO-CONFIG-COPY-MIB"
status: confirmed
provenance: "evidence-grounded (12 files under command-context gate: snmpwalk/snmpset/onesixtyone/snmp-check/community markers) + upstream-verified (net-snmp snmpd.conf(5) extend/exec semantics and NET-SNMP-EXTEND-MIB dynamic SET configuration; Cisco document 15217 copy-config-via-SNMP with CISCO-CONFIG-COPY-MIB). Enrichment pass 1, 2026-11-12."
---

# SNMP

> A UDP management plane that answers detailed questions about the host — processes, interfaces, installed software, even "run this command and tell me the output" — to anyone holding a default or brute-forceable community string that is usually `public`.

## What it is

SNMP is the standard protocol for asking networked devices about themselves: a management **agent** on 161/udp exposes a tree of OIDs (the MIB), a manager walks or sets values. v1 and v2c have no real authentication — the entire credential model is a plaintext **community string** (read-only `rocommunity`, read-write `rwcommunity` in net-snmp's snmpd.conf), and requests ride UDP with no transport security. v3 fixed this with USM users and auth/privacy (HMAC + DES/AES), and its absence is the finding. Because agents exist to *report*, they leak: system description, process tables, interfaces, routes, installed software, open files — and net-snmp's extension mechanism lets operators expose arbitrary command output as OIDs, which attackers read or, with a write string, create.

The operator takeaway: 161/udp open + v1/v2c means an information disclosure engine keyed to a string. Default `public` still ships on printers, embedded kit, and lazy servers; brute force closes the rest. A **rw** string converts the recon plane into command execution.

## The anatomy that matters on an engagement

    manager → 161/udp → snmpd → MIB walk (rocommunity)  → sysDescr, processes, interfaces, storage
                          └→ SET (rwcommunity) → nsExtendConfigTable rows → agent runs the command
    device agents (Cisco, printers) → rw string → config copy (CISCO-CONFIG-COPY-MIB) → startup-config

Three structural properties drive most findings:

- **The community string is the whole auth model** (v1/v2c) — default `public`/`private`, dictionary words, or company names; no lockout on most agents, and UDP makes spraying cheap.
- **The agent is a configured informer.** Operators extend net-snmp with `extend`/`exec` directives that run commands on your behalf when you read the right OID; snmpd.conf(5) documents that `extend`/`extendfix` entries can even be **created dynamically via SNMP SET** to NET-SNMP-EXTEND-MIB — a documented feature that doubles as the RCE when the string is writable (rwcommunity + nsExtendObjects walk observed; brute-forced string → blind RCE observed; monitoring extend script hijack observed).
- **On network gear, write access is config access.** Cisco's own documentation covers pulling the running-config over SNMP with a rw community through CISCO-CONFIG-COPY-MIB (which replaced the deprecated config section of OLD-CISCO-SYSTEM-MIB) — credentials, SNMP strings, and routing config in one file.

## Where you'll meet it

- **Printers and embedded devices** (HP JetDirect, observed): `public` community discloses config — including the device's telnet/admin password (one chain read the JetDirect password OID and hex-decoded it).
- **Linux servers with net-snmp** (repeatedly observed): walks leak users, processes, services; extend/exec directives turn a string into shells.
- **Monitoring infrastructure** (observed): SNMP feeds Nagios-style systems — the walk exposes what is monitored, and the monitoring credentials often follow.
- **Network gear** (observed): Cisco IOS agents, version strings, interface maps; rw strings = config theft.
- **Windows services for SNMP**: valid OIDs for installed software and hotfixes; sometimes the TCP/IP routing table and even writable services.

## Reading the protocol's mind

1. **`snmpwalk -v2c -c public host` is the single highest-yield UDP probe** — a `sysDescr.0` answer means the whole MIB is next; multiple observed chains start exactly there.
2. **Version strings come free** — sysDescr on devices names OS/firmware (observed: "SNMPv1 server; pysnmp SNMPv3 server (public)").
3. **nsExtendObjects (.1.3.6.1.4.1.8072.1.3.2) enumerates operator-configured commands** — walking it lists every command the agent will run, by name, before you run anything.
4. **rwcommunity turns reads into writes into execution** — snmpd.conf(5) allows creating extend rows via SET; that is the documented mechanism, not a CVE.
5. **v3 with USM is the wall** — a probe that answers v1/v2c with `public` but nothing else has no USM users worth guessing; move on unless hashes leak elsewhere.

## Where to go next

[[snmp-bug-bounty|SNMP bug bounty methodology]] — string brute force, OID walk targets, rw write attacks, the net-snmp extend RCE, and Cisco config theft.
