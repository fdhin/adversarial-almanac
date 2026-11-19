---
name: "ldapscripts"
categories:
  - blackarch-automation
version: "2.0.8"
homepage: "https://sourceforge.net/projects/ldapscripts"
packaged-by: [blackarch]
blackarch-builddate: "2024-01-31"
build-system: scripts
license: GPL2
meta-checked: "2026-11-09"
github-repo: "martymac/ldapscripts"
github-stars: 32
github-pushed: "2022-08-23"
github-archived: false
github-issues: 1
github-match: "search"
gh-checked: "2026-11-07"
worth: 2.1
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# ldapscripts

> Shell scripts to create and manage POSIX users, groups and machines in an LDAP directory.

## What it's for

ldapscripts is a set of shell wrappers around the OpenLDAP client tools (`ldapadd`, `ldapmodify`, `ldapdelete`, `ldapsearch`) for administering POSIX accounts in an LDAP directory. It was originally designed to be called from Samba's `smb.conf` add/delete user scripts so a Samba domain controller provisions its accounts into LDAP automatically, but the scripts work standalone too. It also ships query helpers (`ldapfinger`, `ldapid`, `ldapgid`, `lsldap`) and an `ldapinit` bootstrap. This is directory administration against a server you control — configure the server, suffix and bind credentials in `/etc/ldapscripts/ldapscripts.conf` (or point `LDAPSCRIPTS_CONF` elsewhere); it is not an enumeration or attack tool.

## Typical use cases

### Add a POSIX user

Provision a user account with the next free uidNumber.

```
ldapadduser jdoe users
```

Outcome: creates the posixAccount entry under the configured suffix, built from the user LDIF template; the entry shows up immediately in directory queries.

### Reset a user's password

Update the password attribute of an existing account.

```
ldapsetpasswd jdoe
```

Outcome: modifies the user's password in LDAP according to the crypt/policy settings in `ldapscripts.conf`.

### Inspect accounts and inventory the directory

Use the bundled query helpers.

```
ldapfinger jdoe
lsldap
```

Outcome: `ldapfinger` prints the full LDAP entry for a user, group or machine; `lsldap` performs a large recursive query from the directory root — a quick inventory of everything that exists. `ldapid`/`ldapgid` resolve a user or group to its numeric IDs.

### Bootstrap a fresh directory

Initialize the tree the other scripts write into.

```
ldapinit
```

Outcome: creates a minimal base structure in the LDAP directory so user, group and machine entries have a parent to live under.

### Wire Samba to auto-provision accounts

Hook the scripts into smb.conf so Windows-side account actions hit LDAP.

```
add user script = /usr/local/sbin/ldapadduser '%u' sambausers
add group script = /usr/local/sbin/ldapaddgroup '%g'
add user to group script = /usr/local/sbin/ldapaddusertogroup '%u' '%g'
delete user script = /usr/local/sbin/ldapdeleteuser '%u'
rename user script = /usr/local/sbin/ldaprenameuser '%uold' '%unew'
```

Outcome: when an account is created/renamed/deleted through Samba, the matching ldapscript applies the same change to the LDAP POSIX entry, keeping a Samba domain and its LDAP backend in sync.

*No corpus evidence — derived from upstream documentation.*
