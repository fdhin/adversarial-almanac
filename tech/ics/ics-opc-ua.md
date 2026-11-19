---
technology: "OPC UA (IEC 62541)"
kind: bug-bounty-methodology
audience: ai-agent
family: network-services
role: implementation-variant
layer: industrial data middleware over opc.tcp:// binary transport, TCP 4840 (+4843 TLS binding, vendor ports); mDNS _opcua-tcp._tcp.local discovery
standards: "IEC 62541 (OPC UA), Part 18 (RBAC), deprecated policies Basic128Rsa15/Basic256"
status: upstream-only
source: "rewritten from HackTricks (src/network-services-pentesting/4840-pentesting-opc-ua.md), CC BY-NC 4.0, accessed 2026-11-13"
provenance: "upstream-only (HackTricks + primary sources); zero corpus evidence by design. All five CVEs re-verified against primary advisories 2026-11-13 (GHSA-h958-fxgg-g7w3 + OPC Foundation bulletin CVE-2024-42512; OPC Foundation bulletin CVE-2024-42513; VDE-2025-022 for CVE-2025-1468; Softing PSIRT for CVE-2025-7390; Ubuntu/upstream fix commit for CVE-2024-53429). HT's memory-deref framing of CVE-2024-53429 and CN-replay mechanism of CVE-2025-7390 are NOT in the primary advisories and were dropped. Bucket-2 new-unit wave 1, 2026-11-13."
---

# OPC UA — implementation methodology

Companion router: [[ics|ICS area router]]. OPC UA is the modern middleware that moves process
data between PLCs, SCADA, historians, MES and cloud — and the one ICS protocol *designed* with
security in it: it can authenticate, sign, and encrypt every message. The finding is almost
never the protocol; it is the deployment that turned the security off: an endpoint offering
`SecurityPolicy None` and an `Anonymous` token. Work the configuration first, stack CVEs
second.

## Triage table

| Surface | Gate | Pre-auth? | Payoff |
|---|---|---|---|
| GetEndpoints posture dump | none — pre-auth by design | yes | Every policy/mode/token combo — Info/Critical-if-None |
| Anonymous session with user-grade access | None + Anonymous + weak RBAC | yes | Whole address space — High |
| Unbound Method nodes (ns>=2) | no role binding | yes | `Call` restart/firmware/start-stop — Critical |
| Writable safety variables | write access bits | yes | Out-of-band setpoint/interlock change — Critical |
| Basic128Rsa15 padding oracle | deprecated policy enabled | yes | Server private key → auth bypass — High |
| Credential capture (TOFU clients) | client accepts self-signed server | lure | UserName passwords to a rogue server — High |
| Session/subscription abuse | stack over-trusts session layer | varies | Token replay, DoS — Med/High |

## Phase 0 — Find servers, read the three dimensions

Port spread: **4840** is the familiar `opc.tcp://` port, 4843 the TLS binding — but production
servers bind vendor ports (KepServerEX 49320, Prosys 53530, open62541 reference 62541,
UaGateway 48050, plus 4841/48010/48020), so sweep broadly; on-LAN, mDNS announces
`_opcua-tcp._tcp.local`. There is **no official nmap NSE for OPC UA** — a dedicated client is
the route.

Every endpoint advertises three independent settings, and reading them is the whole
assessment: **SecurityPolicy** (None; Basic128Rsa15/Basic256 — deprecated; Basic256Sha256;
Aes128/Aes256 variants), **MessageSecurityMode** (None / Sign / SignAndEncrypt), and **user
identity token** (Anonymous / UserName / Certificate / IssuedToken). `GetEndpoints` returns
all of it **before any credential** — one unauthenticated request maps the security posture.
A `None` policy means cleartext and no integrity; a `Sign`-only channel still leaves bodies
readable; the UserName password is encrypted to the *server's* certificate, so a client that
trusts a rogue server's self-signed cert (trust-on-first-use) hands over operator credentials
— and on a `None` endpoint the password rides `ActivateSession` in the clear.

