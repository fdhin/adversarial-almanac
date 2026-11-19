---
technology: "SolarWinds Orion & Access Rights Manager"
kind: bug-bounty-methodology
audience: ai-agent
status: upstream-only
family: monitoring
version: "1.1"
last-refresh: "2026-11-19"
provenance: "upstream-only; zero corpus evidence by design (owner-approved bucket-2 new-unit wave 2026-11-13) — CVE-2026-28326 gates/verdicts verified against Bishop Fox research and the BishopFox/CVE-2026-28326-check README, fix semantics against ARM 2026.2.1 release notes + rewritten from HackTricks (src/network-services-pentesting/55555-pentesting-solarwinds-arm.md), CC BY-NC 4.0, accessed 2026-11-13. v1.1 supplement (2026-11-19, claude-opus-4-8): deepened 55555 gRPC/TLS enumeration craft, BinaryFormatter/.NET-Remoting sink at transport depth, post-auth ARM attack paths, forensic artifacts, and a verified SolarWinds-family adjacent-surface map (CVE-2020-10148, CVE-2021-35211, CVE-2024-28995, CVE-2023-33225, CVE-2024-28986, CVE-2024-28987) — all CVEs re-verified against vendor advisories, NVD, CISA KEV, and primary research at write time"
---

# SolarWinds ARM bug-bounty methodology

Companion overview: [[solarwinds]]. Product context and the SUNBURST threat model live there; this file is the TCP 55555 surface only.

Scope: TLS gRPC enumeration on 55555, safe patch-state detection for CVE-2026-28326, the authentication-bypass → BinaryFormatter chain, and forensic exposure checks. Related: [[monitoring]], [[windows-secrets]].

## Phase 0 — Tooling

The Bishop Fox **CVE-2026-28326-check** detector (Python, no exploit payload — it distinguishes patched and vulnerable interceptors without using the shared key or sending a serialized object) and any TLS/gRPC-capable probe client. For exploitation on authorized targets: a .NET gadget-chain generator against `BinaryFormatter` — the sink is classic .NET Remoting deserialization, so the standard gadget families apply, subject to the denylist.

## Phase 1 — Enumerate the listener

55555 speaks HTTP/2 over TLS and **requires ALPN `h2`**:

```bash
nmap -sV --version-light -p 55555 198.51.100.40
openssl s_client -connect 198.51.100.40:55555 -alpn h2   # without ALPN you may see a reset — not a dead service
```

Check the Windows Firewall rule on the host rather than assuming the install default; ARM can bind every interface including IPv6, and multi-host deployments only need 55555 between their own components — a wider allow rule is the exposure.

## Phase 2 — Safe patch-state check (CVE-2026-28326)

Gates: ARM builds **2026.2.0.42 and earlier** vulnerable; **2026.2.1.7 or later** patched (fix shipped in ARM 2026.2.1, 2026-09-17). Run the detector against the **ARM server**, not only a collector — the verdict logic depends on the server role:

```bash
git clone https://github.com/BishopFox/CVE-2026-28326-check
cd CVE-2026-28326-check
python3 cve_2026_28326_check.py 198.51.100.40
python3 cve_2026_28326_check.py 198.51.100.40:55555
python3 cve_2026_28326_check.py -f targets.txt --json > results.json
```

Mechanics worth knowing: each probe opens a fresh TLS connection (no client cert) and sends a valid five-byte gRPC envelope — `b"\x00\x00\x00\x00\x00"`, a zero-length protobuf message — to two exact paths (the misspelled `ConnectionAuthrization` service name is part of the wire contract):

```text
/SolarWinds_ARM_GRPC_ConnectionAuthrization.ConnectionAuthorization/GetCertificate
/SolarWinds_ARM_Remoting.Remoting/Send
```

Interpret the status pair:

| `Remoting/Send` | `GetCertificate` | Verdict |
| --- | --- | --- |
| `14 UNAVAILABLE` | `0 OK` | **Vulnerable** server role (≤ 2026.2.0.42) |
| `16 UNAUTHENTICATED` | any | **Patched** (≥ 2026.2.1.7) |
| `14 UNAVAILABLE` | `14 UNAVAILABLE` | **Inconclusive** — collector, or refusing external requests |

