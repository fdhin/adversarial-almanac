---
technology: "Microsoft Exchange"
kind: bug-bounty-methodology
audience: ai-agent
family: network-services
role: implementation-variant
layer: mail server + web estate (25, 443/owa /ews /autodiscover, RPC over HTTP)
status: thin
homepage: "https://learn.microsoft.com/exchange/"
provenance: "evidence-grounded (4 files under command-context gate — OWA endpoint recon + atomizer password spray + captured hash; unauth base64-.NET deserialization on /ews/MsExgHealthCheckd/; Exchange-install detection via LDAP, Exchange security groups → WriteDacl-style ACL abuse; msexchange-logcopier service banner 10001-10002/tcp on Exchange 2010) + upstream-verified (NVD CVE-2021-26855 ProxyLogon SSRF — pre-auth, Exchange 2013 CU21+ versions affected, DEVCORE disclosure chain; proxylogon.com for chain context). Thin per evidence gate (4 files); the loose probe's Exchange=18 was 'exchange'/EWS substring collisions (key-exchange prose, Open Web Analytics owa.js). Companion router: [[smtp|SMTP]]. Enrichment pass 1, 2026-11-12."
---

# Microsoft Exchange — implementation methodology

Exchange is AD's mail organ: SMTP on 25, but the attack estate is HTTPS — `/owa`
(Outlook Web Access forms login), `/ews` (Exchange Web Services SOAP API), `/autodiscover`
(config + credential oracle), plus RPC-over-HTTP and service ports (one observed chain showed
Exchange 2010's `msexchange-logcopier` on 10001-10002/tcp naming the product and build in
nmap output). Mailbox access usually equals domain credential reuse; server-side bugs
equal domain compromise.

## Triage table

| Surface | Gate | Pre-auth? | Payoff |
|---|---|---|---|
| OWA password spray | /owa reachable, forms auth | credentialed | Mailbox → creds/2FA tokens/mail intel — High |
| Autodiscover abuse | /autodiscover answers | varies | Internal hostnames, mail config, legacy auth — Medium |
| EWS access | /ews with creds | credentialed | Mailbox via API (stealthier than OWA) — High |
| ProxyLogon-era chains (CVE-2021-26855 etc.) | unpatched 2013-2019, pre-2021-03 | **yes** | SSRF→RCE, webshell, full server — Critical |
| HealthCheck/deserialization N-days | specific build, health-check endpoint exposed | yes/limited | RCE as Exchange service — Critical |
| Exchange group membership (AD) | Exchange groups visible in LDAP | domain read | ACL/write privesc paths — High |

## Phase 0 — Fingerprint the estate

```bash
nmap -sV -p 25,80,443,587 198.51.100.10     # "Microsoft ESMTP MAIL Service" + IIS backend
curl -sk https://198.51.100.10/owa/ | head  # login page = forms auth target
curl -sk https://198.51.100.10/autodiscover/autodiscover.xml   # version XML or auth prompt
```

Exchange names itself generously: the SMTP banner carries `Microsoft ESMTP MAIL Service
ready ... XEXCH50`, /owa's frontend leaks server version in headers/OWA banners on old
builds, and /autodiscover returns mailbox/server topology to authenticated users. One observed
route: a service sweep found `10001/tcp msexchange-logcopier Microsoft Exchange
2010` — product + version from a side port, no HTTP needed.

## Phase 1 — OWA recon and password spraying (observed pattern)

1. Recon the OWA endpoint first — the observed chain ran atomizer's OWA recon mode to learn
   tenant/account formats before spraying.
2. Build the candidate list from mail addresses gathered elsewhere (observed chains
   harvested addresses from documents/PDF metadata).
3. Spray season/year and policy-shaped passwords (observed: `Summer2020` —
   `<Season><Year>` pattern) — one attempt per window to dodge lockout.
4. On success: OWA login → mailbox → further creds (one observed chain pivoted mailbox access
   to a captured password hash via an application that trusted the mailbox holder).

Rules of engagement: spray slowly, respect lockout policy (Exchange's default thresholds
are visible via failed-logon behavior), and stop at first hit.

## Phase 2 — EWS and Autodiscover

- `/ews/Exchange.asmx` is the SOAP API — with credentials, enumerate folders and pull
  mail programmatically (mail clients' account-creation flow shows the calls).
- `/autodiscover/autodiscover.xml` leaks internal FQDNs, CAS/server roles, and legacy
  auth posture; unauthenticated variants on old builds enumerate valid SMTP addresses by
  response differential.
- Observed: watch for **unauthenticated deserialization on EWS-adjacent paths** —
  a base64-encoded .NET payload posted to `/ews/MsExgHealthCheckd/` executed server-side
  on the vulnerable build (health-check endpoints are documented Exchange surface;
  GET-probe the path, confirm the `Microsoft-HTTPAPI/2.0` Server-header signature, and
  only then consider the deserialization PoC — destructive actions stay in lab scope).

## Phase 3 — ProxyLogon-era context (verified)

CVE-2021-26855 (NVD, "ProxyLogon"): **pre-auth SSRF** in Exchange 2013/2016/2019 — the
frontend accepts a crafted `X-AnonResource-Backend` cookie/header pair to authenticate as
an internal user and reach the backend's /autodiscover proxy endpoints. The DEVCORE
chain (proxylogon.com) couples it with post-auth file-write (CVE-2021-27065 family) for
unauthenticated RCE via webshell on 443 — the 2021 mass-exploitation event (HAFNIUM).
Any 2021-03-patch-lagged build is presumptively compromised; detection = HttpProxy log
SSRF patterns. Later siblings (ProxyShell, ProxyOracle, ProxyToken — 2021 CVE-2021-34473
family) extend the same proxy-architecture weakness to other endpoints. Verify each
specific CVE ID and patch level against NVD/MSRC before claiming; the family is broad
and version-mapping errors are common.

## Phase 4 — Exchange as an AD privilege lever (observed chain)

Exchange installation reshapes AD: its security groups (`Exchange Windows Permissions`,
`Organization Management`, ...) hold unexpected write/ACL rights over the domain. One observed
chain: anonymous LDAP enumeration revealed Exchange groups; membership/abuse of
`Exchange Windows Permissions` enabled WriteDacl-style escalation to DA. So in an
Exchange forest, enumerate Exchange groups with the LDAP toolset and check their members
for your foothold account — the mail server's groups are a domain-escalation graph
(cross-ref [[ldap]], [[ad-acl]]).

## Command quick reference

```bash
curl -sk https://HOST/owa/ ; curl -sk https://HOST/autodiscover/autodiscover.xml
nmap -sV -p 25,443,587,10000-10050 HOST   # banner + msexchange side services
swaks --to victim@example.com --server HOST   # mail-path probe through the Exchange frontend
```

## Tool mapping (prose)

nmap for banner/service sweeps; curl for OWA/Autodiscover probing; atomizer for OWA
recon+spray; swaks for SMTP-path testing; Burp Suite for EWS/health-check endpoint work
(observed); the LDAP toolset (ldapsearch/SharpHound-style collection) for Exchange
group enumeration. Tools vault owns per-tool pages.