## Phase 1 — Passive recon

The HEL/OPN/GetEndpoints handshake is cleartext regardless of policy; bodies decode wherever
mode is None (the misconfiguration you hunt):

```bash
opcua                                       # all OPC UA
opcua.transport.type == "HEL"               # session start, carries EndpointUrl
opcua.servicenodeid.numeric == 428          # GetEndpoints (recon)
opcua.servicenodeid.numeric == 673          # Write (control)
opcua.servicenodeid.numeric == 712          # Call (method invocation)
opcua.Password                              # cleartext password on unencrypted channel
```

oida's pcap mode (`oida pcap capture.pcap --protocols opcua`) lists endpoints and advertised
policies per session, exchanges certificates, flags insecure `None` policies, and extracts
UserName credentials seen in `ActivateSession` on unencrypted sessions.

## Phase 2 — Read-only enumeration

Address space: typed **nodes** in namespaces (`ns=0` base, `ns=1+` vendor) joined by
references; **NodeId** = `ns=<idx>;<i or s>=<id>`. Enumerate the namespace table FIRST —
vendors reorder indexes across firmware. Variable nodes carry `AccessLevel`/`UserAccessLevel`
bitmasks (`0x01` read, `0x02` write; User* is your session's rights); Method nodes carry
`Executable`/`UserExecutable`. Fingerprint nodes in ns=0: `i=2253` Server, `i=2256`
ServerStatus, `i=2267` diagnostics (session counts — brute-force visibility), `i=85`
ObjectsFolder, `i=2994` Auditing.

```bash
oida opcua 198.51.100.10 --get-endpoints     # the posture dump (policies, tokens)
oida opcua 198.51.100.10 --dump-namespaces   # resolve ns indexes
oida opcua 198.51.100.10 -D --dump-values    # full walk: types, access levels, values
oida opcua 198.51.100.10 --dump-methods      # callable nodes
oida opcua 198.51.100.10 --dump-write        # writable variables
oida opcua 198.51.100.10 --scan-writable     # via access bits — zero writes issued
oida opcua 198.51.100.10 --test-cert-trust   # does it accept an untrusted client cert?
oida opcua 198.51.100.10 --test-rbac --rbac-detailed   # anonymous vs user vs cert matrix
```

Install extra `oida-ics[opcua]` (asyncua underneath). Flag trap: `-p` is port; the password
short flag is `-P` (`-u` username; `-d`/`-D` dump depths). Anonymous is only *proven* by an
activated session — the tool connects and raises the finding only on actual activation. Manual
work with asyncua mirrors it: `Client("opc.tcp://...")`, `get_namespace_array()`,
`get_node("ns=2;s=Pump1.Setpoint")`, `read_attribute(ua.AttributeIds.UserAccessLevel)`.
UaExpert as the interactive GUI client; OpalOPC (`opalopc -vv opc.tcp://...`) for a one-pass
anonymous/weak-policy/writable scan; Secura's opcattack for the crypto attacks below.

## Phase 3 — Gated actions

```bash
oida opcua 198.51.100.10 --node-id "ns=2;i=40" --write-value 1500 --confirm
oida opcua 198.51.100.10 --call-method "ns=2;i=1000" --method-args "[21.5]" --confirm
oida opcua 198.51.100.10 -u users.txt -P passwords.txt --confirm   # brute (lockout risk)
oida opcua 198.51.100.10 --test-subscription-limits --confirm      # DoS ramp
oida opcua 198.51.100.10 --fuzz --fuzz-mode nodes --confirm
```

A `Write` to a setpoint or a `Call` on a vendor restart/start/firmware method is a process
action. Credential brute force is gated because repeated failed `ActivateSession` can lock out
operator accounts. Session-layer abuse worth testing on over-trusting stacks: the
`AuthenticationToken` is a bearer token — capture and replay rides the session; session
flooding past `MaxSessionCount`; `RegisterNodes` handle starvation; very-low publishing
intervals plus oversized monitored-item queues (scheduler overflow). Client-side targeting:
stand up a rogue server (realistic ApplicationDescription/Endpoints, then hostile
ExtensionObjects or browse trees) — TOFU plus client parsing has produced client-side RCE
(Ignition, Softing edgeAggregator); `TranslateBrowsePathsToNodeIds` resolves paths like
`MotorControl/StartStop` straight to targetable NodeIds. GDS/LDS and Reverse Connect extend
the discovery attack surface before any certificate prompt.

## Phase 4 — Verified CVE and attack set

Upstream-history context; verify the specific server's stack and version before claiming any.

**The Basic128Rsa15 class (Bleichenbacher padding oracle).** The deprecated policy's PKCS#1
v1.5 padding leaks oracles in `CreateSession`/`OpenSecureChannel`; enough crafted blobs
recover the server certificate's private key, then impersonation/decryption. opcattack
operationalizes it (`check`, `auth-check`, `sigforge`, `decrypt`).

- **CVE-2024-42512** — OPC UA .NET Standard Stack **< 1.5.374.158** (patched in 1.5.374.158):
  application-authentication bypass **when the deprecated Basic128Rsa15 policy is enabled** —
  it is disabled by default, so most deployments are unaffected; the patch also closes
  Basic128Rsa15 channels on attack detection (a deliberate DoS-before-compromise). CVSS 5.9.
- **CVE-2024-42513** — same stack/release: authentication bypass on **HTTPS endpoints using a
  security policy other than None**; workaround is disabling HTTPS endpoints. Reported by
  Secura. CVSS 6.5.
- **CVE-2025-1468 (VDE-2025-022)** — CODESYS Control V3 runtime **< 3.5.21.0**: the
  CmpOPCUAServer is exposed to the same Bleichenbacher oracle **only when Basic128Rsa15 is
  enabled by customer configuration or the `CMPOPCUASTACK_ALLOW_SHA1_BASED_SECURITY` build
  define** — the default CODESYS configuration is not affected. CVSS 7.5.

**Crash/cert bugs (2024–2025):**

- **CVE-2024-53429** — open62541 **1.4.6**: assertion failure in the binary decoder
  (`fuzz_binary_decode`) reachable pre-auth, crashing servers embedding that version
  (network DoS, CVSS 7.5); fixed in 1.4.7 / 1.3.15.
- **CVE-2025-7390** — Softing **OPC UA C++ SDK 6.40–6.80** `opc.https` servers (and
  edgeConnector / edgeAggregator through 2025.03): the TLS client-certificate trust check can
  be bypassed when the endpoint allows only secure communication → client-auth bypass
  (CWE-295). Fixed in SDK 6.80.1 / SDEX Suite V1.0. CVSS 9.1.

Claroty's opcua-exploit-framework packages the Pwn2Own-grade research (profiles for Kepware,
Ignition, Unified Automation, Softing, open62541, node-opcua). Impact framing:
INCONTROLLER/PIPEDREAM (2022) speaks OPC UA to enumerate servers and read/write node values —
configuration weaknesses are operationalized, not theoretical. ATT&CK for ICS: T1692.001
command messages, T0836 modify parameter.

## Command quick reference

```bash
oida opcua 198.51.100.10 --get-endpoints       # posture in one pre-auth request
oida opcua 198.51.100.10 --test-rbac           # anonymous vs user vs cert
opalopc -vv opc.tcp://198.51.100.10:4840       # one-pass anonymous/weak-policy scan
nmap -sV -Pn -n --open -p 4840,4843,49320,53530,62541 198.51.100.10
```

## Tool mapping (prose)

nmap for the port sweep (no NSE exists); the oida ICS scanner (opcua module over asyncua) for
posture, address-space dumps, RBAC diffing, and confirm-gated writes; asyncua/python-opcua
and the UaExpert GUI for manual sessions; OpalOPC for automated configuration assessment;
opcattack for the cryptographic attacks; Claroty's opcua-exploit-framework for deep
fuzz/exploit work; Wireshark's OPC UA dissector for the passive layer. Tools vault owns
per-tool detail.
