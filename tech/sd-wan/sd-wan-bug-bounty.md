---
technology: Cisco SD-WAN control plane (Catalyst, Viptela lineage)
kind: bug-bounty-methodology
audience: ai-agent
status: upstream-only
family: network
version: "1.1"
last-refresh: "2026-11-19"
provenance: "upstream-only; zero corpus evidence by design — handshake mechanics, pivot details, and fixed-release table grounded against Cisco PSIRT cisco-sa-sdwan-rpa2-v69WY2SW and cisco-sa-sdwan-webauth-xr8beuuU plus Rapid7's CVE-2026-20182 analysis and CVE-2026-76504 ETR + rewritten from HackTricks (src/network-services-pentesting/12346-udp-pentesting-cisco-sd-wan-control-plane.md), CC BY-NC 4.0, accessed 2026-11-13. v1.1 supplement (2026-11-19): fabric inventory + port-to-role fingerprints, certificate/serial trust-root handling, DTLS handshake mechanics, vManage web surface (sessions/multitenancy) and the historical auth-bypass lineage (CVE-2021-1284/-1468/-1515/-1302, CVE-2023-20254 — with the correction that CVE-2021-1472/-1473 are Cisco RV Series, not SD-WAN), hardening-read-as-surface, post-access discipline, and CTF/lab notes — grounded against the Cisco Catalyst SD-WAN Hardening Guide (rev 1.1, 2026-02-09), Cisco advisory cisco-sa-sdw-auth-bypass-65aYqcS2 (CVE-2021-1284) and the CVE-2023-20254 record, plus Cisco control-connection troubleshooting doc 222302"
---

# Cisco SD-WAN control plane bug-bounty methodology

Companion overview: [[sd-wan]].

Scope: the vdaemon DTLS peering service on UDP 12346, the NETCONF pivot on TCP 830, and the Manager HTTPS API authentication path. Fabric exploitation beyond these primitives (OMP route injection, policy pushes) is post-access network engineering.

## Phase 0 — Identify the control plane

```bash
nmap -sU -p12346 198.51.100.10
nmap -sT -p22,830 198.51.100.10
```

UDP 12346 open marks a controller (or manager/validator) even without a handshake; TCP 830 marks the NETCONF service that becomes the pivot target after a peering compromise. DTLS does not banner — confirmation requires handshake-capable tooling or the authenticated paths below. Distinguish which component you have (Controller vs Manager vs Validator) before applying version tables: the Manager additionally exposes the HTTPS API surface.

## Phase 1 — Peering-handshake bypass class (CVE-2026-20127 / CVE-2026-20182)

The verified mechanics, from the vendor advisory and the disclosing researchers' analysis: the peer exchange is DTLS handshake → CHALLENGE (message type 8) → CHALLENGE_ACK (type 9) → CHALLENGE_ACK_ACK (type 10). In the vulnerable builds the DTLS layer accepted any certificate — the certificate-verification failure was logged but the session proceeded — and the real authorization happened in `CHALLENGE_ACK` handling, where the **claimed device role** is verified. CVE-2026-20182 (CVSS 10.0, CWE-287): the verification function had no branch for the vHub role claim, and execution fell through to marking the peer authenticated — so an attacker completed DTLS with any certificate, claimed vHub, said hello, and transitioned to an UP authenticated peer. CVE-2026-20127 was an earlier, distinct flaw in the same service, exploited in the wild since February 2026.

Authorized validation: work from the published analysis and PoC tooling against a disposable lab target of the exact affected train, and require evidence of the authenticated-peer state or the post-pivot artifact (below) before reporting — a timeout or a closed DTLS session is not a finding. The hunt pattern generalizes to any proprietary control plane: attacker-controlled role selection, missing default-deny branches, pre-auth handshake messages.

## Phase 2 — Post-pivot: authorized_keys and NETCONF as vmanage-admin

With a peer session on a vulnerable controller, the documented privileged operation appended attacker-controlled data to `/home/vmanage-admin/.ssh/authorized_keys` — turning the control-plane foothold into durable host access:

