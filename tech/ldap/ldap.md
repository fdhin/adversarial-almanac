---
technology: "LDAP (Lightweight Directory Access Protocol)"
kind: overview
audience: offensive-security
family: directory-services
layer: "directory access protocol"
language: "n/a (protocol; clients everywhere — ldapsearch/OpenLDAP tools, Python ldap3, .NET System.DirectoryServices, PowerShell AD module)"
homepage: "https://www.rfc-editor.org/rfc/rfc4511"
github-repo: "https://github.com/ropnop/windapsearch (representative attack client)"
provenance: "corpus-grounded (re-gated command-context gate — 48 files / 461 machines: ldapsearch 18, bloodyAD 20, AD PowerShell/PowerView LDAP cmdlets 10, windapsearch 6, ad-ldap-enum 1, ldapdomaindump 1; the structure-pass hint of 18 gated on ldapsearch-family tools only, missing the AD-module/bloodyAD families; 69 further files carry nmap '389/tcp open ldap' recon lines tracked as context, not gating) + impl-split probe: AD-LDAP target-side 48 files vs OpenLDAP target-side 2 files (one standalone server: anonymous ldapsearch + cleartext OpenLDAP prose; one containerized deployment: osixia/openldap with cn=admin bind) — earlier probe's 'OpenLDAP 16' was polluted by the '$OpenLDAP:' banner printed in ldapsearch version/output lines; slapcat/slapd/olc/rootdn//etc/ldap have ZERO corpus matches → OpenLDAP page gate FAILS, covered as a section below + upstream-verified: OWASP LDAP Injection Prevention Cheat Sheet (RFC 4514 DN / RFC 4515 filter metacharacters, unauthenticated-bind trap), Microsoft ADV190023/KB4520412 (LDAPServerIntegrity, LdapEnforceChannelBinding, port security matrix), windapsearch + ad-ldap-enum READMEs. This page is the ROUTER: bind/protocol-common surface + dispatch to [[ldap-ad|AD LDAP]]. Companion: [[kerberos]] (SASL binds), [[ad-acl]] (what the writes mean), [[ntlm]] (simple-bind relay). Ports.md additions 2026-11-13: six2dez/pentest-book (enumeration/ports.md, no license — rewritten with attribution), accessed 2026-11-13."
source: "six2dez/pentest-book (enumeration/ports.md, no license — rewritten with attribution), accessed 2026-11-13"
ht-source:
  - "rewritten from HackTricks (src/network-services-pentesting/pentesting-ldap.md), CC BY-NC 4.0, accessed 2026-11-13"
---

# LDAP

> The directory the whole domain answers to: one readable-by-any-authenticated-user database whose bind ladder (anonymous → creds → SASL) and filter language decide most of what you'll ever learn about a forest — and whose write path is where ACL attacks actually land.

## What it is

