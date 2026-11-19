---
technology: Check Point Firewall-1 (Quantum lineage)
kind: bug-bounty-methodology
audience: ai-agent
status: upstream-only
family: network-security
version: "1.0"
provenance: "upstream-only; zero corpus evidence by design — version gates and IoC commands transcribed from Check Point sk1000171 / sk182336 / sk181311 and Rapid7's CVE-2026-16232 analysis (chain mechanics, PoC behavior, patched-vs-vulnerable Jumbo-hotfix takes) + rewritten from HackTricks (src/network-services-pentesting/pentesting-264-check-point-firewall-1.md), CC BY-NC 4.0, accessed 2026-11-13"
---

# Check Point bug-bounty methodology

Companion overview: [[check-point]].

Scope: the pre-auth topology oracle on TCP 264, the gateway/management control-plane ports, the 2026 management-server exploit chains, and the authenticated Management API. The IKE surface is documented in [[ipsec-ike-bug-bounty]].

## Phase 0 — Topology oracle on 264/tcp

`FW1_topo` answers unauthenticated topology requests with gateway and management names. Raw probe:

```bash
printf '\x51\x00\x00\x00\x00\x00\x00\x21\x00\x00\x00\x0bsecuremote\x00' \
  | nc -q 1 198.51.100.10 264 | grep -a CN | cut -c 2-
# CN=gw-edge-01,O=mgmt01.example.com
```

`CN` is the gateway object/hostname; `O` maps to the Internal CA / management naming context. Metasploit's `checkpoint_hostname` gather module automates the same exchange and parses out the firewall and SmartCenter hostnames. Feed the names into hostlists and targeted DNS brute-force — the management hostname is usually the most valuable field. A response also fingerprints Check Point software and implies configured remote access, without proving the management server itself is exposed.

## Phase 1 — Control-plane sweep

```bash
nmap -Pn -sT -p 264,443,18190,18191,18210,18211,18231,18264,19009 \
  198.51.100.10
```

Expect 18190 (FWM/CPMI) and 19009 (CPM/DLE `/cpmws/`) on the management host; CPD, ICA pull/push, policy-server and ICA services ports cluster beside them per the vendor's control-connection port matrix. With **Accept Control Connections** implied rules enabled, several are reachable regardless of the explicit rulebase — rescan from every new foothold (VPN segment, jump host, management VLAN).

## Phase 2 — CVE-2026-93616: unauthenticated traversal + upload on the management server (TCP 19009)

Vendor record: directory traversal and file upload allowing arbitrary script execution on Security Management, Multi-Domain Management, Log, Multi-Domain Log, and SmartEvent servers; CVSS 9.8; **exploited in the wild**. Affected takes (vendor advisory sk1000171): R82.10 Jumbo Hotfix Take ≤44, R82 ≤126, R81.20 ≤166, R81.10 ≤190 (EoS), all R80.x/R81 trains EoS; R82.20 fixed via dedicated Security Hotfix; **LivePatch Take 28/29 does not cover it**. Public research describes the full chain as pairing the traversal with a remote-application authentication defect in the CPM SOAP layer; public patch-checkers fingerprint the fix non-destructively by posting to `/cpmws/UpgradeSvcRemote` with an invalid `targetVersion` and reading the SOAP fault body (a validation fault means patched; dispatch without the validation message means affected) — interpret fault *bodies*, not HTTP status codes.

Detection on a suspect server (Expert mode, from the vendor advisory):

```bash
grep -nHP "login\(loginRequest=LoginRequest\{authenticationInfo=AuthenticationInfoBase\{username='[^']{1001,}'" "$MDS_FWDIR"/log/cpm.elg*
grep -E "ERROR.*upgrade\.base\.ReflectionUtils.*Failed to load allResourceFiles map from" "$MDS_FWDIR"/log/cpm.elg*
```

A hit on either — oversized login usernames, or resource-file paths containing `../` — plus an `fwm`/`mds` core dump at the same timestamp is a potential-exploitation signal. Mitigation to report: management behind a gateway, TCP 19009 reachable only from trusted IPs, Trusted Clients tightened.

## Phase 3 — CVE-2026-16232: SmartConsole authentication bypass (18190 + 19009 chain across the two generations)

Root cause (Rapid7 analysis): during application binds, the login service preferred an attacker-supplied **SIC distinguished name** carried inside the FwSet body over the TLS-authenticated peer certificate DN. The management server's own SIC DN is exposed during unauthenticated SIC bootstrap, so a remote client replays it: forged certificate-less application bind (claiming e.g. `CPM Server`) → application token accepted as a `DLESESSIONID` by the SOAP side → `gen-sso-token` from the legacy service → redeem the SSO ticket at the SOAP login → full SmartConsole administrator session. Exploited in the wild as a zero-day; Trusted Clients in testing did not restrict GUI clients by default. Fixed takes: the analysis compares vulnerable R81.20 Jumbo Take 146 against patched Take 158; a public PoC script exists and cleanly distinguishes vulnerable from patched targets.

Reusable review patterns for any proprietary control plane: authorization must bind to the transport-authenticated identity, never to a replayable body field; and tokens minted by a legacy binary service must not be honored by its modern SOAP/REST successor.

## Phase 4 — Gateway follow-ups

- **CVE-2024-24919** (sk182336): information disclosure on Quantum gateways running Remote Access VPN community or the Mobile Access blade; disclosed May 2024 amid observed exploitation; pre-auth leakage feeding username discovery and credential attacks. Protected from R81.20 JHF Take 65 / R81.10 Take 150 / R81 Take 99 onward.
- **CVE-2023-28130** (sk181311): authenticated command injection in the Gaia Portal **Hosts and DNS** page — the hostname field reached a shell command chain unsanitized, so delegated portal write access became OS command execution. Fixed in R82 and from R81.20 JHF Take 14 / R81.10 Take 95 / R81 Take 82 / R80.40 Take 198.
- History worth one paragraph in any report: the NG-era HTTP Security Server format-string flaw (CAN-2004-0039) — attacker-controlled error-page formatting in the inspection proxy — established the pattern of the firewall's own HTTP parsers as the crash surface.

## Phase 5 — Authenticated management enumeration

With administrator credentials, move from fingerprinting to structure via the Management API (`mgmt_cli` or HTTPS `web_api` on the management server — distinct from the Gaia API, which manages OS-level configuration on the appliance):

```bash
mgmt_cli login -u <user> -p '<PASSWORD>' -m 198.51.100.20 > id.txt
mgmt_cli show gateways-and-servers details-level full -s id.txt --format json \
  | jq '.objects[] | {name, type, ipv4_address}'
mgmt_cli logout -s id.txt
```

Gateway objects, cluster members, and management IPs fall out in one call — the pivot map for the rest of the estate.

## Phase 6 — Reporting the fix

Upgrade to current Jumbo Hotfix takes (per-CVE floors above; LivePatch is not a substitute for the 93616 fix), restrict TCP 19009 and the whole control-plane cluster to trusted management IPs, configure Trusted Clients to actually restrict GUI clients, keep management servers unreachable from the internet behind gateways, and treat any `Authentication method: application token` audit events from non-management hosts as high-signal.

## Appendix — tools in prose

`nc` for the 264 topology probe, `nmap` for control-plane sweeps, Metasploit's `checkpoint_hostname` gather module, `mgmt_cli`/`web_api` for authenticated enumeration, `curl` or Python SOAP tooling for `/cpmws/` interactions, the public CVE-2026-16232 PoC and CVE-2026-93616 checkers for patch validation, and the vendor's Expert-mode `grep` recipes for compromise detection.
