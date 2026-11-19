---
name: "impacket-getuserspns"
categories:
  - corpus-windows
homepage: "https://github.com/fortra/impacket"
origin: "corpus"
github-repo: "fortra/impacket"
packaged-by: []
meta-checked: "2026-11-09"
github-commits-year: 169
gh-activity-checked: "2026-11-09"
worth: 6.5
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
---

# impacket-getuserspns

> List user accounts with SPNs and request their service tickets for offline cracking (Kerberoasting).

## What it's for

GetUserSPNs.py queries a domain controller over LDAP for user accounts that have Service Principal Names registered and, with `-request`, asks the KDC for a TGS for each of them. The TGS reply is encrypted with the service account's key, so the captured ciphertext can be cracked offline with John the Ripper or hashcat — any authenticated domain user can do this, and it works because service accounts often run with weak passwords. Without `-request` it is a pure listing tool. It is not silent (each request is visible in DC auditing), and it cannot roast an account that has no SPN registered — adding one to a user you control is a common prerequisite step.

## Typical use cases

### Kerberoast every SPN account into a hash file

Request TGS hashes for all SPN-bearing users and write them straight to a crackable file.

```
impacket-getuserspns example.com/user:Password1 -dc-ip 198.51.100.10 -request -outputfile kerberoast.txt
```

Outcome: prints the SPN account table, then writes one `$krb5tgs$…` line per account in John/hashcat format to kerberoast.txt (`-outputfile` implies `-request`); crack the file offline — any recovered plaintext is that service account's domain password.

### Target one account with -request-user

Fetch a ticket only for a single known user's SPN.

```
impacket-getuserspns example.com/user:Password1 -dc-ip 198.51.100.10 -request -request-user svc_mssql
```

Outcome: requests the TGS for the SPN(s) registered to `svc_mssql` only (just the username, no domain needed) and prints its hash to stdout — use when the high-value service account is already known and a single clean hash is wanted.

### Authenticate with a Kerberos ticket instead of a password

Run the same attack from a ccache, e.g. one obtained with getTGT.

```
KRB5CCNAME=user.ccache impacket-getuserspns example.com/user -dc-host dc01.example.com -k -request
```

Outcome: authenticates to the DC via Kerberos using the ticket in the ccache (`-k`) and dumps TGS hashes for all SPN users — no NTLM password is sent, which matters where only ticket-based auth is available.
