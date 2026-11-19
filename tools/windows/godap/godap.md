---
name: "godap"
categories:
  - windows
homepage: "https://github.com/Macmod/godap"
origin: "hacktricks"
recipe: "upstream"
packaged-by: [aur]
build-system: go
license: see repository
meta-checked: "2026-11-13"
github-repo: "Macmod/godap"
github-match: "exact"
github-stars: 996
gh-checked: "2026-11-13"
worth: 5.8
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-13"
---

# godap

> Interactive terminal UI for LDAP/AD enumeration and object editing — DACL viewer/editor, ADIDNS, GPOs, Kerberos and certificate auth, all through one explorer.

## What it's for

godap (Macmod) is a Go TUI for exploring and editing Active Directory over
LDAP/LDAPS. It loads the directory tree on demand, renders
users/groups/computers with readable attribute formatting, and exposes
interactive editors for the things offensive work actually needs: a DACL
viewer/editor, an ADIDNS viewer/editor, a GPO viewer, an interactive
userAccountControl editor, group membership lookups, deleted-object
search, and JSON subtree export. Authentication covers password, NTLM
pass-the-hash, Kerberos (ccache/password/hash/AES key/PKINIT
certificate), PEM/PKCS#12 certificate binds, SOCKS proxying, and
StartTLS/LDAPS. Reach for it when you want AD exploration in a terminal
with edit rights, rather than one-shot scripted queries (for those, see
its sibling ADWS client [[sopa]], or the Python AD CS tooling cross-linked
from other notes). It edits live directory data — every write is real.

*No corpus evidence — derived from upstream documentation.

## Typical use cases

### Connect with a password

Open the explorer as a domain user.

```
godap dc01.corp.example.com -u user -p 'Password1' -d corp.example.com
```

Outcome: opens the TUI on the directory tree rooted automatically at the
domain; navigating loads objects on demand. `-u user@corp.example.com`
also works, and `--simple` forces a simple bind instead of NTLM.

### Connect with an NT hash (pass-the-hash)

Explore with only a hash.

```
godap dc01.corp.example.com -u user -H <nthash> -d corp.example.com
```

Outcome: same TUI over an NTLM-authenticated session — or use
`--hashfile` to read the hash from a file.

### Connect over Kerberos with a ccache

Reuse an existing ticket without credentials.

```
KRB5CCNAME=ticket.ccache godap dc01.corp.example.com -k -d corp.example.com
```

Outcome: binds using the ticket from `KRB5CCNAME`; `-k` also accepts
`-p`, `-H`, `--aes-key` or PKINIT certificate flags, or use the
interactive connection panel (`l`) to switch mechanisms at runtime.

### Bind with a certificate over LDAPS

Authenticate with PKI material.

```
godap dc01.corp.example.com --pfx cert.pfx -I -S
```

Outcome: performs a Schannel certificate bind; PEM works with
`--crt cert.pem --key cert.key`. `-S` starts in LDAPS and `-I` skips TLS
verification (StartTLS mid-session: `Ctrl + u`).

### Pivot through a SOCKS proxy

Reach the DC through a C2/port-forward proxy.

```
godap dc01.corp.example.com -u user -p 'Password1' -d corp.example.com -x socks5://127.0.0.1:1080
```

Outcome: routes the LDAP connection (and Kerberos KDC traffic) through
the SOCKS4/4a/5 proxy — useful against an agent pivot where direct LDAP
is not routable.

### Edit, inspect and export from the UI

The interactive surfaces that scripted tools lack.

```
godap UI: navigate to an object -> Enter to inspect
  l        connection settings (auth type, LDAPS, proxy)
  Ctrl+s   export the current subtree to JSON
```

Outcome: `l` opens the connection panel for live re-authentication
(simple/NTLM/Kerberos/certificate); `Ctrl+s` writes a JSON export of the
selected subtree (directory set by `--exportdir`); the per-object views
expose the DACL editor, userAccountControl toggles, group membership and
GPO viewers.
