---
name: "certi"
categories:
  - blackarch-windows
version: "6.6cfa656"
homepage: "https://github.com/zer1t0/certi"
packaged-by: [blackarch, nixpkgs]
blackarch-builddate: "2026-01-12"
build-system: windows
license: AGPL-3.0-only
meta-checked: "2026-11-09"
github-repo: "zer1t0/certi"
github-stars: 328
github-pushed: "2023-02-06"
github-archived: false
github-issues: 4
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.4
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# certi

> Enumerate and abuse Active Directory Certificate Services from Linux — the impacket-based Python counterpart to Certify.

*No corpus evidence — derived from upstream documentation.*

## What it's for

Attacks the AD CS misconfigurations catalogued in "Certified Pre-Owned" (ESC1–ESC3) against an enterprise certificate authority, using impacket for the Windows protocols. The `list` command enumerates enrollment services, CA certificates and certificate templates, marking which enabled templates are vulnerable (ESC1 SAN impersonation, ESC2 any-purpose, ESC3 enrollment-agent abuse). The `req` command requests certificates over Kerberos or NTLM — plain user requests, SAN-impersonation requests where a template allows it, and on-behalf-of issuance through an enrollment-agent certificate. Each issued certificate is saved as a `.pfx` usable for client authentication or PKINIT as the named account. It abuses enrollable templates only; it cannot forge certificates without an ADCS misconfiguration to ride on.

## Typical use cases

### Find vulnerable, enabled certificate templates

```
certi.py list 'example.com/user@dc01.example.com' -k -n --dc-ip 198.51.100.10 --vuln --enable
```

Outcome: enumerates enabled templates and annotates each with its detected misconfigurations (`ESC1 - SAN Impersonation`, `ESC2 - Any Purpose`, `ESC3.2 - Use Agent Certificate`); pipe through `grep ESC1 -B 3` to isolate impersonation candidates. Requires a Kerberos ticket in the environment (`KRB5CCNAME`) for the `-k -n` form.

### Request a certificate for your own account

```
certi.py req 'example.com/user@dc01.example.com' 'example-DC01-CA' -k -n
```

Outcome: requests a certificate from the named CA using the default User template; prints the issued subject, issuer, serial and extended key usages, and saves it as `user.pfx` with the printed password. When NTLM is refused (`CERTSRV_E_ENROLL_DENIED`), the Kerberos form `-k -n` is the one that succeeds.

### Request a certificate impersonating another user (ESC1)

```
certi.py req 'example.com/user@dc01.example.com' 'example-DC01-CA' -k -n --alt-name administrator --template UserSAN
```

Outcome: if a SAN-capable template lets the requester set the subject alternate name, issues a certificate whose alt name is `administrator` (shown as `Cert Altname: administrator@example.com`) and saves it as `administrator@example.com.pfx` — a credential that authenticates as the impersonated account.

### Issue a certificate on behalf of another user (ESC3)

```
certi.py req 'example.com/user@dc01.example.com' 'example-DC01-CA' -k -n --template EnrollAgent
certi.py req 'example.com/user@dc01.example.com' 'example-DC01-CA' -k -n --on-behalf administrator --enroll-cert user.pfx --enroll-cert-pw Password1 --template EnrollWithAgent
```

Outcome: the first request obtains a Certificate Request Agent certificate; the second presents it as the enrollment agent (`On behalf of: administrator`) and obtains a certificate issued for the target account, saved under the target's name. Candidates for both templates come from `list --vuln --enable | grep ESC3`.