LDAP is the query-and-modify protocol for directory services — AD's database behind every DC on **389/tcp** (LDAP) and **636/tcp** (LDAPS), with the **global catalog** on **3268/tcp** / **3269/tcp**. The deployed reality is two species: **Microsoft AD's LDAP** (the NTDS database — the field's overwhelmingly dominant target) and **OpenLDAP-family servers** (slapd and its container images, a thin presence in the field). They share the wire format and the bind ladder but not the misconfiguration classes: AD leaks through **who can read/write which attribute** (SPNs, UAC flags, ACLs, group membership), OpenLDAP through **cleartext 389 traffic and anonymous/simple binds**.

The protocol's offensive gravity comes from the bind. A **simple bind** on 389 without TLS sends the password in the clear (ADV190023 classes those sessions as having "no security"); an **anonymous bind** often still answers the **rootDSE** — including `namingContexts`/`defaultNamingContext`, which hands you the base DN for everything else. Because AD grants *authenticated read* to all domain users by default, any one credential turns the directory into a full map of the estate. The write side is equally central: nearly every AD ACL-abuse primitive (RBCD attribute writes, group adds, SPN sets — the [[ad-acl]] unit) is executed as an LDAP modify, which is why operators live in this protocol.

**Router logic:** LDAP in your sights → anonymous bind + rootDSE first → if the naming context is AD-style (`DC=example,DC=com`, with `servicePrincipalName`, `userAccountControl`, `msDS-*` answering) go to **[[ldap-ad|AD LDAP surface]]**; if it is a standalone OpenLDAP-style server, the thin section at the bottom of this page plus the protocol-common workflow below cover the field's entire observed surface.

## The anatomy that matters on an engagement

    connect 389/tcp (LDAP) · 636 (LDAPS) · 3268/3269 (GC)
      → bind:  anonymous ──► rootDSE answers namingContexts (base DN free)
               simple    ──► user+password (cleartext on 389 unless STARTTLS/TLS)
               unauthenticated-trap: name set + EMPTY password = anonymous success (RFC 4513 §5.1.2)
               SASL      ──► GSSAPI = Kerberos ([[kerberos]]) — signing/sealing for free
      → search: base + scope + FILTER (RFC 4515 prefix notation)
                 (objectClass=user) · bitwise rules 1.2.840.113556.1.4.803 / .804
      → modify: group adds, attribute writes — the delivery vehicle of [[ad-acl]] attacks
    dispatch:
      naming context AD-style, AD attributes answer → [[ldap-ad]]
      standalone slapd / osixia-style container    → OpenLDAP section (this page)

Three structural properties drive most findings:

- **The bind ladder is the front door, and estates skip rungs.** Anonymous rootDSE and anonymous full search (both field-observed, including explicit anonymous-bind checks) still exist; every higher rung only adds attributes.
- **Read is granted by authentication, not by role.** A single valid domain user enumerates users/groups/SPNs/delegation flags for the entire domain — recon is not a privileged act in AD.
- **Filters are a language, so filter injection is code injection.** Apps that splice user input into search filters (login forms doing `(sAMAccountName=<input>)`) leak and bypass per OWASP/RFC 4515 metacharacter rules — the only LDAP bug class that lives outside the directory itself.

## Where you'll meet it

- **Every AD engagement, first hour** — LDAP is the enumeration backbone .
- **BloodHound/SharpHound collection** — the ingest graph is pulled over LDAP; "run bloodhound" is an LDAP operation (see [[ldap-ad]]).
- **Auth-adjacent web apps** — login forms binding to LDAP: cleartext simple binds on 389, and filter injection in the search behind the bind.
- **Appliance/identity backends** — OpenLDAP-family containers behind apps (observed: an osixia/openldap container; a standalone OpenLDAP server) — thin but real: cleartext sniffing and anonymous search were both observed paths.

## Reading the server's mind

1. **Anonymous bind + rootDSE `namingContexts`** is the free first query — it returns the base DN (observed: windapsearch prints "Getting defaultNamingContext from Root DSE"; an observed `ldapsearch -x -b "dc=…"` anonymous query rides it).
2. **Simple bind on 389 = cleartext password** — per Microsoft's ADV190023 matrix, non-TLS simple-bind sessions have *no* security; a privileged position on the wire harvests them (observed: one standalone-server estate's whole pivot).
3. **Empty-password binds "succeed" without authenticating** — RFC 4513 §5.1.2 unauthenticated bind: a name plus a zero-length password yields anonymous authorization; test tools and login flows misread it as valid creds.
4. **DC signing posture is checkable before you bind** — `LDAPServerIntegrity` (None=1 / Require Signing=2) and `LdapEnforceChannelBinding` (0/1/2) under `HKLM\SYSTEM\CurrentControlSet\Services\NTDS\Parameters` decide whether unsigned binds get rejected — relevant when an [[ntlm]] relay or cleartext-capture path looks viable.
5. **`1.2.840.113556.1.4.803` in a filter means a UAC/flag bit query** — e.g. `(userAccountControl:1.2.840.113556.1.4.803:=4194304)` finds no-preauth accounts (AS-REP roast candidates — [[kerberos]]).

## Protocol-common workflow

Tools named in prose; 198.51.100.10 = DC, example.com = domain.

