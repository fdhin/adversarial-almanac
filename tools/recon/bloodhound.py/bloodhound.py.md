---
name: "bloodhound.py"
categories:
  - recon
version: "1.9.0-0parrot1"
homepage: "https://github.com/dirkjanm/bloodhound.py"
origin: "parrot"
recipe: "parrot-repo"
packaged-by: [parrot]
build-system: python
aliases: ["bloodhound-python"]
meta-checked: "2026-11-09"
github-repo: "dirkjanm/bloodhound.py"
github-stars: 2465
github-pushed: "2026-10-24"
github-archived: false
github-issues: 22
github-match: "exact"
gh-checked: "2026-11-07"
worth: 5.8
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 2
gh-activity-checked: "2026-11-09"
---

# bloodhound.py

> Python 3 ingestor for legacy BloodHound (4.2/4.3) — Impacket-based LDAP collection into graph-ready JSON, installed as `bloodhound-python`.

## What it's for

BloodHound.py is the remote Linux-side collector for the legacy 4.x [[bloodhound]] GUI: given any valid domain credential set (password, NT hash, AES key or Kerberos ticket) and LDAP reach to a domain controller, it enumerates users, computers, groups, ACLs, trusts, containers/GPOs, sessions and local admin memberships, then writes the JSON files (or a single zip) the GUI ingests. It talks LDAP and enumerates from the network — nothing runs on the targets, and only session/local-admin collection connects to member hosts at all. The console command is `bloodhound-python` (PyPI package `bloodhound`); the same repository's `bloodhound-ce` branch is a sibling tool, [[bloodhound-ce-python]], whose output targets BloodHound CE instead — the two generations' formats are not interchangeable, and only one of the two packages installs cleanly side by side without pipx/venvs. SharpHound remains the Windows-side counterpart.

## Typical use cases

### Collect everything with username and password

The standard full sweep before graph analysis:

```
bloodhound-python -d example.com -u user -p 'Password1' -ns 198.51.100.10 -c All --zip
```

Outcome: prints `INFO: BloodHound.py for BloodHound LEGACY (BloodHound 4.2 and 4.3)` on startup, then writes one `<timestamp>_bloodhound.zip` containing the users/computers/groups ACL and trust JSON — drag it onto the legacy BloodHound GUI. `-ns` is required whenever the target domain's DNS is not in your resolver, since the tool resolves the DC and all member hosts through it.

### Collect from the domain controller only

When touching member hosts is too loud or blocked:

```
bloodhound-python -d example.com -u user -p 'Password1' -ns 198.51.100.10 -c DCOnly --zip
```

Outcome: everything queryable over pure LDAP (groups, ACLs, trusts, object properties, containers/GPOs) with zero connections to member servers; no session or local-admin data.

### Pin a specific domain controller and global catalog

Override autodetection in multi-DC domains or when only one DC is reachable:

```
bloodhound-python -d example.com -u user -p 'Password1' -dc dc01.example.com -gc dc01.example.com -c All --zip
```

Outcome: LDAP queries go to the named DC and forest-wide lookups to the named global catalog instead of auto-resolved defaults.

### Authenticate with a Kerberos ticket

After exporting a ccache (e.g. from getTGT or a captured ticket):

```
export KRB5CCNAME=user.ccache
bloodhound-python -k -no-pass -u user -d example.com -dc dc01.example.com -c All --zip
```

Outcome: authentication via the Kerberos ccache instead of a password (`-no-pass` suppresses the prompt); requires the domain to resolve via `/etc/krb5.conf` and DNS, which is why `-dc`/`-ns` are usually passed alongside.

### Pass the hash

With only the NT hash of a valid account:

```
bloodhound-python -d example.com -u user --hashes :00000000000000000000000000000000 -ns 198.51.100.10 -c All --zip
```

Outcome: NTLM-authenticated collection using the hash in `LM:NT` form (empty LM segment); AES keys work the same way via `-aesKey hexkey`.

### Select specific collection methods

Trim the sweep to named methods, comma-separated:

```
bloodhound-python -d example.com -u user -p 'Password1' -ns 198.51.100.10 -c Group,LocalAdmin --zip
```

Outcome: only the requested datasets (here group memberships and local admin rights) are collected. Supported: Group, LocalAdmin, Session, Trusts, DCOM, RDP, PSRemote, LoggedOn (needs local admin on targets), Container, ObjectProps, ACL, DCOnly, Default, All (everything except LoggedOn) — method names are case-insensitive.

### Run from a source checkout without installing

```
git clone https://github.com/dirkjanm/BloodHound.py && cd BloodHound.py
python3 bloodhound.py -d example.com -u user -p 'Password1' -c All --zip
```

Outcome: same collection executed through the repo-root script (the installed console script and this entry point share one codebase), useful for pinning a version or reading the source; add `-v` for verbose output.
