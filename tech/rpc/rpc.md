---
technology: "MSRPC / DCE-RPC endpoint mapper"
kind: overview
audience: offensive-security
family: windows
layer: protocol
language: "c (rpcss runtime); clients in c, python, ruby"
homepage: "https://learn.microsoft.com/openspecs/windows_protocols/ms-rpce/"
github-repo: "https://github.com/fortra/impacket (canonical client library)"
provenance: "evidence-grounded (72 files with command-context evidence) + upstream-verified against the Samba rpcclient(1) man page, fortra/impacket example sources (rpcdump.py, samrdump.py, lookupsid.py), Microsoft MS-DCOM/OXID documentation and IRunningObjectTable API docs, 2026-11-12"
---

# MSRPC / DCE-RPC

> Every Windows box runs one port that is a directory of every other service: ask 135 politely and it hands you the map — usually without asking who you are.

## What it is

MSRPC is Microsoft's implementation of the OSF DCE/RPC remote procedure call standard (re-specified as [MS-RPCE] and a constellation of interface documents — [MS-SAMR], [MS-LSAT]/[MS-LSAD], [MS-SRVS], [MS-DCOM], ...). Applications register named **interfaces** — a UUID plus version — and the RPC Endpoint Mapper service (`rpcss.exe`, the same process that hosts DCOM activation and the Running Object Table) listens on **135/tcp**, handing clients the dynamic binding string for any registered interface. Interfaces then run over one of several protocol sequences: `ncacn_ip_tcp` (TCP on dynamic high ports — the `49152-65535 msrpc` block in every full nmap sweep), `ncacn_np` (RPC over SMB named pipes such as `\pipe\samr`, `\pipe\lsarpc`, `\pipe\epmapper` on 445), ALPC locally, and legacy `ncacn_http` over an RPC proxy. One idea, many carriers: when SMB is blocked, the same SAMR enumeration just moves to 135.

The operator takeaway is that MSRPC is **Windows' internal API made remotely reachable**, and its access control is per-interface and wildly uneven. The endpoint mapper itself answers anonymous queries (Impacket's rpcdump.py literally notes "we don't need to authenticate to 135") — a free inventory of UUIDs and bindings. Historically, a NULL session (`rpcclient -U '' -N`) walked the whole SAM and LSA surface; modern defaults tightened that, but any authenticated domain user — even one too restricted to read LDAP — can still enumerate users and groups through SAMR/LSAT by RID cycling, because that right is granted to Authenticated Users "by default for many users and groups" (observed in practice). The abuse patterns split into three families — **enumeration** (lookupsid RID cycling, samrdump, netexec `--rid-brute`), **execution** (wmiexec/dcomexec riding DCOM objects), and **coercion/elevation** (PetitPotam-class interfaces, the Potato family abusing DCOM activation and OXID resolution).

On top of pure RPC sits **DCOM**, the object-oriented layer: activation services (`IRemoteSCMActivator`, `IActivation`) instantiate COM classes remotely, the **OXID resolver** (`IObjectExporter` — opnum 3 `ServerAlive`, opnum 5 `ServerAlive2`) answers unauthenticated and lists a host's network bindings, and `IRemUnknown` keeps object references alive. The **Running Object Table (ROT)** — a session-scoped table inside rpcss that maps monikers to already-running COM objects, queryable via `IRunningObjectTable::EnumRunning/GetObject` — completes the trio as an in-session discovery surface. These are not exotic: the observed GodPotato chains all show the same fake `\pipe\epmapper` + OXID dance, and RemotePotato0 turns DCOM activation of another user's session into a relayed hash.

