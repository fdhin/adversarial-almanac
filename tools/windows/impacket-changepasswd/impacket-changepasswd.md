---
name: "impacket-changepasswd"
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

# impacket-changepasswd

> Change or reset a user's or machine account's password over SAMR, kpasswd or LDAP.

## What it's for

changepasswd.py changes — or, with `-reset`/`-admin`, resets — the password of a user or machine account. The new secret can be a plaintext (`-newpass`) or a raw NT hash (`-newhashes`), the latter turning "I hold a hash" into "the account now has a key I chose", a standard persistence move. Authentication is flexible: the current password in the target string, the current NT hash (`-hashes`), Kerberos from a ccache (`-k`), or a different privileged account performing the change (`-altuser` with `-altpass`/`-althash`). Transport is chosen with `-protocol`/`-p`: smb-samr (default), rpc-samr, kpasswd or ldap.

## Typical use cases

### Change a password you already know

Convert credentials you hold into ones you control.

```
impacket-changepasswd example.com/user:'OldPass1!'@198.51.100.10 -newpass 'NewPass1!'
```

Outcome: performs a SAMR password change (which requires the current password, supplied in the target string) against the target host; on success the account logs in with NewPass1!.

### Change a password when you only have the NT hash

Pass-the-hash straight into a password change.

```
impacket-changepasswd -hashes :0123456789abcdef0123456789abcdef example.com/user@198.51.100.10 -newpass 'NewPass1!'
```

Outcome: authenticates as the target user with the NT hash (empty LM part) and changes the password to NewPass1! — useful right after dumping a hash that is awkward to reuse elsewhere.

### Rotate a machine account password over rpc-samr

Set a machine$ credential to one you know, choosing the RPC transport explicitly.

```
impacket-changepasswd -p rpc-samr 'example.com/PC01$':Password1!@198.51.100.10 -newpass 'NewPass1!'
```

Outcome: changes machine account PC01$'s password over DCE/RPC SAMR; the account is now usable with the new secret for delegation abuse — but the real host cannot authenticate until its secret is restored, so prefer machines you control.

### Set a chosen NT hash on an account using a Kerberos ticket

Admin ticket in hand, write a raw hash onto another principal.

```
KRB5CCNAME=admin.ccache impacket-changepasswd -k -no-pass -altuser admin@dc01.example.com -newhashes :0123456789abcdef0123456789abcdef 'example.com/PC01$:OldPass1!@dc01.example.com'
```

Outcome: authenticates as the `-altuser` identity from the ccache (`-k -no-pass`) and sets PC01$'s password to the supplied NT hash (LM part empty) — the account is now pass-the-hash-able with a hash you chose, no plaintext involved.