```bash
ssh -i <loot_key.pem> -p 830 vmanage-admin@198.51.100.10
```

SSH on 830 speaks NETCONF, not a shell: configuration and state retrieval are available even where interactive commands are not — for the attacker, the whole fabric's config; for the owner, the whole incident. Vendor incident guidance: collect `request admin-tech` evidence and review control connections **before** upgrading a suspect system, because patching over a compromise leaves the injected keys and accounts in place.

## Phase 3 — Manager API authentication bypass (CVE-2026-76504)

Separate service, separate bug class: improper URI-encoding handling (CWE-177) let an unauthenticated request bypass the authentication rule guarding the login endpoint and reach the API **as the admin user**. The disclosed shape percent-encoded one character of the request target (the `j` of `j_security_check` as `%6a` is only the example — the vendor states encoding any single character triggers the path):

```http
POST /%6a_security_check HTTP/1.1
Host: manager.example.com
```

Validation discipline: preserve the raw request target byte-for-byte, and require a usable authenticated session or a successful protected API call as evidence — a 200 from the login handler alone proves nothing. This is the raw-vs-normalized URI authorization-bypass pattern: test every auth-rule boundary with encoded variants of ordinary characters, not just the documented ones.

Fixed releases (vendor/ETR table): 20.9 → 20.9.10.1, 20.12 → 20.12.8.2, 20.15 → 20.15.6.1, 20.18 → 20.18.4.1, 26.1 → 26.1.2.1, 26.2 → 26.2.1; earlier trains must migrate. Exploited in the wild, CISA KEV-listed with an emergency remediation window — version-gate any Manager you can reach.

## Phase 4 — Detection and log review

- Manager side: decode raw request targets in the service-proxy access log and flag any whose decoded path resolves to `/j_security_check` while differing from the raw bytes. Searching for the single `%6a` spelling misses every other single-character variant — decode first, compare second.
- Correlate hits in the manager server log against the source IP and expected management-network posture; usernames beginning with the reserved internal prefix can appear in legitimate operation, so source correlation decides.
- Controller side: audit `/home/vmanage-admin/.ssh/authorized_keys` for appended keys after control-plane events, and review control-connection state for unexpected peers.

## Phase 5 — Reporting the fix

Upgrade every control component to a fixed release (the table above for managers; the corresponding controller/validator trains for the peering flaws), treat the vendor's Live Protect shield as temporary partial coverage only, restrict UDP 12346 and TCP 830 to fabric-internal address ranges, never expose managers to the internet, and treat compromised-then-patched systems as still compromised until keys, accounts, and configs are audited.

## Appendix — tools in prose

`nmap` for UDP/TCP triage, `ssh` in NETCONF mode (or a NETCONF client library) for the 830 pivot, `curl` with raw request targets for the API bypass, Python for percent-decoding log triage, and the published analysis/PoC tooling for lab reproduction of the peering chain.

---

## Supplement — fabric inventory, trust root, and the web surface (added 2026-11-19)

*Source: external research round (November 2026), re-verified against the Cisco Catalyst SD-WAN Hardening Guide (rev 1.1, 2026-02-09), Cisco advisory cisco-sa-sdw-auth-bypass-65aYqcS2 (CVE-2021-1284), the CVE-2023-20254 record, and Cisco control-connection troubleshooting doc 222302. This extends Phases 0–5 with the inventory step that comes before them, the trust model the handshake bypasses attack, the web surface sitting beside the API, and the discipline that keeps a control-plane proof-of-access from becoming a fabric outage. Nothing above was rewritten.*

A naming note that runs through everything below: Cisco rebranded the Viptela components, and advisories now mix both vocabularies. **Validator** = vBond (orchestrator), **Controller** = vSmart, **Manager** = vManage, and the WAN edges are vEdge (Viptela OS) or cEdge (IOS-XE SD-WAN). Read every port rule and CVE against both names.

### Where the money is (control-plane triage priority)

