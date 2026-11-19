---
technology: "Active Directory Certificate Services (AD CS)"
kind: overview
audience: offensive-security
family: directory-services
layer: "PKI / enterprise certificate authority integrated with AD"
language: "C++/COM (certsrv); attack tooling Python (Certipy) / C# (Certify, PSPKI)"
homepage: "https://learn.microsoft.com/en-us/windows-server/networking/core-network-guide/cncg/server-certs/"
github-repo: "https://github.com/ly4k/Certipy"
provenance: "corpus-grounded (re-gated command-context gate — 29 files / 461 writeups: certipy usage 25, ESC1-ESC8 class strings in find-output 14, CA client surface (certreq/certsrv/web-enrollment) 11, relay-to-CA 3; the structure-pass hint of 9 was a ~3x undercount — its marker set missed prose-form certipy usage and the ESC-class family; bare 'ADCS'/'certificate template' mentions were NOT counted, only tool/class/command lines) + upstream-verified: SpecterOps 'Certified Pre-Owned' paper v1.0.1 (PDF fetched + ESC1-ESC8 sections read directly — every class below quotes its conditions), Certipy README (ESC1-ESC17 support, v5.x), Microsoft KB5005413 (ESC8 mitigation guidance: EPA Required / disable NTLM on IIS), MSRC ADV210003. Corpus exemplars span ESC1, ESC3, ESC4, ESC7, ESC8 relay, and certreq-native enrollment. Companion methodology: [[ad-cs-bug-bounty|AD CS bug bounty methodology]]."
---

# Active Directory Certificate Services

> The PKI that was built to make auth *stronger* became AD's most reliable privilege-escalation engine: misconfigured templates let a domain user request a certificate as the domain admin, and the CA's HTTP enrollment endpoint turns any coerced NTLM authentication into a persistent identity.

## What it is

AD CS is Microsoft's enterprise PKI — a CA role that issues X.509 certificates whose **certificate mapping** makes them AD logon credentials (Kerberos PKINIT or Schannel). The offensive model, established by the SpecterOps "Certified Pre-Owned" paper (2021), is simple: **a certificate that authenticates as a principal IS that principal**, often past password resets and for the life of the cert. The attack surface splits into **template misconfigurations** (ESC1-ESC4: who can enroll, which EKUs, who can edit the template object), **CA/PKI-object control** (ESC5-ESC7), and **relay** (ESC8: NTLM relay into the HTTP enrollment interface). Post-paper research extended the catalogue — Certipy's README today advertises ESC1-ESC17 — but the field's wins are overwhelmingly ESC1/ESC3/ESC4/ESC7/ESC8.

Everything enumerated here is **LDAP-visible**: templates are AD objects under `CN=Certificate Templates,CN=Public Key Services,CN=Services,CN=Configuration,<NC>` with their EKUs in `pKIExtendedKeyUsage` and their ACLs on the object (see [[ldap-ad]]); the CA itself is an object in the Enrollment Services container. That is why `certipy find` (an LDAP query storm) is the universal opening move — nearly every engagement runs it.

## The anatomy that matters on an engagement

    enumerate:  certipy find -vulnerable          ← LDAP reads (templates, CA ACLs, enrollment rights)
    misconfig tree (paper-verified):
      ESC1  template: low-priv enroll + no approval/signatures + auth EKU
            (Client Auth 1.3.6.1.5.5.7.3.2 · PKINIT 1.3.6.1.5.2.3.4 · SmartCard Logon
             1.3.6.1.4.1.311.20.2.2 · Any Purpose 2.5.29.37.0 · no EKU)
            + CT_FLAG_ENROLLEE_SUPPLIES_SUBJECT → you set the SAN → cert AS anyone
      ESC2  same, but Any Purpose / no EKU → cert usable for anything (SubCA can even mint more)
      ESC3  Certificate Request Agent EKU (1.3.6.1.4.1.311.20.2.1) → enroll on BEHALF of another user
      ESC4  template-object ACL lets you EDIT a safe template into ESC1 (set the SAN flag)
      ESC5  ACL on PKI container objects (NTAuthCertificates, Enrollment Services, CA computer obj)
      ESC6  CA flag EDITF_ATTRIBUTESUBJECTALTNAME2 → SAN honored in ANY template (-attrib "SAN:u:")
      ESC7  CA rights: ManageCA (flip ESC6 flag / config) · ManageCertificates (approve pending)
      ESC8  NTLM relay → http://ca/certsrv (web enrollment) → cert as the coerced victim
    use:        certipy auth -pfx → PKINIT → TGT (+ victim's NT hash as a bonus)

Three structural properties drive most findings:

- **Enrollment is delegation, and defaults are generous.** Any principal with the Enroll ACE on a template that also allows SAN-in-CSR can impersonate; the paper's ESC1 is not exotic — observed engagements hit it with default-looking templates (three chains).
- **The CA's own controls are a second ACL problem.** ManageCA flips the ESC6 flag remotely via the DCOM `ICertAdminD2::SetConfigEntry` call (paper-verified), turning "CA administrator" into domain compromise (ESC7; two observed chains).
- **Certificates outlive the credential.** Unlike a password, a forged-identity cert authenticates until expiry — resets don't revoke what the CA never knew was wrong. ESC8's whole value is converting a one-shot relay into this durable artifact.

## Where you'll meet it

- **Any domain where 445/88 recon shows a CA** — `certipy find` is cheap and near-deterministic (29 observed engagements).
- **NTLM-heavy estates with web enrollment** — PetitPotam/PrinterBug coercion → ntlmrelayx → `certsrv` (ESC8; five observed chains — see [[ntlm]]).
- **ACL-attack chains** — ESC4/ESC5 are the PKI branches of the [[ad-acl]] unit; template-object GenericAll is an ESC1 one write later (three observed chains).
- **Shadow-credentials chains** — `msDS-KeyCredentialLink` writes issue certs via the same CA machinery (field-observed; see [[kerberos]]).
- **Trust-boundary pivots** — NTAuthCertificates control (ESC5) lets foreign/rogue CAs become domain-trusted logon issuers.

## Reading the CA's mind

1. **`certipy find -vulnerable` output lines ARE the ESC classes** — `ESC1 : 'EXAMPLE\Domain Users' can enroll…` tells you the class, the right, and the target in one line (four observed chains).
2. **`ESC8 : Web Enrollment is enabled over HTTP`** means the CA answers `http://198.51.100.20/certsrv` with NTLM-only auth — a relay target that converts coercion to certificates (paper-verified default; Microsoft's KB5005413 exists precisely to say "enable EPA or disable NTLM").
3. **`CT_FLAG_ENROLLEE_SUPPLIES_SUBJECT` in a template** means the CSR's SAN field is trusted — the ESC1 heart; the paper is explicit that SAN-in-CSR + authentication EKU + enroll rights = impersonation.
4. **`pKIExtendedKeyUsage = 2.5.29.37.0` (Any Purpose) or empty** on an enrollable template = ESC2-grade trust — usable for client auth, server auth, even issuing further certs (SubCA).
5. **A pending-request queue with your request in it** means manager approval is ON (anti-ESC1) — pivot to ESC7's ManageCertificates right to approve yourself.

## Where to go next

[[ad-cs-bug-bounty|AD CS bug bounty methodology]] — certipy workflows per class, template enumeration filters for raw LDAP, the ESC8 relay chain end-to-end, misconfig→ESC mapping tables, and post-paper classes (ESC9+) context.
