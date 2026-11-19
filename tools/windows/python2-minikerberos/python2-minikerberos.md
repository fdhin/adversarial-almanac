---
name: "python2-minikerberos"
categories:
  - blackarch-windows
version: "17.e7e8d0a"
homepage: "https://github.com/skelsec/minikerberos"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-13"
meta-checked: "2026-11-09"
github-repo: "skelsec/minikerberos"
github-stars: 304
github-pushed: "2026-11-07"
github-archived: false
github-issues: 12
github-match: "exact"
gh-checked: "2026-11-07"
worth: 6.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 17
gh-activity-checked: "2026-11-09"
---

# python2-minikerberos

> skelsec's pure-Python Kerberos client library, shipping ready-made attack CLIs (getTGT, kerberoast, S4U, ticket converters) under the minikerberos- prefix.

## What it's for

minikerberos (by skelsec) is a Kerberos client library in pure Python — asynchronous and blocking clients with the same API — that serves as the Kerberos backend inside tools like pypykatz, aiosmb and msldap. It supports password, NT-hash, RC4 and AES-key secrets, CCACHE and KEYTAB files, SSPI on Windows, and PKINIT with PFX/PEM certificates; credentials are addressed through the "kerberos URL" syntax: `kerberos+<secret-type>://DOMAIN\user:secret@<dc-ip-or-host>`. Installing the package also places its example tools on PATH as `minikerberos-<tool>`: getTGT and getTGS, kerberoast (SPN roasting), asreproast, getNTPKInit (NT-hash recovery over PKINIT), getS4U2self and getS4U2proxy (delegation/RBCD), ccacheroast, ccache2kirbi / kirbi2ccache conversion, ccacheedit, keylist and a password-change utility. This vault entry tracks the Python 2 era package; current upstream requires Python 3.

## Typical use cases

### Fetch a TGT with a password

```
minikerberos-getTGT 'kerberos+password://EXAMPLE.COM\user:Password1@198.51.100.10' --ccache user.ccache
```

Outcome: authenticates to the KDC and writes the TGT to user.ccache (use --kirbi for kirbi format; with no output flag the ticket prints to stdout as kirbi).

### Fetch a TGT with a hash or key

Overpass-the-hash style, without a plaintext secret.

```
minikerberos-getTGT 'kerberos+nt://EXAMPLE.COM\user:<NTHASH>@198.51.100.10' --ccache user.ccache
```

Outcome: performs the same AS-REQ using the NT hash as the key material; the scheme also takes kerberos+rc4://, kerberos+aes://, an existing ticket via kerberos+ccache://, or a certificate via kerberos+pfx://...?certdata=cert.pfx for PKINIT.

### Kerberoast SPN accounts

```
minikerberos-kerberoast 'kerberos+password://EXAMPLE.COM\user:Password1@198.51.100.10' EXAMPLE.COM svc_sql
```

Outcome: requests service tickets for the named SPN accounts (or a file of usernames) and prints the roastable encrypted blobs; -e 23,17,18 selects encryption types and -o writes results to a file.

*No corpus evidence — derived from upstream documentation.*
