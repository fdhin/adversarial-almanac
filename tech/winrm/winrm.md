---
technology: "WinRM / WS-Management"
kind: overview
audience: offensive-security
family: windows
layer: protocol
language: "c++ (service); ruby, python clients"
homepage: "https://learn.microsoft.com/windows/winrm/"
ht-source:
  - "rewritten from HackTricks (src/network-services-pentesting/5985-5986-pentesting-omi.md), CC BY-NC 4.0, accessed 2026-11-13"
provenance: "evidence-grounded (87 files with command-context evidence) + upstream-verified against Microsoft WinRM docs, the WinRb winrm gem, and the Hackplayers evil-winrm README, 2026-11-12"
---

# WinRM / WS-Management

> The management port that turns any leaked credential or NTLM hash into a PowerShell shell — admins enabled it, attackers inherit it.

## What it is

Windows Remote Management is Microsoft's implementation of the WS-Management protocol — a SOAP-over-HTTP management bus standardized by the DMTF. Every Windows host since Server 2008 ships the WinRM service (auto-start on servers, listener created by `winrm quickconfig` / `Enable-PSRemoting`); since Server 2012 PowerShell remoting is on by default. One process (the WinRM service behind `HTTP.SYS`) listens on **5985/tcp (HTTP)** and optionally **5986/tcp (HTTPS)**, serving SOAP envelopes at the `/wsman` URL prefix; modern hosts expose the same HTTP.SYS stack on **47001/tcp** as well, which shows up in nearly every full nmap sweep of a Windows box. Remote PowerShell sessions ride this bus via PSRP (PowerShell Remoting Protocol), so a valid login is not a shell-less service read — it is interactive PowerShell as that user.

The operator takeaway is that WinRM is a **credential-to-shell converter with a generous default policy**. Default service auth accepts Negotiate (NTLM or Kerberos via SPNEGO) and Kerberos — which means pass-the-hash works out of the box — while Basic and client-certificate auth are off until an admin turns them on (`Basic=false`, `Certificate=false` by default; `AllowUnencrypted=false`). Authorization is coarse: local **Administrators** plus the built-in **Remote Management Users** group can open sessions, nothing else. In observed engagements this port is overwhelmingly the *end* of a chain: hash from secretsdump, password from LSA/SAM, ticket from Kerberoast — then `evil-winrm -i ... -u ... -p/-H` and the box is yours.