```bash
# 1. anonymous triage: who am I to this directory?
ldapsearch -x -H ldap://198.51.100.10 -s base -b '' '(objectclass=*)' \
  namingContexts rootNamingContext supportedSASLMechanisms
#    → namingContexts: DC=example,DC=com   (your base DN; free recon)

# 2. credentialed dump-scale enumeration (full AD context → [[ldap-ad]])
windapsearch -d example.com --dc-ip 198.51.100.10 -u user@EXAMPLE.COM -p 'Password123!' -U   # users
ad-ldap-enum -l 198.51.100.10 -d example.com -u user -p 'Password123!'                       # group-member CSVs
ldapdomaindump -u example.com\\user -p 'Password123!' 198.51.100.10                          # HTML/JSON dump

# 3. raw filter work (the pattern everything reduces to)
ldapsearch -x -H ldap://198.51.100.10 -D 'user@example.com' -w 'Password123!' \
  -b 'DC=example,DC=com' '(&(objectClass=user)(servicePrincipalName=*))' sAMAccountName servicePrincipalName

# 4. filter-injection probe (web app splicing input into filters)
#    payload chars: * ( ) \ NUL in filters;  \ # + < > , ; " = and leading/trailing space in DNs
#    e.g. input  *)(objectClass=*)  into "(sAMAccountName=INPUT)" → boolean filter reshape
```

Expected outputs: step 1 either returns the rootDSE (anonymous allowed) or an anonymous-bind error; step 3's attribute list is the recon skeleton; step 4 returning *more rows on malformed input* than on a clean username is the injection signature (per OWASP, defense = DN/filter-context escaping per RFC 4514/4515 — apps that "just concatenate" are the targets).

## The thin OpenLDAP surface (gate failed — documented, no separate page)

