---
technology: "RDP (Remote Desktop Protocol / Remote Desktop Services)"
kind: overview
audience: offensive-security
family: windows
layer: "remote-access protocol (GUI)"
language: "C/C++ (Microsoft implementation); FreeRDP in C"
first-released: "RDP 4.0 with Windows NT 4.0 Terminal Server, 1996"
license: "proprietary protocol (MS-RDPBCGR spec published); FreeRDP is Apache-2.0"
homepage: "https://learn.microsoft.com/windows-server/remote/remote-desktop-services/welcome-to-rds"
provenance: "thin unit, first-class depth: command-context evidence in 4 files (xfreerdp client chains; strict gate — 24 files show bare 3389/tcp listeners) + upstream-heavy: FreeRDP man pages, Kali 'Passing the Hash with Remote Desktop', Microsoft Learn (tscon, Restricted Admin, BlueKeep guidance CVE-2019-0708), CISA AA19-168A, MITRE ATT&CK T1563.002. Verified 2026-11-12."
---

# RDP (Remote Desktop Protocol)

> The GUI logon protocol that will take a password, an NT hash, a Kerberos ticket — or neither, if you are SYSTEM and the administrator left a disconnected session on the box.

## What it is

RDP is Microsoft's graphical remote-access protocol (an RDP server ships in every Windows box as Remote Desktop Services, TCP/3389 by default; xrdp and VRDP are third-party implementations). The canonical client on Windows is `mstsc`; on Linux it is **xfreerdp** (FreeRDP) or the older **rdesktop**. The protocol's security layer comes in three generations, and the generation is a finding in itself: legacy **standard RDP security** (RSA-encrypted, no server auth — broken by design), **TLS**, and **NLA** — Network Level Authentication over CredSSP, which authenticates *before* the server allocates a session graph. NLA is required by default from Server 2012 on and is the mitigation that makes several pre-auth bug classes (including BlueKeep on modern-enough OSes) unreachable.

For an operator, RDP matters for three reasons beyond "it's a login screen". First, it is a **credential sink that accepts many credential forms**: password, NT hash (via Restricted Admin mode, below), smartcard, or a Kerberos ticket. Second, its server keeps **state**: disconnected sessions linger by default, holding logged-on tokens — and a SYSTEM shell on the host can inherit them without any credential. Third, its history includes one of the two or three most-wormed Windows bugs ever (BlueKeep, CVE-2019-0708). In practice RDP is mostly the *last hop* — the standard `xfreerdp /u:user /p:pass /v:target` after privilege escalation, often with a `/tls-seclevel:0` band-aid against legacy 2008-era TLS stacks.

The design brief — "make the remote desktop feel local" — is the operator takeaway: to feel local it must reattach sessions, forward drives and clipboards, and store reconnectable state, and each of those conveniences is an attack surface.

## The anatomy that matters on an engagement

    client (mstsc / xfreerdp / rdesktop)
      → TCP 3389 → X.224 connection request (this is where BlueKeep lives, pre-NLA)
      → security negotiation: standard RDP | TLS | NLA(CredSSP: NTLM or Kerberos)
      → logon (interactive — credentials land in the target's LSASS)
         ... unless Restricted Admin: network logon, NT-hash/Kerberos only, nothing cached
      → session (session 0 = services; console; user sessions, active or *disconnected*)
      → redirection channels: drive/clipboard/printer/port — each a file-move primitive

Three structural properties drive most findings:

- **Interactive logon by default means credential caching.** A normal RDP logon leaves the user's secrets in the target's LSASS (dumpable — see [[windows-secrets]]). Restricted Admin mode (`mstsc /restrictedadmin`, xfreerdp `/pth`) exists to stop that — and, ironically, is what makes hash-based GUI logon possible.
- **Sessions outlive connections.** Disconnect (not logoff) leaves the session resident with its token; `tscon` as SYSTEM reattaches it to your console with no credentials and no user prompt.
- **Channel redirection is bidirectional file access.** `/drive:share,/tmp` and `/clipboard` move files across the boundary; on locked-down targets they are often the only file-transfer path left.

## Where you'll meet it

- **The final hop of an internal engagement** — after collecting credentials, RDP onto the box the account owns. Implication: the default workflow is one xfreerdp command; membership in "Remote Desktop Users"/"Remote Management Users" (or an ACL granting it, granted ad hoc with bloodyAD) is the gate to check.
- **Internet-exposed 3389** — still thousands of boxes, still the classic pre-auth RCE lottery on XP/2003/2008-era patch levels. Implication: fingerprint the security layer first (`rdp-sec-check`); standard-RDP-only means legacy config *and* legacy patch posture.
- **Administrative jump hosts** — many disconnected admin sessions on one box. Implication: a single SYSTEM foothold there harvests every session (Phase 4 of the methodology) — the highest-value credential concentration in a flat network.

## Reading the protocol's mind

Five facts predict an RDP target's behavior — each expands into a phase of [[rdp-bug-bounty|the methodology]]:

1. **NLA is the patch-level tell.** NLA required = Server 2012+ posture; NLA off or unsupported = pre-2012 config where BlueKeep-era bugs and CredSSP issues cluster.
2. **`/pth` only works when Restricted Admin mode is available** — Windows 8.1/Server 2012 R2 and later (backported to 7/2008 R2, disabled by default; enabled state is the `DisableRestrictedAdmin` registry value). A `/pth` failure is usually that registry value, not your hash.
3. **Disconnected sessions are credentials waiting for SYSTEM** — `query user`, then `tscon <id> /dest:console`. No password involved; MITRE catalogs it as T1563.002.
4. **Standard RDP security (no TLS/NLA) offers no server authentication** — on legacy boxes a MITM position silently becomes session access; as a client you force the generation with `/sec:tls` or `/sec:nla`.
5. **Legacy TLS stacks reject modern clients** — Server 2008-era boxes negotiate TLS 1.0/ciphers FreeRDP3 refuses; the standard band-aid is `/tls-seclevel:0` (downgrade *your* client, deliberately, once).

## Where to go next

[[rdp-bug-bounty|RDP bug bounty methodology]] — port fingerprinting and security-layer probing, the xfreerdp/rdesktop client workflows (flags, file transfer, legacy-TLS fallback), pass-the-hash via Restricted Admin, session hijacking with tscon, BlueKeep-era CVE context, and hardening signals that change your next move.