## The anatomy that matters on an engagement

    client → 135/tcp epmapper (anonymous OK) → binding strings per interface UUID
        → ncacn_ip_tcp dynamic ports (49152-65535)  |  ncacn_np via SMB 445 pipes
        → per-interface authz: NULL? Authenticated Users? Admin? write ops?
    DCOM: activation (rpcss) → OXID resolution (IObjectExporter, unauth) → IRemUnknown
        → ROT (session monikers) → live object pointers
    abuse classes:
        enum: lookupsid/samrdump/rpcclient --rid-brute  (SAMR/LSAT)
        exec: wmiexec (WMI) / dcomexec (MMC20, ShellWindows) — needs DCOM rights
        coerce: MS-EFSR/MS-DFSNM/MS-RPRN interfaces → NTLM to attacker
        elevate: GodPotato (fake OXID/epmapper + SeImpersonate), RemotePotato0 (relay)

Three structural properties drive most findings:

- **The mapper is an anonymous directory.** rpcdump of 135 (or `\pipe\epmapper` over 445) yields every interface UUID the host exposes — map UUIDs to interface specs and you know the attack surface before touching anything else.
- **Enumeration rights lag the interface surface.** SAMR/LSAT let any ordinary domain account list users/groups/SIDs (RID cycling); password-policy, share and session data (SRVSVC) leak similarly; write operations (SAMR `setuserinfo`, LSA secrets) depend on delegated ACLs that estates routinely over-grant (observed: a support-tier user resetting an admin password via `setuserinfo <user> 23 <pass>`).
- **DCOM's machinery is trusted by the OS and abusable by design.** Unauthenticated OXID resolution leaks interface bindings (host pivot discovery); activation triggers authenticated calls from SYSTEM-context services (the whole Potato family); per-user COM objects in other sessions are reachable through the ROT. Microsoft fixed pieces over the years but explicitly will not "fix" cross-session activation (RemotePotato0 is documented as won't-fix behavior).

## Where you'll meet it

- **Every domain controller and member server** — 135 is the second line of every nmap scan after 445; on hardened hosts where SMB/LDAP enumeration is ACL-starved, SAMR-over-445 or RID brute over 135 is the fallback enumeration channel (observed in practice).
- **NULL-session-era estates** — older Windows and misconfigured Samba members where `rpcclient -U '' -N` still walks users/shares; a single srvinfo/enumdomusers hit rewrites the recon plan (observed in practice).
- **Credential-validation loops** — lockout-free password checking at RPC speed (observed: `rpcclient getusername` loop against a no-lockout policy).
- **Any box with SeImpersonate** — web-shells, services, MSSQL: the Potato family converts DCOM activation into SYSTEM (observed repeatedly — GodPotato over NETWORK SERVICE).
- **Session-rich servers (RDP/WinRM jump hosts)** — RemotePotato0 territory: another user's interactive session is the credential, the DCOM activation chain is the extraction path (observed).
- **Exchange/legacy RPC-over-HTTP** — `ncacn_http` through an RPC proxy on 443 (rpcdump's 443/593 binding) marks an Exchange-era estate.

## Reading the endpoint mapper's mind

1. **`135/tcp open msrpc Microsoft Windows RPC`** is a fingerprint of Windows itself, not of one service; the interface dump that follows is the real recon artifact — full workflow in [[rpc-bug-bounty|the methodology]].
2. **Anonymous is answered, auth-level 1 (`RPC_C_AUTHN_LEVEL_NONE`) is testable**: rpcmap-style opnum brute tells you which methods of an interface skip authentication (observed — `ServerAlive`/`ServerAlive2` on IObjectExporter).
3. **High ports 49152+ `msrpc` in nmap are the actual interface endpoints** — filtering 135 alone stops the directory, not the services.
4. **`\pipe\epmapper`, `\pipe\samr`, `\pipe\lsarpc` over 445 are the same interfaces** — expect your enumeration to survive SMB-only rulesets and vice versa.
5. **`setuserinfo <user> 23 <password>`** is SAMR's password-reset op — its ACL (not authentication) is the only gate, and estates hand it to helpdesk-style groups.

## Where to go next

[[rpc-bug-bounty|MSRPC bug bounty methodology]] — endpoint dumping, NULL-session and RID-cycling enumeration, the rpcclient command surface, DCOM execution and potato-class elevation, with verified commands and safe verify variants.