A dedicated `ldap-openldap.md` was **not** written: the impl-split gate (target-side slapcat/slapd/olc\*/rootdn//etc/ldap markers) matched **zero** observed engagements; the earlier "OpenLDAP 16" hint was banner pollution (`$OpenLDAP:` strings in ldapsearch output). The 2-file residual surface, for completeness:

- **Anonymous search + cleartext binds** (observed once) — `ldapsearch -x` walks the tree, and unencrypted 389 lets a privileged position on the wire harvest simple-bind credentials.
- **Containerized OpenLDAP as an app's identity backend** (observed once) — `osixia/openldap` images carrying an `LDAP_ADMIN_PASSWORD` env var and a `cn=admin,dc=…` manager DN; app config files leak the admin bind pair, after which the whole tree is one ldapsearch away (`-D 'cn=admin,dc=example,dc=com'`).

If future engagements produce ≥3 slapd-config-path targets, split a page then.

## Linux client-side artifacts (any foothold on an LDAP-integrated host)

*Added 2026-11-13 — rewritten from HackTricks (see frontmatter); the server is
not the only place the directory pays out.* On Linux hosts integrated with
LDAP/AD, the reusable secrets sit in the **client configuration**:

```bash
ls -l /etc/sssd/sssd.conf /etc/nslcd.conf /etc/ldap/ldap.conf /etc/krb5.conf
grep -nE '^(ldap_uri|ldap_search_base|ldap_default_bind_dn|ldap_default_authtok|id_provider|auth_provider)\s*=' \
  /etc/sssd/sssd.conf /etc/nslcd.conf
```

What each key hands you: `ldap_uri` + `ldap_search_base` (where and what to
query), `ldap_default_bind_dn` + `ldap_default_authtok` (a reusable machine
bind credential — often a service account), `id_provider`/`auth_provider`
(whether the host consumes LDAP, Kerberos, or both). The findings that matter
are world-readable `sssd.conf`/`nslcd.conf`, cleartext bind tokens inside
them, and any directory-backed SSH or sudo integration that turns a readable
config into real authorization. Follow up with the recovered pair:

```bash
ldapsearch -x -H ldap://198.51.100.10 -D '<bind-dn>' -w '<password>' -b '<base-dn>'
```

## The write path beyond AD: when a writable attribute becomes login

Directory write access on an OpenLDAP-family backend converts to host access
only through a consuming integration — verify the consumer before claiming
the impact:

- **`sshPublicKey`**: if hosts fetch authorized keys from LDAP (SSSD
  `ssh_authorizedkeys` or an `AuthorizedKeysCommand` script), replacing a
  target's attribute is authentication as that user without their password.
  The attribute merely existing proves nothing — confirm the mapping is live.
- **`gidNumber` / `userPassword`**: only matter where the target's NSS/SSSD
  stack consumes them. A `gidNumber` write is a sudo chain only if the mapped
  local group holds a sudoers rule; cached identities can also delay the
  change taking effect.

A writable entry alone is not root — the report needs the consumer (key
source, mapped group, sudoers line) chained to the write.

## StartTLS suppression and the TLS posture

An on-path position can do more than read simple binds on 389: where a client
treats TLS as optional, suppressing or interfering with the StartTLS
extension makes many stacks fall back to an unauthenticated simple bind.
Fail-closed (require TLS before sending credentials) is the only safe client
policy — its absence is a finding on its own. For `ldaps://` or completed
StartTLS, interception additionally needs the client to accept an untrusted
or name-mismatched certificate; LDAP clients are usually unattended services,
so the certificate-validation *policy* is the control to audit, not the
interactive prompt nobody will see.

## Post-exploitation: carving the legacy OpenLDAP database

Older OpenLDAP deployments keep Berkeley DB files under `/var/lib/ldap`; with
a readable `.bdb` backend, string-carving recovers password-hash records
(select the hash value — e.g. the string starting `{SSHA}` — and match it to
the right John the Ripper / hashcat format; do not paste adjacent database
fields like `structuralObjectClass` into the crack file). Modern `mdb`
backends and binary formats need backend-aware offline tooling or a clean
backup instead.

## Enumeration additions (2026-11-13)

- **NetExec null-bind queries** — where anonymous bind is permitted, the
  `--query` form pulls objects without credentials:
  `nxc ldap 198.51.100.10 -u '' -p '' --query "(sAMAccountName=*)" ""`
  (an Operations error "a bind is required" is the denied answer); harvest
  `sAMAccountName`/`userPrincipalName`, `memberOf`, `pwdLastSet`,
  `userAccountControl` straight from the response.
- **Godap** (Macmod) — interactive TUI LDAP client: simple binds,
  pass-the-hash/ticket/cert, object CRUD, group edits, DACL changes, ADIDNS
  records, JSON export — the GUI-era replacement for repeated ldapsearch
  round trips.
- **ldapx** (Macmod) — an LDAP proxy that inspects and *transforms* traffic in
  flight; operators use it to reshape LDAP queries so identity-protection and
  LDAP-monitoring tooling fail to parse them (the MaLDAPtive technique set).

## Ports cheat-sheet additions (2026-11-13)

From six2dez/pentest-book enumeration/ports.md (no license — rewritten with attribution); the source's ldapsearch and windapsearch forms were already the workflow's steps 1–3 — skipped. Two client mentions are new:

- **jxplorer** — the long-standing Java LDAP browser: point it at the base DN from step 1 and walk the tree interactively, editing attributes by hand — the GUI shape of step 3 when a round-trip-per-question CLI loop is slowing the exploration down.
- **go-windapsearch** — the Go rebuild of windapsearch (single static binary, same user/group/computer query set): the drop-in when the foothold box has no Python tooling.

## Where to go next

- [[ldap-ad|AD LDAP surface]] — the observed AD reality: bind types against a DC, naming-context recon, SPN scanning via LDAP, ACL/GUID reads, BloodHound ingest context, ad-ldap-enum patterns, bloodyAD writes.
- [[ldap-injection|LDAP filter injection]] — the app-side technique family: filter grammar, login-bypass payload set, search-then-bind discipline, blind boolean/timing oracles, attribute extraction loops.
- [[kerberos]] — SASL/GSSAPI binds, and the roast targets (SPN, DONT_REQ_PREAUTH) you find *through* LDAP.
- [[ad-acl]] — what the modify operations you launch here actually require.
- [[ntlm]] — simple-bind/LDAP as a relay and capture target.