The verdict comes from the duplex-streaming `Send` handler because it preserves the interceptor's rejection; the auth interceptor rejects the empty message before any deserialization sink, which is why the check is non-invasive. **Automation trap:** exit code 0 means "no target classified vulnerable" — patched, unaffected, and inconclusive all fold into it. Parse the per-target verdict or `--json` output instead of the exit code.

## Phase 3 — The chain, in order (authorized targets only)

1. **Handshake without a client certificate** — TLS completes; the application layer is the only gate left.
2. **Forge the fallback token** — an HMAC over caller-controlled values using a shared key shipped with the product; no nonce, server challenge, channel binding, or body binding, so tokens are generated offline. (The token mechanism existed for local child processes; the server never validated loopback origin or process identity.)
3. **Inherit a strong identity** — after accepting the token the server loads a registered-client certificate from its own store and adopts its thumbprint; downstream code authorizes you as that client. A matching certificate exists on a default installation.
4. **Deliver to the sink** — `/SolarWinds_ARM_Remoting.Remoting/Send` passes .NET Remoting payloads to `BinaryFormatter`. The in-product regex denylist of known gadget types does not constrain the .NET type space; a surviving gadget chain executes with the ARM service identity — **LocalSystem** by default, plus ARM's credentials and delegated access.

For impact assessment after execution, standard Windows host work applies: credential harvesting from the LocalSystem context and the ARM service's own stores.

## Phase 4 — Forensic exposure checks (report these)

- ARM debug logs: `Unable to validate remote request token` marks rejected anonymous attempts; successful forgeries may leave less (auth returns early).
- Where the GrantMA service is deployed, pull `GET /api/armconfig/network/Connections` — a `NetworkConnectionEntry` with `IsAuthenticated: false` and a populated `ClientCertThumbPrint` is consistent with the bypass and is not produced by normal client authentication. The list is memory-only, entries expire after three days, and the restart performed during patching clears it: collect before upgrading, and treat an empty result as unknown, not clean.
- Remediation line for the report: ARM 2026.2.1.7 or later — the fix deletes the shared-key fallback (local child processes authenticate from localhost with a fresh per-boot GUID token) and changes anonymous rejection to `16 UNAUTHENTICATED` with the certificate-registration message; network side, restrict 55555 to ARM's own collectors and clients.

---

## Supplement — 55555 enumeration craft, the sink at transport depth, post-auth ARM, and the SolarWinds-family surface map (added 2026-11-19)

