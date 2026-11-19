---
technology: "WSUS (Windows Server Update Services)"
kind: overview
audience: offensive-security
family: windows
layer: "patch management / software distribution (IIS-hosted)"
language: "C#/.NET (SharpWSUS, wsuks are attack tooling); WSUS itself is a Windows Server role on IIS + SQL (SUSDB)"
first-released: "2003 (Software Update Services lineage); WSUS as such from 2005"
license: proprietary
homepage: "https://learn.microsoft.com/windows-server/administration/windows-server-update-services/"
provenance: "thin unit, first-class depth. Evidence: 2 files with full command-context chains (WSUS Administrators group → SharpWSUS malicious-update injection; AD-CS ESC17 cert + DNS poison → wsuks rogue WSUS server). All other 'wsus/8530' marker hits are collisions (base64 strings, PIDs, PDF names). Upstream: SharpWSUS usage (multiple references), GoSecure PyWSUS research, ESC17/wsuks research (Durukan 2026, matches the observed chain), Microsoft guidance (8530 HTTP / 8531 HTTPS defaults, TLS requirement 'without TLS an attacker can modify update information in transit'), CVE-2025-59287 (Oct 2025, KEV-listed WSUS RCE). Verified 2026-11-12."
---

# WSUS (Windows Server Update Services)

> The box that decides what code every Windows machine in the domain runs next — and whose only integrity check on that code is "is the binary Microsoft-signed", not "is this actually an update".

## What it is

WSUS is Microsoft's on-premises patch-management role: a Windows Server running IIS with the WSUS application (default ports **8530 HTTP / 8531 HTTPS**) backed by a SQL database (`SUSDB`), which mirrors Microsoft Update and distributes approved updates to domain clients pointed at it by GPO. Clients find their update server in the registry (`HKLM\Software\Policies\Microsoft\Windows\WindowsUpdate`, with `AU\UseWUServer=1` making the setting live), poll it on schedule, download content from `WsusContent`, and install what the server approves — as `NT AUTHORITY\SYSTEM`.

The operator takeaway is the trust model, which has exactly one link: **the binary must be signed by Microsoft**. Nothing checks that a "Microsoft-signed binary with an attacker-chosen command line" is a legitimate update — which is why every WSUS attack ends with `PsExec64.exe -accepteula -s -d <your command>` delivered as a fake KB. WSUS checks *signature*, not *semantics*, and Microsoft's own hardening guidance concedes the transport half: without TLS, an attacker positioned on-path can modify update traffic; with TLS, the remaining attacks are the server itself and the certificate authority that issues its cert.

Second structural fact: **the WSUS server is a SYSTEM-everywhere distribution engine**. Whoever controls it — a local administrator, a member of the **WSUS Administrators** group, or anyone reaching `SUSDB` with enough rights — approves an "update" for a computer group, and every member of that group executes it at its next check-in. The observed chain is exactly this: a user whose only distinguishing privilege was WSUS Administrators membership converted it into SYSTEM on the domain controller.

## The anatomy that matters on an engagement

    clients (GPO-pointed, registry UseWUServer=1)
      → poll http://wsus:8530 (or https:8531)  ← on-path position poisons this when HTTP
      → metadata: what updates exist + approval state + command line
      → content download (WsusContent/) — signature-checked, semantics-blind
      → install: Microsoft-signed binary + attacker args, running as SYSTEM

    attacker entry points:
      1. local admin / WSUS Administrators on the server  → inject updates legitimately
      2. SUSDB reach with rights                          → same, via the database
      3. on-path (ARP / mitm6 / DHCP spoof) + HTTP feed    → rogue WSUS replies
      4. ESC17-style cert + DNS poison                     → rogue *HTTPS* WSUS
      5. CVE-2025-59287 (2025, KEV)                        → pre-auth RCE on the server

Three properties drive the findings:

- **Signature-only trust turns Sysinternals into a payload class.** Any Microsoft-signed binary that accepts a command line (PsExec, BgInfo) is a valid "update" file.
- **HTTP mode is a MitM primitive, TLS mode is only as strong as the CA.** The 2026 ESC17 research shows the domain CA itself can mint the rogue server a trusted certificate — TLS alone does not close this surface.
- **Execution lands as SYSTEM on every enrolled client, including domain controllers.** WSUS is patch *management*: high-value hosts are enrolled by design.

## Where you'll meet it

- **Mid-size and large AD estates** — anything too big for manual patching; the WSUS box often co-hosts with SCCM or runs standalone on a member server (port 8530/8531 in nmap output is the tell). Implication: after any foothold, check group membership — `whoami /groups` revealing **WSUS Administrators** is the whole privesc (observed).
- **Workstations as clients** — from a user-context shell, the registry keys name the WSUS server and whether the feed is HTTP (`WUServer` starting `http://`); that decides whether a network-position attack needs any server access at all.
- **AD-CS engagements** — certificate templates with Server Authentication EKU and enrollee-supplied subject (ESC17-class) chain directly into a rogue-HTTPS-WSUS SYSTEM execution (observed — cert minted for the WSUS hostname itself, DNS poison, wsuks serve-only).
- **Perimeter/unsegmented networks** — the October 2025 CVE-2025-59287 (deserialization, CVSS 9.8, CISA KEV) made unpatched WSUS servers pre-auth RCE targets in their own right.

## Reading the platform's mind

Five facts predict a WSUS target's behavior — each expands into a phase of [[wsus-bug-bounty|the methodology]]:

1. **Ports 8530/8531 fingerprint the role instantly** — and which of the two answers tells you whether the transport attack needs a certificate.
2. **The client registry is the map**: `HKLM\Software\Policies\Microsoft\Windows\WindowsUpdate` names the server; `AU\UseWUServer=1` proves the client actually consumes it.
3. **"Microsoft-signed + arbitrary args" is the universal payload shape** — every tool (SharpWSUS, PyWSUS, wsuks) wraps PsExec64.exe for a reason.
4. **Approval is per-computer-group** — targeting one host (the DC) is as easy as targeting the fleet, and `usoclient StartScan` / `wuauclt /detectnow` forces the client's next poll on demand.
5. **TLS mode gates on the domain CA** — if a template lets you mint a server-auth cert for an arbitrary DNS name, HTTPS buys the defender nothing.

## Where to go next

[[wsus-bug-bounty|WSUS bug bounty methodology]] — locating the server and reading client config, the WSUS-Administrators → SharpWSUS injection workflow (observed chain), on-path HTTP poisoning with PyWSUS, the ESC17 + wsuks rogue-HTTPS chain (observed chain), forcing and verifying installs, cleanup, and the 2025 RCE context.
