---
name: "sopa"
categories:
  - windows
homepage: "https://github.com/Macmod/sopa"
origin: "hacktricks"
recipe: "upstream"
packaged-by: []
build-system: go
license: see repository
meta-checked: "2026-11-13"
github-repo: "Macmod/sopa"
github-match: "exact"
github-stars: 64
gh-checked: "2026-11-13"
worth: 5.1
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-13"
---

# sopa

> Scriptable Golang client for Active Directory Web Services (ADWS): query, get, create, delete and edit AD objects over port 9389 instead of LDAP.

## What it's for

sopa (Macmod) implements the ADWS protocol stack (MS-NNS + MC-NMF + SOAP)
and exposes it as a clean command-line interface plus an interactive
shell. Everything LDAP clients do — searching with filters, fetching by
DN, creating/deleting objects, editing attributes, setting passwords,
group membership — works over the ADWS endpoint (TCP 9389) that
monitoring often ignores, with the same auth options as its sibling
TUI [[godap]] (password, NT hash, AES key, Kerberos ccache, PFX/PEM
certificates) plus SOCKS5 tunneling for pivoting. Use it when you want
replayable one-command AD operations rather than an interactive explorer,
or when the ADWS path is the point (evasion of LDAP-centric detection or
firewall allowlisting). Object writes are real operations against live
directory data.

*No corpus evidence — derived from upstream documentation.

## Typical use cases

### Query objects through the DC's own DNS

Search the directory with an LDAP filter over ADWS.

```
sopa query --dns 198.51.100.10 -d corp.example.com -u user -p 'Password1' --filter '(objectClass=user)'
```

Outcome: resolves and connects to a discovered DC and prints every object
matching the filter; `--dc dc01.corp.example.com` pins the DC instead of
SRV discovery. Passing `--dns` is recommended — the ADWS endpoint needs
FQDNs, and the DC's integrated DNS answers the PTR/SRV lookups sopa makes.

### Fetch one object by DN

Read a single account's full attribute set.

```
sopa get --dc dc01.corp.example.com --dns 198.51.100.10 -d corp.example.com -u user -p 'Password1' --dn 'CN=administrator,CN=Users,DC=corp,DC=example,DC=com'
```

Outcome: retrieves that object via WS-Transfer Get and prints its
attributes; combine with query results to drill into interesting
principals without re-scanning.

### Edit an attribute in place

The write primitive for targeted operations.

```
sopa attr replace --dc dc01.corp.example.com -d corp.example.com -u user -p 'Password1' --dn 'CN=user,CN=Users,DC=corp,DC=example,DC=com' --attr description --value 'audited'
```

Outcome: replaces the attribute's value over WS-Transfer Put; `attr add`
appends a value to multi-valued attributes and `attr delete` removes
values — the same verbs used for SPN writes, UAC toggles or RBCD blobs.

### Set an account password

Rotate or set credentials through MS-ADCAP.

```
sopa set-password --dc dc01.corp.example.com -d corp.example.com -u user -p 'Password1' --dn 'CN=target,CN=Users,DC=corp,DC=example,DC=com' --new 'NewPassword1!'
```

Outcome: sets the target account's password via the ADWS admin protocol
(`change-password` needs the old password); pair with `create computer`
(`--ou`, `--computer-pass`) and `attr` writes to build RBCD takeovers
entirely over ADWS.

### Enumerate group members and topology

Recon with the ADCAP helpers.

```
sopa members --dc dc01.corp.example.com -d corp.example.com -u user -p 'Password1' --dn 'CN=Domain Admins,CN=Users,DC=corp,DC=example,DC=com' --recursive
sopa info forest --dc dc01.corp.example.com -d corp.example.com -u user -p 'Password1'
```

Outcome: the first lists group members (optionally recursive via
GetADGroupMember); the second prints forest topology metadata — `info
version|domain|forest|dcs` cover the other levels, and `groups --dn ...`
shows a principal's memberships.

### Fingerprint the ADWS endpoint unauthenticated

Check exposure before committing credentials.

```
sopa mex --dc 198.51.100.10
```

Outcome: performs an unauthenticated WS-MetadataExchange request and
dumps the ADWS service endpoint metadata — a port-9389 reconnaissance
primitive requiring no auth flags.

### Work interactively over one connection

Reuse a session instead of authenticating per command.

```
sopa --dc dc01.corp.example.com -d corp.example.com -u user -p 'Password1'
```

Outcome: opens the interactive shell (tab completion, `help`), keeping a
single ADWS connection for all commands — `query --filter
'(objectClass=user)' --attrs sAMAccountName`, `get --dn ...`, `exit`.
SOCKS5 pivoting works in both modes with `-x 127.0.0.1:1080`.
