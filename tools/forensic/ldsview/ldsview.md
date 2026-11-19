---
name: "ldsview"
categories:
  - blackarch-forensic
version: "47.d8bfcaa"
homepage: "https://github.com/kgoins/ldsview"
packaged-by: [blackarch]
blackarch-builddate: "2021-05-17"
build-system: go
license: Apache
meta-checked: "2026-11-09"
github-repo: "kgoins/ldsview"
github-stars: 109
github-pushed: "2021-10-14"
github-archived: false
github-issues: 32
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.4
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# ldsview

> Offline search and triage of LDAP directory dumps in LDIF format — find entities, filter on attributes and decode AD flags without a live domain controller.

## What it's for

ldsview parses LDIF exports of an LDAP directory — typically a dump of an Active Directory database — and lets you search them offline. Use it during assessments or incident response to enumerate and filter users, computers and groups without touching a live domain controller. Besides searching, it decodes Active Directory user-account-control (UAC) bitmasks, translates raw directory timestamps into readable ones, and can generate the `ldapsearch` command that produces the LDIF in the first place. It works on files only; it never talks to a directory server itself.

## Typical use cases

### Generate the ldapsearch command to export an LDIF

Build the command that dumps a directory to a file ldsview can read.

```
ldsview cmdbuilder
```

Outcome: prompts for the directory host (FQDN or IP), domain DN, user and password, then prints a ready-to-run `ldapsearch` command; run it to produce the LDIF input for the other subcommands.

### Look up one entity in a dump

Fetch everything stored about a single account.

```
ldsview -f dump.ldif entity user
```

Outcome: prints the full attribute set of the entity named `user` (or a not-found message), read straight from the offline LDIF.

### Filter the dump on attributes

Find entities matching conditions across attributes.

```
ldsview -f dump.ldif search "adminCount:=1,sAMAccountName:!=krbtgt" --tdc
```

Outcome: lists every entity whose `adminCount` equals 1 except `krbtgt`, with directory timestamps translated to human-readable form (`--tdc`); add `-i` to restrict which attributes are printed. Filters combine as comma-separated `attribute:=value` and `attribute:!=value` terms.

### Decode a user-account-control value

Read a UAC bitmask found during analysis.

```
ldsview uac 532480
```

Outcome: prints the named account flags that the numeric UAC value represents, so you can tell disabled, locked, no-password-required and similar account states without a lookup table.

*No corpus evidence — derived from upstream documentation.*
