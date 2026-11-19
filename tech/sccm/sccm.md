---
technology: "SCCM / MECM (Microsoft Configuration Manager)"
kind: overview
audience: offensive-security
family: windows
layer: "endpoint management / software distribution (systems management)"
language: "C++/C#/WMI (site roles), PowerShell (module)"
first-released: "1994 as SMS (Systems Management Server); Configuration Manager from 2007; MECM branding from 2019; continuing as 'Microsoft Configuration Manager' under Intune family"
license: proprietary
homepage: "https://learn.microsoft.com/mem/configmgr/"
provenance: "thin unit, first-class depth, upstream research heavy. Evidence: 1 file with genuine command-context evidence (ntlmrelayx patched with SCCMPoliciesAttack/SCCMDPAttack relayed against an HTTP management point); the structure pass's other 2 marker files are OCR hex-collisions, not evidence. Upstream: SpecterOps (Thompson: 'Coercing NTLM Authentication from SCCM' 2022, 'Relaying NTLM from SCCM Clients' 2022, 'The Phantom Credentials of SCCM'), Guidepoint (automatic client push + AD system discovery; NAA compromise), Truesec (SCCM tier killer), Palo Alto Unit 42 (NAA via WMI/policy), Microsoft Learn (client security guidance: Kerberos-only client push, NAA, Enhanced HTTP). Verified 2026-11-12."
---

# SCCM / MECM

> A management platform whose job is to push software and policy to every Windows machine in the estate — so any credential theft in it scales to every host it manages, and its own HTTP endpoints will happily hand policy secrets to a machine you just invented.

## What it is

SCCM (born SMS in 1994, renamed Configuration Manager, then Microsoft Endpoint Configuration Manager/MECM, now Microsoft Configuration Manager) is Microsoft's on-premises endpoint-management platform: a **site server** hierarchy whose roles — **management point (MP)** for policy, **distribution point (DP)** for content, site database on SQL Server — enroll every Windows client and push applications, updates, and configuration to them. Clients talk to the MP over HTTP or HTTPS at endpoints like `/ccm_system/request` and pull content from DPs; discovery (AD system discovery, network discovery, heartbeat) keeps the device inventory the site acts on.

For an operator, SCCM is best modeled as a **tiering junction with a credential distribution habit**. It exists to move trust outward: the site server holds high-privilege accounts (client push installation accounts, the legacy **Network Access Account**), and several of those credentials are *deliberately shipped to clients* — the NAA's username and password travel inside the machine policy every client downloads, DPAPI-protected but decryptable by anyone with local admin (or anyone who can read the CIM repository file, whose DACL is world-readable). Microsoft's own guidance now says: stop using NAAs, use Enhanced HTTP or PKI, and disable the NAA account — but "phantom" NAA blobs persist on former clients long after decommissioning, so the attack surface outlives the setting.

The second deliberate-trust mechanism is **client push installation**: to onboard a discovered device, the *server reaches out to the client* and authenticates with a client-push account (or its machine account). Combined with attacker-controlled discovery records and the "Allow connection fallback to NTLM" option, this inverts: the management point can be made to authenticate *to you*, with an account that is usually a local admin everywhere. That is the SpecterOps 2022 coercion class and the Guidepoint automatic-client-push site-takeover class.

## The anatomy that matters on an engagement

    attacker (any domain user / machine quota)
      → discover SCCM: AD System Management container, LDAP name fuzz, port layout
      → MP HTTP endpoints (when PKI not enforced):
           /ccm_system_windowsauth/request   <- register a (fake) device
           /ccm_system/request               <- fetch machine policy
           /SMS_MP/.sms_pol/{GUID}           <- fetch specific policy (NAA secrets inside)
      → NAA policy blobs (DPAPI, machine key):
           WMI root\ccm\policy\Machine\ActualConfig → CCM_NetworkAccessAccount
           CIM repository C:\Windows\System32\wbem\Repository\OBJECTS.DATA (world-readable)
      → client push path (auto push + NTLM fallback on):
           forged discovery/registration → MP authenticates to attacker SMB/HTTP
           → relay (to MP/DP HTTP: policy + DP attacks) or capture

Three structural properties drive most findings:

- **The MP will talk HTTP and trust machine-level auth.** Where "HTTPS only / PKI required" is off, policy endpoints accept NTLM over HTTP — register a device with any machine account (default quota lets a user create 10) and pull the site's policy secrets.
- **Credentials ship with policy.** The NAA is in every client's policy; local-admin on any one client (or the world-readable OBJECTS.DATA) yields a domain credential that is over-privileged far too often — Truesec engagements show NAAs that could modify tier-1 groups or own the site database (which is itself full-site admin: SCCM rights are rows in `CM_<SITECODE>`).
- **The server initiates trust decisions.** Discovery + automatic client push means *data you inject* (a discovery record, a registered device) causes *server-side privileged authentication* toward a host you control.

## Where you'll meet it

- **Large corporate/enterprise Windows estates** — every Fortune-scale network with on-prem AD has SCCM or its Intune-coexisting hybrid; the attack surface concentrates on a handful of site servers and every managed client. Implication: the fastest domain-wide credential is one NAA, not one hundred LSASS dumps.
- **Post-foothold on any managed workstation** — you are probably standing on an SCCM client; `SharpSCCM local siteinfo` names the MP and site code, and the same box holds DPAPI NAA blobs. Implication: enumerate before you lateral — the client tells you where the crown jewels route.
- **AD attack paths mid-engagement** — relay infrastructure that already exists for ESC8/coercion chains extends to SCCM: the observed relay chain pushes a coerced machine identity straight into the MP/DP HTTP applications to dump policies.

## Reading the platform's mind

Five facts predict an SCCM target's behavior — each expands into a phase of [[sccm-bug-bounty|the methodology]]:

1. **The System Management container in AD lists the site servers** — the MP's FQDN and site code (three characters, e.g. `PS1`) are enumerable pre-auth from LDAP.
2. **HTTP on the MP means machine-authenticated policy access** — register, then request; unapproved devices are refused *secret* policies, approval is the only gate, and rogue-device registration is the standard bypass.
3. **NAA blobs decrypt with the *system* DPAPI key** — no user password needed; and they persist in OBJECTS.DATA after the account is removed from the site.
4. **"Allow connection fallback to NTLM" is the client-push kill switch** — with it on and PKI off, a low-privileged domain user can make the MP's installation/machine accounts authenticate to an attacker host; Microsoft's client security guidance now allows requiring Kerberos-only for exactly this reason.
5. **Site-database admin = site admin** — SCCM's permission model lives in the `CM_<SITECODE>` SQL database; an over-privileged NAA or service account reaching the DB server with `sa`-like rights converts to full SCCM administration.

## Where to go next

[[sccm-bug-bounty|SCCM bug bounty methodology]] — site discovery from AD and ports, MP HTTP endpoint workflows (device registration, policy retrieval), NAA extraction (WMI, disk, policy), client-push coercion and relay (including the observed ntlmrelayx relay chain), distribution-point and site-database escalation, and the hardening signals that flip your next move.
