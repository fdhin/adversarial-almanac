---
name: "impacket-getnpusers"
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

# impacket-getnpusers

> Find accounts with Kerberos pre-authentication disabled and dump their AS-REP hashes for offline cracking.

## What it's for

Kali packaging of `GetNPUsers.py` from fortra/impacket — the AS-REP
roasting tool. For any account with "Do not require Kerberos
preauthentication" set, the KDC hands out an AS-REP encrypted with the
account's key without proof of identity; this script collects those
encrypted blobs in hashcat/John format so their passwords can be cracked
offline. It operates in two modes: with a valid domain credential it
enumerates the affected accounts over LDAP, and with just a username
list it tries each name unauthenticated. Always pass `-no-pass` when you
have no password — otherwise the script's password prompt can increment
the target's `badpwdcount`. It is read-only recon/cracking prep; it
neither authenticates you nor executes anything.

## Typical use cases

### Roast a username list without credentials

```
impacket-getnpusers -no-pass -usersfile users.txt example.com/
```

Outcome: for every listed account that exists and has pre-auth disabled,
prints a `$krb5asrep$...` hash line (hashcat format by default) ready
for offline cracking — no password needed from you thanks to `-no-pass`.
Add `-format john` for John the Ripper format and `-outputfile
asrep.txt` to write the hashes to a file instead of stdout; crack them
to recover those accounts' passwords.

### Enumerate pre-auth-disabled accounts with a valid credential

```
impacket-getnpusers example.com/user:'Password1' -dc-ip 198.51.100.10 -request
```

Outcome: queries LDAP over the named DC and lists every account in the
domain with `UF_DONT_REQUIRE_PREAUTH` set; `-request` additionally dumps
each one's AS-REP hash in crackable format in the same run, so the full
roastable set is visible from a single authenticated session.