| Priority | Class | Where it lives | Typical severity |
|---|---|---|---|
| 1 | Peering-handshake / role-claim bypass | vdaemon DTLS UDP 12346 (Phase 1; CVE-2026-20182/-20127) | Critical (CVSS 10.0, pre-auth fabric join) |
| 2 | Manager API auth bypass | `/dataservice` + `/j_security_check` (Phase 3; CVE-2026-76504) | Critical (unauth admin) |
| 3 | Trust-root weakness | cert validation + authorized-serial handling (below) | Critical (forge membership) |
| 4 | Multitenant tenant-isolation break | Manager web/API tenant context (below; CVE-2023-20254 class) | High (cross-tenant) |
| 5 | NETCONF pivot exposure | TCP 830 reachable off the Manager path (below) | High (config read/write) |
| 6 | Web session handling | JSESSIONID/CSRF lifecycle on the Manager UI (below) | Medium–High |
| 7 | Hardening-inversion misconfig | internet-exposed 443/22/830, self-signed cert, SNMPv1/2 (below) | Medium |

Do the inventory (S1) before any of it — applying a Controller version table to a Validator, or an API probe to a box with no web surface, wastes requests and muddies evidence.

### S1 — Fabric inventory: which port tells you which role

All three control components (Validator, Controller, Manager) listen on the same DTLS base port, so a single open UDP 12346 tells you "a control-plane component" and nothing more. Role falls out of the *combination* of ports and of where the box sits in the onboarding flow:

```bash
# Control-plane triage sweep (authorized hosts only)
nmap -sU -p12346,12446,12546 198.51.100.10        # DTLS base + per-core offsets
nmap -sT -p22,443,830,23456 198.51.100.10          # SSH, web/API, NETCONF, TLS-transport base
```

| Port / protocol | Open on | What it identifies |
|---|---|---|
| **UDP 12346** (DTLS, Core 0 base) | Validator, Controller, Manager | "a control component" — not which one |
| **UDP 12446 / 12546 / …** (+100 per core) | multi-core Controllers/Managers | higher core count ⇒ Manager/Controller sizing, not an edge |
| **UDP 12346+n, 12366+n, 12386+n, 12406+n, 12426+n** (n=0–19, port-hopping) | edges cycling the offset | the *client* side of a control connection |
| **TCP 23456** (TLS, Core 0 base; +100/core → 23556, 23656…) | Controller, Manager (TLS transport / NAT fallback) | control component using TLS instead of DTLS |
| **TCP 443** (HTTPS) | **Manager only** | the Manager — web UI + `/dataservice` REST API |
| **TCP 830** (NETCONF) | Controller, Validator, Manager | a NETCONF target — the Phase 2 pivot destination |
| **TCP 22** (SSH) | all components | management + OS fingerprint (Viptela custom Linux vs IOS-XE) |
| the public ZTP/PnP first-contact endpoint | **Validator** | the Validator — the one address reachable "from any source" |

Decision tree: **TCP 443 serving a Manager login ⇒ Manager**; a control component that is the published, internet-reachable ZTP contact point with no web surface ⇒ **Validator**; a control component carrying OMP peering with no web and not the ZTP target ⇒ **Controller**. Distinguishing a Validator from a Controller purely from the outside, without a handshake, is genuinely hard — the Validator is the one ZTP points edges at first.

**Expected banners / tells:**

```bash
# DTLS does not banner — confirmation needs handshake-capable tooling (Phase 1).
# SSH on 22: the OS underneath. Viptela OS and IOS-XE SD-WAN differ in the banner.
nc -w3 198.51.100.10 22            # -> SSH-2.0-OpenSSH_... (note the version string)

# NETCONF on 830: an SSH transport that, post-auth, speaks the NETCONF <hello>
#   capabilities XML — urn:ietf:params:netconf:base:1.0/1.1 plus Cisco/Viptela models.

# Manager on 443: the web tells, in order of strength
curl -sk https://198.51.100.10/ -o /dev/null -w '%{http_code}\n'      # login page
curl -skI https://198.51.100.10/dataservice/client/server             # REST API base -> JSON
curl -ski https://198.51.100.10/ | grep -iE 'jsessionid|xsrf|location'
```

