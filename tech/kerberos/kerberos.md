---
technology: "Kerberos"
kind: overview
audience: offensive-security
family: directory-services
layer: "authentication protocol"
language: "n/a (KDC service; tooling in Python, C#, PowerShell, MIT krb5)"
homepage: "https://learn.microsoft.com/en-us/windows-server/security/kerberos/kerberos-authentication-overview"
github-repo: "https://github.com/fortra/impacket (canonical client/forgery tooling)"
provenance: "field-grounded (re-gated command-context gate — 79 files / 60 engagements; families: ticket forgery & pass-the-ticket 56 files, kerberoast 22, delegation 20, AS-REP roast 15, kerbrute user-enum 11, SPN enum 10; 18 further files with kadmin/kinit/klist-only usage tracked as mit-krb5-appliance, not gating the AD count; structure-pass hint was 24 files) + upstream-verified: hashcat v8.0.0 example-hash table (13100 TGS-REP etype 23 / 18200 AS-REP etype 23, plus 19600/19700/32100/32200 AES variants), NVD CVE-2020-17049 (bronze bit), Microsoft Learn Kerberos constrained delegation overview. Companion methodology: [[kerberos-bug-bounty|Kerberos bug bounty methodology]]."
---

# Kerberos

> The domain's authentication protocol hands out encrypted blobs whose keys derive from account passwords — so any account whose ticket you can request, you can crack offline; and whoever holds the krbtgt key stops asking the KDC anything at all.

## What it is

Kerberos is AD's trust engine: a **KDC** (every DC) issues **TGTs** from the AS exchange (encrypted with the `krbtgt` key) and **service tickets** from the TGS exchange (encrypted with the service account's key — located by its **SPN**). The protocol's offensive gravity comes from three design consequences. First, **the KDC never checks who's asking**: anyone can request a TGT for any principal without pre-auth enabled, and any authenticated user can request a service ticket for any SPN — so the offline crack (AS-REP roast, Kerberoast) is a *protocol feature*, not a bug. Second, **tickets are bearer artifacts**: possession is authorization, which makes pass-the-ticket and local forgery (golden/silver) the dominant post-credential moves. Third, **delegation is opt-in machinery with a decade of abuse classes**: unconstrained (forwarded TGTs), constrained (S4U2Self/S4U2Proxy), and resource-based constrained (RBCD — the target itself names who may impersonate to it via `msDS-AllowedToActOnBehalfOfOtherIdentity`).

Etype is the cracking lever: legacy accounts negotiate **RC4 (etype 23)**, whose key is the bare NT hash — hashcat modes **13100** (TGS-REP, Kerberoast) and **18200** (AS-REP, AS-REP roast), verified against hashcat v8.0.0's example table. AES-only accounts (etype 17/18) still roast but need modes 19600/19700/32100/32200 and far more compute. 60 observed engagements route through this surface — it is the second busiest AD unit after the ACLs.

## The anatomy that matters on an engagement

    AS-REQ (principal, no preauth flag set) ─► AS-REP  ─► $krb5asrep$23  ─► 18200 offline crack
    AS-REQ (password/hash/cert)             ─► TGT (.ccache / kirbi)
    TGS-REQ (SPN)                           ─► TGS-REP ─► $krb5tgs$23   ─► 13100 offline crack
    service auth: present ticket (GSSAPI over HTTP/LDAP/SMB/RDP...)
    forgery:  krbtgt key → golden (any user, any service) · service key → silver (that service)
    delegation:
      unconstrained  → TGT lives in memory of the service host (dump it)
      constrained    → S4U2Self + S4U2Proxy to allowedToDelegateTo targets
      RBCD           → write msDS-AllowedToAct… (ad-acl unit) → S4U as anyone to that host
    tickets on disk: KRB5CCNAME=…ccache · kirbi ⇄ ccache (kirbi2ccache)

Three structural properties drive most findings:

- **Pre-auth-less and SPN'd accounts are free crack attempts.** `DONT_REQ_PREAUTH` on a user (query it in LDAP) makes GetNPUsers-style TGT requests offline-crackable; every SPN-bearing account is Kerberoastable by any authenticated user.
- **The KDC believes its own crypto, not reality.** Tickets forged with the krbtgt/service key validate everywhere (golden/silver); tickets whose delegation flag was tampered with used to validate too, until CVE-2020-17049 (bronze bit) taught the KDC to re-check.
- **Machine accounts are the new admins.** S4U/RBCD chains and PKINIT shadow credentials all key off machine or service principals the estate under-values (default 100+-char random passwords — but the *rights* attached matter, not the password).

## Where you'll meet it

- **Every domain with a user credential** — Kerberoast + AS-REP roast are the standing first moves after collection (recurring across the evidence pool).
- **NTLM-disabled or signing-enforced estates** — Kerberos-only paths (`getTGT` + `-k -no-pass` on every Impacket tool, observed repeatedly) are the workaround, not an obstacle.
- **Delegation-rich service estates** — unconstrained DCs, constrained web/service accounts with `AllowedToDelegateTo` entries, RBCD wherever you can write the attribute (all observed in the field).
- **MIT krb5 appliances** — Linux hosts running Kerberized services with local `kadmin` administration (observed) — a separate, non-AD surface where kadmin principals and keytabs are the targets.

## Reading the KDC's mind

1. **`GetNPUsers` returning a hash without credentials means the "do not require Kerberos preauthentication" flag is set** — the account is AS-REP roastable by design error; hash format `$krb5asrep$23$user@REALM` feeds hashcat 18200.
2. **Any user, any SPN: a service ticket is one TGS-REQ away** — `$krb5tgs$23*svc$REALM$spn*` feeds 13100; prefer `-request-user` to target one account (both observed).
3. **RC4 vs AES decides your evening** — etype 23 modes (13100/18200) are rockyou-speed; etype 17/18 (19600/19700/32100/32200) are compute-bound.
4. **`-impersonate` is the S4U tell** — a getST invocation with it means a constrained/RBCD chain is impersonating someone; the attribute write that enabled it was an ACL finding (ad-acl unit).
5. **Clock skew is the #1 false failure** — every ticket workflow starts with time sync against the DC (an observed ntpdate note); Kerberos rejects skewed timestamps before it evaluates anything else.

## Where to go next

[[kerberos-bug-bounty|Kerberos bug bounty methodology]] — user enumeration, both roasts with verified hashcat modes, ticket request/export/forgery, all three delegation classes, pass-the-ticket, bronze-bit context, and the MIT krb5 appliance surface.
