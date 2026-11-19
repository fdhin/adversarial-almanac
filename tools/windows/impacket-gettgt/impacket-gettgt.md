---
name: "impacket-gettgt"
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

# impacket-gettgt

> Request a Kerberos TGT for one principal with a password, NT hash or AES key and save it as a ccache file.

## What it's for

Kali packaging of `getTGT.py` from fortra/impacket. It sends a single
Kerberos AS-REQ to a domain controller for one principal and writes the
resulting Ticket Granting Ticket to `<user>.ccache` in the current
directory. Any impacket secret works: plaintext password, NT hash
(`-hashes`), AES key (`-aesKey`), or a client certificate via PKINIT.
The ccache output is consumed by exporting `KRB5CCNAME` and running other
impacket scripts with `-k`, so gettgt is normally the first step of a
Kerberos workflow — overpass-the-hash (hash to ticket), pass-the-key, or
pivoting through machine accounts. It only mints tickets; it does not
enumerate, crack, or parse them.

## Typical use cases

### Request a TGT with a password

```
impacket-gettgt example.com/user:'Password1' -dc-ip 198.51.100.10
```

Outcome: authenticates to the KDC, prints `Saving ticket in user.ccache`,
and writes the ccache to the current directory. `-dc-ip` pins the domain
controller directly — use it when the domain FQDN does not resolve from
the attack host or the DC is only reachable by address.

### Request a TGT with an NT hash (overpass-the-hash)

```
impacket-gettgt -hashes :NTHASH example.com/user
```

Outcome: keys the Kerberos exchange with the NT hash (RC4-HMAC) instead
of a password and writes `user.ccache` — the standard overpass-the-hash
step that turns a credential dump into a live Kerberos session. Works for
machine accounts the same way (`example.com/'web01$'` with the computer
object's hash).

### Request a TGT with an AES key

```
impacket-gettgt -aesKey <hex key> -dc-ip 198.51.100.10 example.com/user
```

Outcome: authenticates with AES (128/256-bit) key material and saves
`user.ccache`; this is the form to use on domains where RC4/NT-hash
Kerberos is disabled.

### Hand the ticket to other impacket scripts

```
KRB5CCNAME=user.ccache impacket-getst -k -no-pass -spn cifs/dc.example.com example.com/user
KRB5CCNAME=user.ccache impacket-smbclient -k -no-pass example.com/user@dc.example.com
```

Outcome: with `KRB5CCNAME` pointing at the ccache, downstream scripts run
with `-k -no-pass` authenticate from the ticket instead of a secret — no
password ever crosses the wire again. Target by FQDN hostname, not IP,
when using `-k`.
