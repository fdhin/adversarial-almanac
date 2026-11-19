---
name: "rubeus"
categories:
  - windows
homepage: "https://github.com/GhostPack/Rubeus"
origin: "pentest-book"
recipe: "upstream"
build-system: windows
meta-checked: "2026-11-13"
github-repo: "GhostPack/Rubeus"
github-stars: 5223
github-match: "exact"
gh-checked: "2026-11-13"
worth: 5.8
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-13"
license: "BSD-3-Clause"
---

# rubeus

> C# toolkit for raw Kerberos abuse on Windows AD: request, forge, harvest and import tickets from any secret you hold.

## What it's for

Rubeus (GhostPack, by harmj0y and contributors) is the canonical
Windows-side Kerberos attack toolkit — a single C#/.NET binary that
turns Active Directory Kerberos abuse primitives into verbs: request
TGTs/TGSs from passwords, hashes, keys or certificates (`asktgt`,
`asktgs`), roast service-account and passwordless credentials
(`kerberoast`, `asreproast`), abuse constrained delegation (`s4u`),
forge golden/silver/diamond tickets, harvest and extract live tickets
(`monitor`, `harvest`, `triage`, `dump`, `tgtdeleg`) and manage logon
sessions (`ptt`, `purge`, `klist`, `describe`). Reach for it on any
Windows host in an AD engagement where you hold a credential and need
a usable ticket or crackable material — `/ptt` and `/createnetonly`
apply tickets directly into logon sessions. It does not do DCSync or
LSASS memory dumping (that is mimikatz territory) and it is
Windows/.NET only; the project is in maintenance mode but remains the
reference implementation. No official binaries are released — compile
it yourself (see [[sharpcollection]] for nightly community builds).

## Typical use cases

### Derive Kerberos keys from a password

Turn a plaintext credential into the key formats other commands take.

```
Rubeus.exe hash /password:Password1 /user:websvc$ /domain:target.example.com
```

Outcome: prints the RC4/NTLM, AES128 and AES256 Kerberos keys for the
principal — feed them into `asktgt`, `s4u` or ticket forgery via
`/rc4:` or `/aes256:`.

### Request a TGT from a hash (over-pass-the-hash)

Convert a stolen NTLM hash into a usable ticket.

```
Rubeus.exe asktgt /user:websvc$ /rc4:NTLM_HASH /domain:target.example.com /ptt /nowrap
```

Outcome: requests a TGT via PKINIT-less encrypted-timestamp pre-auth,
imports it into the current logon session (`/ptt`) and prints it
base64-encoded on one line (`/nowrap`) for reuse elsewhere.

### Request a TGT from a certificate

Use a cert with a UPN SAN to get tickets and the account's key.

```
Rubeus.exe asktgt /user:user /certificate:cert.pfx /password:CERT_PASS /getcredentials /nowrap
```

Outcome: authenticates via PKINIT with the PFX, requests the TGT, and
(`/getcredentials`) also prints the account's NTLM hash derived from
the PKINIT reply — the standard step after cert theft. Works with
raw DER hex strings in place of the file path.

### Kerberoast the domain

Harvest crackable service tickets for SPN-backed accounts.

```
Rubeus.exe kerberoast /creduser:TARGET.EXAMPLE.COM\user /credpassword:Password1 /nowrap
```

Outcome: enumerates users with SPNs via LDAP, requests their service
tickets and prints them as hashcat-ready hashes (`/rc4opsec`,
`/aes` and LDAP filters like `/ldapfilter:` tune which accounts are
roasted); crack offline with hashcat mode 13100/18200.

### Abuse constrained delegation

Impersonate a user onto a service a machine account is trusted for.

```
Rubeus.exe s4u /user:websvc$ /rc4:NTLM_HASH /impersonateuser:administrator /msdsspn:cifs/fileserver.target.example.com /ptt
```

Outcome: performs the S4U2self/S4U2proxy pair and imports the
resulting service ticket for the impersonated user, granting access to
the delegated SPN (`/altservice:` swaps the service class;
`/self` covers protocol transition on the same host).

### Get a usable TGT without elevation

Extract the current user's TGT session key from a non-elevated shell.

```
Rubeus.exe tgtdeleg /nowrap
```

Outcome: uses the s4u2self trick against the KDC to obtain a
forwardable TGT for the current context with its session key, printed
as base64 — usable for kerberoasting (`kerberoast /tgtdeleg`) or from
other machines without admin rights.

### Watch for new tickets

Park on a host and capture TGTs/TGSs as users authenticate.

```
Rubeus.exe monitor /interval:60 /targetuser:administrator /nowrap
```

Outcome: polls every 60 seconds for new tickets in logon sessions and
prints any that appear for the target user (all users when
`/targetuser:` is omitted) as base64 kirbis; `/runfor:` bounds the
watch window and `/registry:` persists the interval across runs.
