---
name: "impacket-getst"
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

# impacket-getst

> Request a Kerberos service ticket for an SPN — including S4U tickets impersonating another user (constrained delegation abuse).

## What it's for

Kali packaging of `getST.py` from fortra/impacket. Given a password, NT
hash, AES key, or an existing TGT in `KRB5CCNAME`, it requests a service
ticket for one SPN and saves it as `<user>@<service>.ccache`. Its
distinctive capability is S4U: with `-impersonate` it runs
S4U2Self+S4U2Proxy to mint a service ticket *for another principal* as
long as the authenticating account is allowed to delegate to that SPN —
the primitive behind constrained-delegation and RBCD abuse.
`-altservice` rewrites the ticket's sname to a different service class.
For plain offline kerberoasting of user accounts, impacket-getuserspns
is the purpose-built tool; getst is for obtaining tickets you intend to
*use*.

## Typical use cases

### Request a service ticket for an SPN

```
impacket-getst -spn cifs/dc.example.com -dc-ip 198.51.100.10 example.com/user:'Password1'
```

Outcome: writes `user@cifs_dc.example.com.ccache`; export it with
`KRB5CCNAME=...` and consume it from any impacket script run with
`-k -no-pass` to act as the user against that service.

### Impersonate a user through S4U (constrained delegation)

```
impacket-getst -spn cifs/dc.example.com -impersonate administrator -hashes :NTHASH example.com/svc_web$
```

Outcome: performs S4U2Self+S4U2Proxy and saves
`administrator@cifs_dc.example.com.ccache` — a valid cifs ticket naming
the administrator, minted with only the service account's hash. It works
only if that account is allowed to delegate to the named SPN; add
`-self` to stop after S4U2Self (no proxy leg) when testing what
delegation the account actually holds.

### Chain from an existing TGT (Kerberos-only)

```
KRB5CCNAME=user.ccache impacket-getst -k -no-pass -impersonate 'dc$' -spn cifs/dc.example.com
```

Outcome: `-k` authenticates the S4U exchange with the cached ticket
(typically produced by impacket-gettgt) instead of any long-term secret,
keeping the whole chain ticket-based; target by FQDN hostname.

### Rewrite the ticket's service class with -altservice

```
impacket-getst -spn 'cifs/dc.example.com' -altservice 'http/dc.example.com' example.com/user:'Password1'
```

Outcome: the returned ticket carries the `http/` sname instead of
`cifs/`, so a single delegation grant to one service class yields a
usable ticket to another service on the same host.