Two default behaviors matter to the attacker as much as the defender. First, message-level encryption: even over plain HTTP 5985, NTLM/Kerberos sessions encrypt the SOAP payloads (NTLM with an RC4-derived session key, Kerberos with the ticket's etype) — TLS on 5986 is for server-auth and Basic/cert scenarios, not the only confidentiality. Second, **TrustedHosts**: a workgroup client that cannot do Kerberos is told to trust remote hosts *without authenticating them* (`winrm set winrm/config/client '@{TrustedHosts="..."}'`), the canonical setup step in labs and the canonical credential-leak footgun in real estates.

## The anatomy that matters on an engagement

    credentials (password | NTHash | ticket | cert)
        → evil-winrm / pwsh / nxc winrm  →  http://host:5985/wsman (SOAP)
        → SPNEGO: NTLM (PTH-friendly) or Kerberos (needs FQDN + realm)
        → PSRP runspace → PowerShell as the account (admin or Remote Mgmt Users)
        → 5986 HTTPS variant (self-signed common) — -S flag; Basic auth lives here
        → relay path: coerce (printerbug/PetitPotam) → ntlmrelayx WinRM server → shell

Three structural properties drive most findings:

- **Auth before authorization, and the auth is NTLM.** Any NTLM hash from anywhere (LSASS dump, NTDS.dit, relay capture) is a valid WinRM login; no plaintext ever needed. Protected Users (observed) close the NTLM door — that is the exception to plan around, not the rule.
- **Coarse default authorization.** Administrators + Remote Management Users is the whole gate; a service account in that group (observed: a service account over 5985) is a direct foothold, and nxc winrm mass-sprays this door across a range.
- **The port is a protocol, not a banner.** 5985/5986 also belongs to non-Windows WS-Man stacks — the OMI agent on Linux (OMIGod, CVE-2021-38647: unauthenticated SOAP POST to `/wsman` = root) is the standing reminder to fingerprint the stack, not the port number.

## Where you'll meet it

- **Domain controllers and member servers** — PS remoting on by default since 2012; the DC is where dumped hashes redeem. Kerberos-only estates (Protected Users, Smartcard-required) push you to `-r realm`/ticket auth and sometimes to patching the client (observed).
- **Hardened/segmented hosts** — 5985 firewalled, 5986 open with self-signed certs (observed): the `-S` flag plus `-c cert.crt -k private.key` certificate auth after stealing a PFX from the box.
- **Windows management estates (SCCM/SCOM/Exchange-adjacent)** — tooling accounts granted Remote Management Users for "just remoting"; harvest-then-spray (observed with cme/nxc winrm).
- **Linux estates with OMI/Chef/Puppet WS-Man agents** — Azure VMs in particular; same ports, different (and historically RCE-able) stack — observed once.
- **Pivot chains** — evil-winrm through proxychains into segmented AD (observed repeatedly), including running the raw `evil-winrm.rb` under ruby when the packaged gem misbehaves.

## Reading the service's mind

1. **`5985/tcp open http Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)`** with `_http-title: Not Found` and `_http-server-header: Microsoft-HTTPAPI/2.0` is the nmap signature; `-sV` often mislabels it — probe `/wsman` to confirm. Full details in [[winrm-bug-bounty|the methodology]].
2. **Negotiate/Kerberos on, Basic/cert off** are the service defaults; a host that *accepts* Basic over 5985 has `AllowUnencrypted=true` set — cleartext credentials on the wire, a finding in itself.
3. **-H pass-the-hash is first-class** in evil-winrm (40 of 185 observed invocations); only the NTHash is needed, no LM half.
4. **Kerberos auth needs the FQDN and a matching realm** (`-i dc01.example.com -r EXAMPLE.COM`), krb5.conf on the attacker box, and — in the stock ruby client — is mutually exclusive with SSL `-S`; the ly4k patch or evil-winrm-py covers that combination (observed).
5. **The winrm ruby gem beneath evil-winrm breaks on OpenSSL 3.x** (MD4 retired) — enable the legacy provider in `openssl.cnf` or the NTLM handshake dies mid-connection.

## OMI (Open Management Infrastructure): same ports, different product

*Added 2026-11-13 — rewritten from HackTricks; distinct from WinRM, never
conflate them.* OMI is Microsoft's **open-source implementation of the DMTF
CIM/WS-Management standards for Unix-like systems** — a Linux agent that can
end up listening on the same **5985/5986** ports (Wiz also documents
**1270/tcp** in some configurations). It arrives silently as a dependency of
Azure VM management extensions — Log Analytics, Diagnostics, Automation,
Security Center/Configuration Management and friends install it; Wiz's 2021
survey found a large share of sampled Azure Linux VMs running an unpatched
copy. Facts that decide your engagement:

- The service (`omiengine`) **runs as root** and may expose an HTTPS
  listener — but OMI also operates through a local Unix socket, so package
  presence alone proves nothing about network reachability; check the actual
  listener.
- **CVE-2021-38647 ("OMIGOD")**: unauthenticated RCE as root — vulnerable
  versions mishandle a request to `/wsman` when the `Authorization` header is
  simply *omitted*, and the `ExecuteShellCommand` WS-Man operation (against
  the `SCX_OperatingSystem` class) answers with command output. **All OMI
  below 1.6.8-1 are affected** (verified against Microsoft's OMI advisory
  GHSA-fmh7-p6gp-8xpj, which also lists three sibling privilege-escalation
  fixes: CVE-2021-38645, -38648, -38649; no workaround exists). Vendor
  backports mean a package version alone may not date the code.
- A co-observed process and an open port do not prove socket ownership or an
  exploitable path — and never send WS-Man requests during passive
  enumeration.

The practical workflow stays in [[winrm-bug-bounty|the methodology]] (Phase
0 stack discrimination, Phase 6 OMIGOD): fingerprint the stack before
assuming Windows because the port says 5985.

## Where to go next

[[winrm-bug-bounty|WinRM bug bounty methodology]] — fingerprinting and stack discrimination (Windows vs OMI), auth-mode triage, the evil-winrm workflows (password/hash/ticket/cert), relay-to-WinRM, and evidence handling.