*Source: independent model-drafted deepening of the 55555 methodology plus a verified adjacent-surface map for wider-scope programs. Every CVE below was re-verified at write time against the vendor Trust Center advisories, NVD, the CISA KEV catalog, and primary research (Microsoft/MSTIC, Qualys, CERT/CC VU#843464, NCC Group, Bishop Fox). Nothing above was rewritten; this extends Phases 1–4 and adds the family map. Two corrections to common mislabelling are flagged inline.*

### S1 — gRPC/TLS enumeration craft on 55555 (extends Phase 1)

The listener is HTTP/2-over-TLS with ALPN `h2`, so before the patch-state check you characterise the channel itself. Three cheap, single-connection probes, each with the output that tells you where you are.

**Inspect the TLS identity.** ARM presents a certificate even though it never *requires* one from you (the structural flaw in 3.1). Read it — the subject/issuer frequently name the product, the install, or a per-deployment CA, and the validity window dates the install:

```bash
openssl s_client -connect 198.51.100.40:55555 -alpn h2 </dev/null 2>/dev/null \
  | openssl x509 -noout -subject -issuer -dates -ext subjectAltName
# -> subject= CN=SolarWinds ARM ... (self-signed or per-install CA is the common case)
# -> ALPN protocol: h2   (if absent from the handshake, the service is not speaking gRPC here)
```

No ALPN line echoed back means either the port is not ARM gRPC or your client did not offer `h2` — re-send with `-alpn h2` before concluding anything. A connection reset with ALPN present is consistent with ARM refusing a non-mTLS *application* exchange, not a dead port (Phase 1 already warns on this; the cert read distinguishes the two).

**Try reflection — and expect it to be off.** ARM does not register the gRPC server-reflection service, and that negative result is itself a fingerprint:

```bash
grpcurl -insecure 198.51.100.40:55555 list
# -> Failed to list services: server does not support the reflection API (Unimplemented)
```

This is the normal, healthy response for ARM, and it is *why* the Bishop Fox detector hand-rolls raw HTTP/2 DATA frames rather than driving grpcurl: with reflection off and no public `.proto`, grpcurl cannot format a message for a service it cannot describe. Do not read "reflection unsupported" as "hardened" — the deserialization sink is still reachable by callers who already know the two method paths (they are in Phase 2 and are a fixed wire contract). If reflection *is* enabled on some fork or misconfiguration, `list` returns the service set and `grpcurl -insecure describe SolarWinds_ARM_Remoting.Remoting` hands you the method and message descriptors for free — treat that as an unexpected gift and snapshot it.

**Health/service enumeration.** Probe the standard gRPC health service; ARM does not implement it, which is one more corroborating tell and keeps you from mistaking `UNIMPLEMENTED` for a down service:

```bash
grpcurl -insecure 198.51.100.40:55555 grpc.health.v1.Health/Check
# -> ... code = Unimplemented (12)   # ARM exposes no health service — expected
```

Decision point after S1: if the cert reads as SolarWinds/ARM, ALPN `h2` is negotiated, reflection returns `Unimplemented`, and health returns `12`, you have positively fingerprinted ARM 55555 without touching the auth or deserialization paths — now run the Phase 2 Bishop Fox detector for the *version* verdict. Keep the enumeration here to one connection per probe; this is reconnaissance, not the exploit.

### S2 — the BinaryFormatter sink at transport depth (extends Phase 3 step 4)

Why `BinaryFormatter` is the sink, stated precisely, because the report's remediation and the gadget choice both depend on it. ARM tunnels legacy **.NET Remoting** messages inside the gRPC `Remoting/Send` stream. .NET Remoting's wire format is a `BinaryFormatter`-serialized object graph: the `RemotingSurrogateSelector`/`ObjRef` machinery calls `BinaryFormatter.Deserialize` on the incoming message body to reconstruct the method call and its arguments. `BinaryFormatter.Deserialize` is type-blind — it reads the embedded `.NET` type names from the stream and instantiates them, running their deserialization callbacks (`ISerializable` constructors, `[OnDeserialized]` methods, property setters) *before* any application code inspects the result. That pre-inspection execution window is the entire vulnerability class; it is why Microsoft deprecated `BinaryFormatter` outright and why no amount of post-deserialization validation closes it.

**What the gadget needs.** A working chain against this sink must satisfy three constraints, not just "be a ysoserial.net payload":

1. **A delegate-confusion or property-oriented trigger that fires during deserialization.** The canonical pick is **TypeConfuseDelegate** (James Forshaw's gadget — it abuses a `MulticastDelegate`/`Comparer` to redirect a delegate invocation to `Process.Start`). It is `BinaryFormatter`- and `LosFormatter`-compatible (ysoserial.net's own matrix), which is exactly the formatter ARM uses. Generate with `ysoserial.exe -f BinaryFormatter -g TypeConfuseDelegate -c "<command>"`.
2. **A Remoting-shaped envelope.** Vanilla `BinaryFormatter` gadgets target a raw object stream; .NET Remoting wraps the graph in its own message structure (`IMethodCallMessage`). A raw TypeConfuseDelegate blob may deserialize but never reach invocation unless it is delivered where Remoting expects a message — so the payload is framed as (or smuggled through) the Remoting `Send` call, not posted as a bare serialized object. This is the practical gap between "I can deserialize arbitrary types" and "I get code execution," and it is where unmodified public PoCs stall.
3. **A type that survives the in-product denylist.** ARM ships a regex denylist of known-bad gadget type names. A denylist over the unbounded .NET type space cannot be complete; the task is to select (or lightly re-namespace) a chain whose type names the regex does not match while the runtime still binds them. Report the *mechanism* — denylist, not allowlist, on an unbounded domain — because that is the root cause the vendor must fix, not the specific string you slipped through.

The execution identity is **LocalSystem** (ARM's default service account), so a single successful `Send` is host-level RCE plus ARM's stored credentials (S3). CTF framing: this is the textbook "deserialization behind a weak front-door auth" box — in a CTF you would be handed the open port and a hint about the formatter; in a bounty the work that earns the payout is the *safe* half (Phase 2 patch-state verdict, S1 fingerprint, forensic proof in S4) and a single-command PoC against an authorized target, never a sprayed gadget.

### S3 — post-auth attack paths on ARM itself (new, authorized targets only)

Code execution is not the end of the ARM story — ARM's *purpose* makes it a credential and access-map trove even before you pop a shell, and these are the findings a defender most needs to hear.

**The classic ARM finding — stored directory/scan credentials.** ARM cannot do its job without standing credentials: a service account (local administrator on the ARM server by SolarWinds' own requirement) plus one or more **scan accounts** it stores to authenticate against Active Directory, file servers, SharePoint, and Exchange on a schedule. Those credentials live in ARM's configuration/database so scans can run unattended. Post-exploitation in the LocalSystem context, they are recoverable — and because the service account is local admin on the box and the scan accounts are privileged readers across the audited estate, recovering them is lateral movement into every system ARM audits. Report path: demonstrate access to the credential store (not the plaintext of production secrets — capture the *fact* of recoverability, redact the values), and frame impact as "ARM's scan identities grant read access across the audited domain(s)."

**Report generation as an exfiltration primitive.** ARM's reason to exist is to answer "who can access what." Its report engine will, on request from an authorized-looking caller, render the full permission map: group memberships, NTFS/share ACLs, who-has-access-to-which-folder, AD account inventories, and permission-change history. That output is a complete targeting map of the environment — more useful to an intruder than many shells. Where the GrantMA/API surface is reachable, enumerate what report and config endpoints answer to your adopted identity before generating anything, and in the write-up treat "attacker can generate the organisation's full access-rights report" as a High-impact data-exposure finding in its own right, independent of RCE.

**AD credentials in ARM configs.** Beyond the live scan accounts, ARM configuration files and backups can carry connection strings and credential material for the SQL back end and directory connectors. Grep the ARM program-data and config tree (post-auth, authorized) for connection strings, stored credential blobs, and the config entries that name the scan/service accounts; each is a reportable exposure and a pivot.

### S4 — forensic artifacts that prove exposure (extends Phase 4)

What to collect, on the ARM host and the network, to evidence an exposure claim — for your report and for a blue team verifying the same:

- **ARM debug log, rejected attempts:** `Unable to validate remote request token` marks an anonymous/forged-token attempt the interceptor rejected. Presence proves someone exercised the fallback path; absence proves nothing (successful forgeries return early and log less — §4 already notes this, so never read a clean log as "not targeted").
- **GrantMA connection ledger (pre-upgrade only):** `GET /api/armconfig/network/Connections` — a `NetworkConnectionEntry` carrying `IsAuthenticated: false` together with a populated `ClientCertThumbPrint` is the bypass's signature and is not produced by normal client auth. Memory-only, three-day expiry, cleared by the patch restart: collect before upgrading, and treat empty as *unknown*, not clean.
- **Windows event and service telemetry:** correlate the above with process creation under the ARM service account (LocalSystem) around the request time — a `BinaryFormatter`-driven `Process.Start` surfaces as a child process of the ARM service, which is the host-side proof that `Send` reached the sink. On an engagement, capture the parent/child process pair as the execution evidence rather than relying on the gRPC log alone.
- **Network evidence:** a single authorized capture on the segment showing a `Send` request to 55555 without a client certificate in the TLS handshake corroborates "mTLS requested but not required" directly on the wire — the deployment-boundary property no scanner asserts for you.

### S5 — SolarWinds-family adjacent-surface map (for wider-scope programs)

If the program scope is the *organisation* rather than ARM alone, SolarWinds products cluster on the same management VLANs and share a credential-rich, high-privilege posture. The SUNBURST supply-chain event stays context (see [[solarwinds]]), not a technique. Below: one-paragraph class summaries with detection fingerprints — enough to recognise and prioritise each surface, not full methodologies. **Every CVE here was re-verified against the vendor advisory, NVD, and CISA KEV at write time; two are commonly mislabelled and corrected inline.**

**Orion Platform — API authentication bypass (CVE-2020-10148, CVSS 9.8, CWE-288/306, CISA KEV).** The Orion API has privileged reach across every Orion component. The bypass: appending a `PathInfo` segment that the request handler treats as a resource exempt from authorization — the reported trigger strings are `WebResource.adx`, `ScriptResource.adx`, `i18n.ashx`, or `Skipi18n` in the request path, which cause Orion to set an internal `SkipAuthorization` flag and serve the API call unauthenticated. This is the flaw SolarWinds indicated was used to install the **SUPERNOVA** in-memory .NET web shell (distinct from the SUNBURST backdoor). Affected: Orion Platform 2019.4 HF5, 2020.2 with no hotfix, 2020.2 HF1; fixed in 2019.4 HF6 and 2020.2.1 HF2. *Detection fingerprint:* the Orion Web Console / `/Orion/` and `/api/` paths of a monitoring server; probe (authorized) an API route with one of the `Skipi18n`-class suffixes and watch for a 200 where an unauthenticated 401/403 is due. CERT/CC: VU#843464.

**SolarWinds Platform (Orion Web Console) — authenticated deserialization to SYSTEM (CVE-2023-33225, CVSS 7.2).** *Correction: this CVE is frequently attributed to Web Help Desk — it is not.* It is a deserialization-of-untrusted-data flaw (vendor title; NVD also tags CWE-697 incorrect comparison) in the **SolarWinds Platform** Web Console that lets a user **with administrative Web Console access** execute arbitrary commands with **SYSTEM** privileges — a privilege-boundary break (admin→SYSTEM on the host) rather than a pre-auth entry. *Detection fingerprint:* same Orion Web Console surface as above; relevant once you hold or can reach admin-level console access. Treat as a post-auth escalation to chain after any console-credential finding.

**Serv-U (managed file transfer) — SSH pre-auth RCE (CVE-2021-35211, CVSS 10, CWE-787, CISA KEV).** *This is the real "Serv-U SSH CVE."* An out-of-bounds write ("remote memory escape") in Serv-U's **SSH implementation**, present only when **SSH is enabled**. Successful exploitation makes Serv-U throw an exception, then overwrites the exception handler with attacker code → RCE. Exploited as a zero-day by the China-linked group Microsoft tracks as **DEV-0322**, later by **TA505** for initial access; SolarWinds states it is unrelated to SUNBURST. Affected ≤ 15.2.3 HF1; fixed 15.2.3 HF2. *Detection fingerprint:* Serv-U banner on the SSH port (commonly 22 on an MFT host); version ≤ 15.2.3 HF1 with SSH enabled. Host-side proof: exception entries in `DebugSocketLog.txt` (not conclusive alone — the file logs unrelated exceptions too).

**Serv-U — unauthenticated path traversal / file disclosure (CVE-2024-28995, CVSS 8.6, CWE-22, CISA KEV).** *Correction: this is not an SSH bug.* It is a directory-traversal flaw in the Serv-U **web interface** that lets an **unauthenticated** attacker read arbitrary files off the host (e.g. `/etc/passwd`, Windows `win.ini`, Serv-U's own config) via traversal sequences in a GET request — on affected builds the plain-`/../` filter was bypassable with encoded or mixed-separator forms. Affected 15.4.2 HF1 and earlier; fixed 15.4.2 HF2. Credited to Hussein Daher; added to KEV 2024-07-17. *Detection fingerprint:* Serv-U web client (HTTP/HTTPS management port); a single authorized traversal GET against a known-present file that returns the file body instead of a 400/403. High-value reads: Serv-U credential/config files that then feed CVE-2021-35211-class or credential-reuse follow-ups.

**Web Help Desk — Java deserialization RCE (CVE-2024-28986, CVSS 9.8, CWE-502, CISA KEV).** *This — not CVE-2023-33225 — is the Web Help Desk RCE.* A Java object-deserialization flaw in **Web Help Desk** enabling remote code execution; SolarWinds initially reported it unauthenticated, later stated exploitation required authentication, but it was added to KEV and ~800 instances were internet-exposed at disclosure. Affected all WHD versions through 12.8.3 HF1; fixed by the 12.8.3 hotfix line. *Detection fingerprint:* the Web Help Desk login portal (a Java/Tomcat app, distinct banner from Orion); version ≤ 12.8.3 HF1.

**Web Help Desk — hardcoded credentials (CVE-2024-28987, CVSS 9.1, CWE-798, CISA KEV).** A built-in hardcoded credential pair in **Web Help Desk** acts as a backdoor: an unauthenticated remote attacker can log in and read or modify help-desk data (tickets routinely carry credentials, reset tokens, and internal infrastructure detail). Affected through 12.8.3 HF1; fixed in 12.8.3 HF2; KEV 2024-10-15. *Detection fingerprint:* same WHD portal; test the known credential pair (authorized only) or, non-intrusively, gate on version ≤ 12.8.3 HF1. Pairs naturally with CVE-2024-28986 on the same host.

Prioritisation across the family: Serv-U and Web Help Desk expose **pre-auth** internet-facing entry points (CVE-2021-35211, CVE-2024-28995, CVE-2024-28986/28987) and are the first reach on a wide-scope external program; Orion/SolarWinds-Platform findings (CVE-2020-10148 pre-auth; CVE-2023-33225 post-auth) and ARM 55555 sit on the management plane and are the pivot once you are internal. All of them share the product class's defining property — high privilege, credentialed to everything they touch — so each is a tier-0-adjacent finding, not a routine web bug.

### References (supplement)

- SolarWinds Trust Center — CVE-2021-35211 (Serv-U remote memory escape) — https://www.solarwinds.com/trust-center/security-advisories/cve-2021-35211
- SolarWinds Trust Center — CVE-2024-28995 (Serv-U path traversal / local file disclosure) — https://www.solarwinds.com/trust-center/security-advisories/cve-2024-28995
- SolarWinds Trust Center — CVE-2023-33225 (SolarWinds Platform deserialization) — https://www.solarwinds.com/trust-center/security-advisories/CVE-2023-33225
- SolarWinds Trust Center — CVE-2024-28987 (Web Help Desk hardcoded credential) — https://www.solarwinds.com/trust-center/security-advisories/cve-2024-28987
- Microsoft MSTIC — SolarWinds Serv-U zero-day (DEV-0322), CVE-2021-35211 — https://www.microsoft.com/en-us/security/blog/2021/07/13/microsoft-discovers-threat-actor-targeting-solarwinds-serv-u-software-with-0-day-exploit
- NCC Group — TA505 exploits CVE-2021-35211 for initial access — https://research.nccgroup.com/2021/11/08/ta505-exploits-solarwinds-serv-u-vulnerability-cve-2021-35211-for-initial-access/
- Qualys ThreatPROTECT — SUPERNOVA / Orion API auth bypass (CVE-2020-10148) — https://threatprotect.qualys.com/2020/12/30/supernova-solarwinds-orion-api-authentication-bypass-vulnerability-cve-2020-10148/
- CERT/CC Vulnerability Note VU#843464 (CVE-2020-10148) — https://kb.cert.org/vuls/id/843464
- Rapid7 — CVE-2020-10148 Orion API auth bypass and RCE analysis — https://www.rapid7.com/blog/post/ra-cve-2020-10148-solarwinds-orion-api-authentication-bypass-and-rce-analysis/
- Help Net Security — CVE-2024-28986 Web Help Desk RCE — https://www.helpnetsecurity.com/2024/08/15/cve-2024-28986/
- NVD — CVE-2024-28986 (Web Help Desk Java deserialization) — https://nvd.nist.gov/vuln/detail/CVE-2024-28986
- ysoserial.net (TypeConfuseDelegate gadget; BinaryFormatter/LosFormatter support) — https://github.com/pwntester/ysoserial.net
- Metasploit docs — .NET deserialization gadget/formatter matrix — https://docs.metasploit.com/docs/development/developing-modules/libraries/deserialization/dot-net-deserialization.html
- grpcurl (reflection behaviour, list/describe, `-protoset` fallback) — https://github.com/fullstorydev/grpcurl
- SolarWinds ARM documentation — scan/service account credentials — https://documentation.solarwinds.com/en/success_center/arm/content/enter-arm-server-credentials.htm

*Supplement authored 2026-11-19 by claude-opus-4-8. Sources: SolarWinds Trust Center advisories (CVE-2021-35211, CVE-2024-28995, CVE-2023-33225, CVE-2024-28986, CVE-2024-28987), NVD, CISA KEV catalog, Microsoft MSTIC, NCC Group, Qualys ThreatPROTECT, CERT/CC VU#843464, Rapid7, ysoserial.net, Metasploit .NET-deserialization docs, grpcurl, and the SolarWinds ARM product documentation — each consulted and verified at write time. Two prevalent mislabellings corrected against primary advisories: CVE-2023-33225 is a SolarWinds Platform (Orion Web Console) bug, not Web Help Desk; CVE-2024-28995 is a Serv-U web path-traversal, not an SSH flaw (the Serv-U SSH RCE is CVE-2021-35211). All examples use the reserved documentation ranges; run every probe single-request, low-volume, and only under authorized scope.*