A `JSESSIONID` cookie, an `X-XSRF-TOKEN`/CSRF cookie, a `/dataservice/...` path that returns JSON, and a `/j_security_check` form-login target (Spring Security — the same endpoint Phase 3's CVE-2026-76504 attacks) together identify a Manager with near certainty. Record all of them verbatim as your Manager baseline.

### S2 — The trust root: certificates and the authorized-serial file

The whole fabric is a certificate PKI with an allowlist bolted on, and that is where membership is decided. A device is trusted onto the overlay only when **three** facts all hold:

1. its certificate chains to a **root CA** the controllers trust (the enterprise root, a trusted public CA, or Cisco's PKI);
2. the **serial number + chassis ID** carried in that certificate appears on the **authorized serial-number file** — the digitally-signed WAN-edge list, retrieved from the Plug and Play (PnP) Connect portal, synced into the Manager, and pushed down to the Validator and Controllers;
3. the **organization name** in the certificate matches the overlay's configured org name.

The Validator enforces this at first contact: it extracts the serial from the presenting device's certificate, checks it against the authorized list, compares the org name, and — if the serial is not on the list or the org name mismatches — tears the tunnel down and logs it. **Whoever can forge or obtain a certificate that satisfies all three joins the fabric as a legitimate peer.** That is not a hypothetical: it is exactly the invariant CVE-2026-20182 broke, by making step (1) non-fatal (the certificate-verification *failure was logged but the session proceeded*) and then falling through the role check. The trust-root view reframes Phase 1 — the handshake bypass is a *certificate-validation* bug, and certificate validation is the thing to probe wherever the fabric makes a trust decision.

**Reading the trust decisions (authorized assessment, on-box or via NETCONF):**

```bash
show sdwan control connections            # current peers and their state (should be UP/authenticated)
show sdwan control connections-history    # the Local Error / Remote Error columns — the gold here
show control connections-history          # vEdge/Viptela-OS equivalent
show sdwan certificate serial             # the installed/authorized serials
```

The error codes in the history output are a validation-failure dictionary — each one is a place the trust check *fired*, and a place that, if it ever logs-then-proceeds, is a finding:

| Code | Meaning | What it proves about validation |
|---|---|---|
| `CRTVERFL` | Certificate verification failed | peer cert did not chain to a trusted root (or clock skew). A `CRTVERFL` immediately followed by the session going **UP** is the logged-not-enforced tell — the CVE-2026-20182 signature. |
| `CTORGNMMIS` | Organization-name mismatch in the cert | the device is not in this overlay's org — step (3) working |
| `DCONFAIL` | DTLS connection failed | transport never formed (often cannot reach the Validator) |
| `DHSTMO` | DTLS handshake timeout | handshake started, never completed |
| `NOZTPEN` / `BIDNTVRFD` | No/bad chassis entry in ZTP / board ID not verified | step (2) — serial/chassis not on the authorized list |

**Detection of weak or missing cert validation, black-box:** you cannot read the controller's logs from outside, so the test *is* Phase 1 — complete a DTLS handshake presenting a self-signed / untrusted certificate and observe whether the peer still transitions to an authenticated state. A handshake that proceeds past certificate presentation with an untrusted cert is the external evidence that step (1) is not enforced; a closed session or `DHSTMO`-equivalent timeout is the secure behavior. On an authorized on-box assessment, grep the vdaemon/vdebugd logs for `CRTVERFL` events that are *not* followed by a torn-down connection. Hardening-side tells that weaken the root: the Manager still serving its **default self-signed web certificate** (it should be replaced with an enterprise/public-CA cert), and any virtual edge onboarded by chassis-ID+token whose authorization was never scoped — virtual devices (vedge-cloud, c8000v) carry no built-in serial, so their allowlist entry is entirely administrative.

### S3 — DTLS handshake mechanics (for the Phase 1 bypass)

Phase 1 names the Viptela peering exchange (CHALLENGE / CHALLENGE_ACK / CHALLENGE_ACK_ACK, message types 8/9/10). That exchange rides *on top of* a standard DTLS 1.2 handshake, and the bypass lives in the seam between the two layers:

```
Attacker (claims a role)                 Controller / Validator
  ClientHello  ------------------------------->
               <------------------ HelloVerifyRequest (stateless cookie)
  ClientHello (+ cookie) --------------------->
               <-- ServerHello, Certificate, ServerKeyExchange,
                   CertificateRequest, ServerHelloDone
  Certificate, ClientKeyExchange,
  CertificateVerify, [ChangeCipherSpec], Finished -->
               <------------------ [ChangeCipherSpec], Finished
  == DTLS session established (should be MUTUALLY authenticated) ==
  CHALLENGE (type 8) ------------------------->
               <------------------ CHALLENGE_ACK (type 9)   # role verified HERE
  CHALLENGE_ACK_ACK (type 10) ---------------->
               == peer marked UP / authenticated ==
```

Two mechanics matter to the attacker. First, the `HelloVerifyRequest` **cookie is anti-spoofing, not authentication** — it only proves the client can receive at its claimed source address, so it stops blind/off-path DoS but does nothing against a direct attacker who completes the round-trip. Do not mistake "the cookie exchange worked" for "the handshake authenticated me." Second, the handshake is specified as *mutual* auth: the server's `CertificateRequest` demands the client present a certificate, and `CertificateVerify` proves possession of the key. The bypass class is precisely a failure to make a **bad client certificate fatal** — the DTLS layer logs the verification failure (`CRTVERFL`) and proceeds, so the real authorization collapses onto the later CHALLENGE_ACK role check, and CVE-2026-20182 made *that* fall through for the vHub role claim. The generalizable hunt: in any proprietary control plane layered over (D)TLS, test whether an untrusted client certificate actually terminates the handshake, and whether the application-layer authorization has a default-deny branch for every role value it does not recognize.

### S4 — The vManage/Manager web surface beyond API auth

Phase 3 attacks the Manager's API authentication (CVE-2026-76504). The web surface around it has its own lineage and its own session machinery.

**Historical auth-bypass lineage — and a citation correction.** The brief's "CVE-2021-1472 auth-bypass class" is a trap worth flagging in your own reports: **CVE-2021-1472 and CVE-2021-1473 are Cisco Small Business RV-Series routers** (web-management auth bypass and command injection), *not* SD-WAN. The actual vManage auth-bypass of that era is **CVE-2021-1284** (advisory cisco-sa-sdw-auth-bypass-65aYqcS2, CVSS 8.8, AV:A): a flaw in the **web-based messaging service interface** where insufficient authorization checks let an unauthenticated, *adjacent* attacker — one who can already reach an associated vEdge — send crafted HTTP requests and gain **read and write** access to the vManage system and the devices it manages (fixed 20.3.1 / 20.4.1 / 20.5.1). The surrounding 2021 cluster is the rest of the lineage:

| CVE | Class | Note |
|---|---|---|
| CVE-2021-1284 | Web messaging-interface authZ bypass → unauth read/write | the real "vManage auth bypass"; adjacent, needs vEdge reach |
| CVE-2021-1468 | Cluster-mode unauthorized message processing (CVSS 9.8) | no workaround; cluster deployments only |
| CVE-2021-1515 | Improper access control on API endpoints in multi-tenant mode | fixed 20.4.1 |
| CVE-2021-1302 | Authenticated attacker bypasses authZ, connects to other tenants | multi-tenant isolation |
| CVE-2023-20254 | Multi-tenant **session-management** bypass | authenticated tenant-A user reaches tenant B on the same instance; multi-tenant feature only |

The pattern across all of them: the Manager's authorization is *context-dependent* (which tenant, which session, which message interface), and the bugs are failures to re-check context, not failures to authenticate.

**Session handling.** The Manager issues a `JSESSIONID` and a CSRF token (`X-XSRF-TOKEN` / cookie). Test the lifecycle, not just the login:

```bash
# Does the session id rotate on authentication? (fixation check)
curl -sk https://manager.example.com/ -c pre.txt -o /dev/null          # grab pre-auth JSESSIONID
# ...authenticate via /j_security_check carrying that cookie, then compare the post-auth JSESSIONID
# Does a state-changing /dataservice call succeed without the XSRF token?
curl -sk -X POST https://manager.example.com/dataservice/... -b "JSESSIONID=$SID"
```

Findings: a `JSESSIONID` that is not reissued on login (session fixation), a CSRF token that is accepted when absent or cross-session, sessions that outlive the configured lifetime (default **24h**, idle **30 min** — long defaults are themselves worth a hardening note), and tokens valid after logout.

**Tenant / context separators in multitenancy.** Manager multitenancy is a *provider → tenant* model: a provider administers the instance, and each tenant is meant to be isolated. The separator is a **tenant-context token** — the `VSessionId` a provider obtains (via `/dataservice/tenant/{tenantId}/vsessionid`) and then presents on subsequent `/dataservice` calls to act within that tenant. The CVE-2023-20254 class is exactly a failure to bind that context to the session: manipulate or swap the tenant-context token / `VSessionId` and attempt a second tenant's `/dataservice` resources. This is the SD-WAN instance of the two-account BOLA swap from the FastAPI guide — two tenants, one object reference, one context token — so run it the same way: establish access as tenant A, enumerate a resource you own, then request tenant B's equivalent while varying only the tenant separator. A 200 with B's data is a cross-tenant isolation break.

### S5 — Post-access discipline: the line you do not cross

A peer session, a NETCONF channel on 830, or an authenticated `/dataservice` call *proves the vulnerability*. That is the whole finding. Do **not** convert it into fabric manipulation:

- **Never push OMP routes or policies.** Injecting an OMP route or a control/data policy reroutes production traffic across real sites — it is destructive, irreversible mid-flight, and categorically outside single-request, evidence-first testing. The *impact narrative* ("an attacker at this access level can inject OMP routes and redirect or blackhole site traffic") is demonstrated by the access you hold, not by executing it.
- **Scope NETCONF to reads.** Prove the pivot with a read-only `<get-config>` / `<get>` capability exchange or a narrowly-scoped config retrieval; do not `<edit-config>`. The `<hello>` capabilities exchange plus one scoped read is sufficient proof.
- **Capture, don't weaponize, the documented operation.** Where the known chain appends to `/home/vmanage-admin/.ssh/authorized_keys` (Phase 2), capturing the appended artifact *as evidence* is proof; using the key to establish further persistence or lateral movement is beyond the report.

**Evidence set to capture:** the authenticated-peer / connection-UP state; the NETCONF `<hello>` and advertised capabilities; one scoped read-only retrieval; the raw request/response for an API bypass; and timestamps. **Reporting:** describe the pivot path, state the demonstrated access level, name the possible-but-not-executed fabric impact, and recommend the operator collect `request admin-tech` and review `show sdwan control connections` **before** upgrading — because, as Phase 2 notes, patching over a compromise leaves injected keys and accounts in place.

### S6 — Hardening read as attack surface

The Cisco Catalyst SD-WAN Hardening Guide is, read adversarially, a list of things that are findings when they are *not* done. Each recommendation inverts into a test:

| Hardening recommendation | The finding when it is missing |
|---|---|
| UDP 12346 toward Controllers/Manager only from edge IPs | 12346 reachable from arbitrary internet sources |
| **NETCONF TCP 830 only from the Manager IP** to Controllers/Validators | 830 reachable from anywhere but the Manager = pivot exposure |
| SSH 22 and HTTPS 443 only from a jump host / mgmt subnet | 22/443 reachable broadly; "never expose controllers to the Internet" |
| "Do not expose administrative interfaces (443, 22, 830) to the internet" | any of them internet-facing |
| VPN 512 management kept strictly out-of-band | management plane routed through the DMZ or internet |
| Replace the Manager's default self-signed web cert | default self-signed cert still presented on 443 |
| SNMPv3 only | SNMPv1/v2 reachable |
| FIPS mode (default on from 20.15.1) | FIPS off on a 20.15.1+ component |
| Short session/idle timeouts | 24h session / 30-min idle defaults left wide |

The single highest-value inversion is the NETCONF rule: 830 is *designed* to be reachable only from the Manager, so a Controller or Validator answering NETCONF from your vantage point is both a misconfiguration finding on its own and the open door for the Phase 2 pivot.

### S7 — CTF and enterprise-lab notes

Cisco SD-WAN is almost never a public CTF box — the controllers are heavyweight VMs and the protocol is proprietary — but the *pattern* is a recurring CTF archetype: a custom binary/handshake protocol whose authentication has an attacker-controlled role field and a non-fatal certificate check. If you have internalized "complete the handshake, claim a privileged role the server forgot to default-deny, fall through to authenticated," you have the Phase 1 lesson, and it transfers to any proprietary C2 or clustering protocol in a CTF.

To build the reproduction lab: stand up the fabric yourself — Validator + Controller + Manager + at least one edge — using the Cisco dCloud SD-WAN sandbox or self-hosted controller images, onboard the edge through the authorized-serial-file flow (S2) so you understand the trust decision you are about to break, then deploy a **disposable Controller of the exact affected train** and run the published CVE-2026-20182/-20127 PoC tooling against it. Require the authenticated-peer state or the appended-`authorized_keys` artifact as success — a timeout is not a reproduction.

What differs CTF vs. bounty: in a CTF the foothold *is* the goal — the NETCONF/SSH access leads to a file read and the flag, and you pursue it. In a bounty against a live fabric, S5 is absolute: you stop at proof-of-access, you never push OMP, and the lab is the only place the full chain runs to completion.

### Supplement references

- Cisco Catalyst SD-WAN Hardening Guide (rev 1.1, 2026-02-09) — https://sec.cloudapps.cisco.com/security/center/resources/Cisco-Catalyst-SD-WAN-HardeningGuide
- Cisco SD-WAN vManage Software Authentication Bypass (CVE-2021-1284), cisco-sa-sdw-auth-bypass-65aYqcS2 — https://www.cisco.com/c/en/us/support/docs/csa/cisco-sa-sdw-auth-bypass-65aYqcS2.html
- NVD, CVE-2023-20254 (Manager multi-tenant session-management bypass) — https://nvd.nist.gov/vuln/detail/CVE-2023-20254
- NVD, CVE-2021-1473 (Cisco RV Series — the citation-correction source) — https://nvd.nist.gov/vuln/detail/CVE-2021-1473
- Cisco, Troubleshoot Common SD-WAN Control and Data Plane issues (doc 222302) — https://www.cisco.com/c/en/us/support/docs/routers/sd-wan/222302-troubleshoot-common-sd-wan-control-and-d.html
- Cisco Catalyst SD-WAN Controller Authentication Bypass (CVE-2026-20182), cisco-sa-sdwan-rpa2-v69WY2SW — https://sec.cloudapps.cisco.com/security/center/content/CiscoSecurityAdvisory/cisco-sa-sdwan-rpa2-v69WY2SW
- Rapid7, CVE-2026-20182 analysis (vdaemon DTLS / vHub role fall-through) — https://www.rapid7.com/blog/

*Supplement drafted 2026-11-19 (claude-opus-4-8); every CVE and port fact re-verified against the primary Cisco advisories, the Cisco Catalyst SD-WAN Hardening Guide rev 1.1, and Cisco troubleshooting doc 222302 at the URLs above. Correction of record: CVE-2021-1472/-1473 are Cisco RV-Series router flaws, not SD-WAN; the vManage-era auth bypass is CVE-2021-1284.*
